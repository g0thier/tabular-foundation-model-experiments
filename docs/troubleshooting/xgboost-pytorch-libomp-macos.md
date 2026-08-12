# Crash du kernel Python avec XGBoost, PyTorch et scikit-learn sur macOS ARM64

## Symptômes

Dans un notebook Jupyter exécuté par VS Code sur un Mac Apple Silicon, le kernel Python disparaît brutalement pendant un entraînement XGBoost. Il n'y a généralement pas de traceback Python exploitable : macOS produit un rapport de crash natif contenant notamment :

```text
Exception Type: EXC_BAD_ACCESS (SIGSEGV)
Termination Reason: Segmentation fault: 11
Triggered by Thread: ...
libomp.dylib
libxgboost.dylib
XGQuantileDMatrixCreateFromCallback
```

Cas observé avec :

```text
macOS ARM64 (Apple Silicon)
Python 3.13.5
scikit-learn 1.8.0
TabICL 2.1.1
PyTorch 2.13.0
XGBoost 3.4.0
Homebrew libomp 22.1.8
```

Les numéros de version ci-dessus décrivent le cas constaté ; le problème peut exister avec d'autres versions.

## Cause

Le processus Python charge plusieurs implémentations distinctes du runtime OpenMP `libomp.dylib` :

```text
scikit-learn -> site-packages/sklearn/.dylibs/libomp.dylib
TabICL -> PyTorch -> site-packages/torch/lib/libomp.dylib
XGBoost -> /opt/homebrew/.../libomp.dylib
```

Il ne s'agit pas d'un double `import` Python. Chaque paquet natif charge indirectement sa propre bibliothèque OpenMP. Leur coexistence peut corrompre l'état interne d'OpenMP ou provoquer un crash lors de la synchronisation de ses threads.

Dans ce cas, `from tabicl import TabICLClassifier` charge PyTorch indirectement. Le crash a eu lieu dans `libomp` pendant la création d'une matrice XGBoost.

## Diagnostic

### 1. Relever les versions et l'interpréteur

Dans le terminal utilisé par le projet :

```bash
python3 -c "
import sys, torch, sklearn, xgboost, tabicl
print('Python      :', sys.version)
print('Executable  :', sys.executable)
print('Torch       :', torch.__version__)
print('scikit-learn:', sklearn.__version__)
print('XGBoost     :', xgboost.__version__)
print('TabICL      :', getattr(tabicl, '__version__', 'inconnue'))
"

brew list --versions libomp
python3 -m pip show torch scikit-learn xgboost tabicl
```

Vérifier que le terminal et le kernel VS Code utilisent le même `sys.executable`.

### 2. Lister les runtimes OpenMP chargés

Dans un kernel fraîchement redémarré, après les imports problématiques :

```python
import os
import sklearn
import torch
import xgboost

os.system(
    f"vmmap {os.getpid()} "
    "| grep '/libomp.dylib' "
    "| grep '__TEXT'"
)
```

Plusieurs chemins différents confirment que plusieurs images de `libomp` sont chargées.

### 3. Examiner la dépendance de XGBoost

```bash
XGB_LIB="$(python3 -c 'from pathlib import Path; import xgboost; print(Path(xgboost.__file__).parent / "lib/libxgboost.dylib")')"
otool -L "$XGB_LIB" | grep libomp
```

Avant correction, le cas observé affichait :

```text
@rpath/libomp.dylib
```

Le chargeur dynamique résolvait ce chemin vers la copie Homebrew, alors que PyTorch avait déjà chargé sa propre copie.

## Correctif validé dans ce cas

Le correctif consiste à modifier **la copie de `libxgboost.dylib` installée dans l'environnement Python concerné**, afin que XGBoost référence explicitement le même `libomp.dylib` que PyTorch.

> Avertissement : il s'agit d'un patch local d'un binaire installé. L'appliquer d'abord dans un environnement virtuel est préférable. Une mise à jour ou une réinstallation de XGBoost annulera le patch. La compatibilité ABI n'est pas garantie pour toutes les combinaisons de versions.

### 1. Définir les chemins automatiquement

Activer exactement l'environnement utilisé par le notebook, puis exécuter :

```bash
TORCH_OMP="$(python3 -c 'from pathlib import Path; import torch; print(Path(torch.__file__).parent / "lib/libomp.dylib")')"
XGB_LIB="$(python3 -c 'from pathlib import Path; import xgboost; print(Path(xgboost.__file__).parent / "lib/libxgboost.dylib")')"

test -f "$TORCH_OMP" || { echo "libomp de PyTorch introuvable"; exit 1; }
test -f "$XGB_LIB" || { echo "libxgboost.dylib introuvable"; exit 1; }

printf '%s\n' "$TORCH_OMP" "$XGB_LIB"
```

### 2. Vérifier la dépendance avant modification

```bash
otool -L "$XGB_LIB" | grep libomp
```

La commande suivante suppose que la sortie contient exactement `@rpath/libomp.dylib`. Si ce n'est pas le cas, ne pas l'appliquer telle quelle.

### 3. Sauvegarder et modifier le lien

```bash
cp -p "$XGB_LIB" "${XGB_LIB}.backup"

install_name_tool \
  -change @rpath/libomp.dylib \
  "$TORCH_OMP" \
  "$XGB_LIB"

codesign --force --sign - "$XGB_LIB"
```

La signature ad hoc est nécessaire parce que `install_name_tool` modifie le binaire Mach-O.

### 4. Vérifier le résultat

```bash
otool -L "$XGB_LIB" | grep libomp
```

La sortie doit maintenant contenir le chemin absolu vers :

```text
.../site-packages/torch/lib/libomp.dylib
```

Fermer complètement VS Code (`Cmd+Q`) afin de tuer l'ancien processus du kernel, puis le relancer.

## Test de validation

Dans un nouveau kernel :

```python
import os
import numpy as np
import sklearn
import torch
from tabicl import TabICLClassifier
from xgboost import XGBClassifier

print("Torch:", torch.__version__)

X = np.random.default_rng(42).normal(size=(10_000, 20))
y = (X[:, 0] + X[:, 1] > 0).astype(int)

model = XGBClassifier(
    n_estimators=100,
    n_jobs=-1,
    random_state=42,
)
model.fit(X, y)

print("Entraînement XGBoost terminé")
os.system(
    f"vmmap {os.getpid()} "
    "| grep '/libomp.dylib' "
    "| grep '__TEXT'"
)
```

Critères de réussite :

- l'import de TabICL/PyTorch et de XGBoost réussit ;
- l'entraînement XGBoost termine sans disparition du kernel ;
- aucune copie Homebrew de `libomp` n'est chargée par XGBoost ;
- le rapport `otool` de `libxgboost.dylib` pointe vers la copie de PyTorch.

scikit-learn peut toujours charger sa propre copie de `libomp`. Dans le cas documenté, le rattachement de XGBoost à celle de PyTorch a suffi à supprimer le crash, mais cela ne démontre pas que toutes les combinaisons avec deux runtimes restants sont sûres.

## Retour arrière

Dans le même environnement :

```bash
TORCH_OMP="$(python3 -c 'from pathlib import Path; import torch; print(Path(torch.__file__).parent / "lib/libomp.dylib")')"
XGB_LIB="$(python3 -c 'from pathlib import Path; import xgboost; print(Path(xgboost.__file__).parent / "lib/libxgboost.dylib")')"

test -f "${XGB_LIB}.backup" || { echo "Sauvegarde introuvable"; exit 1; }
cp -p "${XGB_LIB}.backup" "$XGB_LIB"
codesign --force --sign - "$XGB_LIB"
otool -L "$XGB_LIB" | grep libomp
```

Fermer ensuite complètement VS Code et relancer le kernel.

Une réinstallation forcée de XGBoost restaure également le binaire officiel :

```bash
python3 -m pip install --force-reinstall --no-cache-dir "xgboost==3.4.0"
```

Adapter le numéro à la version souhaitée.

## Cas particulier : XGBoost ne s'importe plus après suppression de Homebrew `libomp`

Erreur typique :

```text
Library not loaded: @rpath/libomp.dylib
tried: /opt/homebrew/opt/libomp/lib/libomp.dylib (no such file)
```

Réinstaller d'abord la dépendance :

```bash
brew install libomp
```

Redémarrer ensuite complètement le kernel. Supprimer `libomp` avec `brew uninstall --ignore-dependencies` ne corrige pas le conflit : cela empêche simplement la wheel macOS de XGBoost de se charger avant l'application du patch.

## Alternatives plus maintenables

Pour un projet partagé ou une CI, préférer :

1. un environnement Python isolé et verrouillé (`venv`, Conda ou uv) ;
2. des versions précises dans un fichier de verrouillage ;
3. si possible, des paquets natifs provenant d'une chaîne de distribution cohérente ;
4. des processus séparés pour TabICL/PyTorch et XGBoost si l'unification d'OpenMP n'est pas fiable.

Limiter les threads peut atténuer le problème, sans supprimer les runtimes multiples :

```python
import os

os.environ["OMP_NUM_THREADS"] = "1"        # avant les imports natifs
os.environ["MKL_NUM_THREADS"] = "1"
os.environ["VECLIB_MAXIMUM_THREADS"] = "1"
```

et :

```python
XGBClassifier(n_jobs=1)
```

Ne pas utiliser `KMP_DUPLICATE_LIB_OK=TRUE` comme correction : cette variable masque certains contrôles sans rendre plusieurs runtimes sûrs.

## Reproductibilité et maintenance

Après validation, conserver les versions :

```bash
python3 -m pip freeze > requirements-working.txt
brew list --versions libomp >> requirements-working.txt
```

Après toute mise à jour de XGBoost, contrôler si le patch existe encore :

```bash
XGB_LIB="$(python3 -c 'from pathlib import Path; import xgboost; print(Path(xgboost.__file__).parent / "lib/libxgboost.dylib")')"
otool -L "$XGB_LIB" | grep libomp
```

Documenter également l'architecture (`arm64`), la version de macOS, la version de Python et le chemin de `sys.executable` dans tout rapport de bug.

## Résumé

Le crash n'était ni un plantage du kernel macOS ni une exception Python. Il provenait d'un crash natif dans OpenMP alors que scikit-learn, PyTorch et XGBoost avaient chargé trois copies différentes de `libomp.dylib`. Dans l'environnement observé, faire pointer `libxgboost.dylib` vers la copie OpenMP de PyTorch, puis signer de nouveau le binaire et redémarrer totalement VS Code, a supprimé le crash.

# Tabular Foundation Model Experiments

## Description

This project explores how a [Tabular Foundation Model](https://github.com/soda-inria/tabicl) can be used on classic supervised learning tasks. It contains two notebook-based experiments: one for classification and one for regression.

![Capture](/docs/images/Picture.jpg)
Illustrative photo by <a href="https://unsplash.com/fr/@adrian_trinkaus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Adrian Trinkaus</a> on <a href="https://unsplash.com/fr/photos/photo-de-toles-ondulees-45GmPkD806w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>

## Table of Contents

- [Tabular Foundation Model Experiments](#tabular-foundation-model-experiments)
  - [Description](#description)
  - [Table of Contents](#table-of-contents)
  - [🎯 Objective of the project](#-objective-of-the-project)
  - [👥 Target audience](#-target-audience)
  - [🗂️ Repository structure](#️-repository-structure)
  - [🚀 Quick start](#-quick-start)
  - [🐍 Notebooks](#-notebooks)
  - [🥽 Security](#-security)
  - [📰 Changelog](#-changelog)
  - [🩷 Acknowledgements](#-acknowledgements)
    - [Environnement](#environnement)
  - [🧪 Project Status](#-project-status)
  - [🔒 License](#-license)
  - [🤝 Contributing](#-contributing)
  - [👤 Author](#-author)

## 🎯 Objective of the project

The goal is to test a Tabular Foundation Model on two common tabular machine learning settings:

- Classification, where the model predicts a discrete target class.
- Regression, where the model predicts a continuous numerical target.

The notebooks are intended to make the workflow easy to inspect, reproduce, and adapt for further experiments.

## 👥 Target audience

- Python developers
- Data science students
- Machine learning practitioners interested in foundation models for tabular data

## 🗂️ Repository structure

```text
tabular-foundation-model-experiments/
├── docs/
│   └── images/
├── notebooks/
│   ├── classification_TFM.ipynb
│   └── regression_TFM.ipynb
├── ACKNOWLEDGEMENTS.md
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE.md
├── README.md
└── SECURITY.md
```

## 🚀 Quick start

1. Clone the repository.
2. Open the `notebooks/` directory.
3. Launch the notebook you want to inspect with JupyterLab, VS Code, or another notebook-compatible environment.
4. Run the cells in order to reproduce the experiment.

## 🐍 Notebooks

The `notebooks/` directory contains two Tabular Foundation Model experiments:

- `classification_TFM.ipynb`: tests a Tabular Foundation Model on a classification task and evaluates its ability to predict categorical labels from tabular features.
- `regression_TFM.ipynb`: tests a Tabular Foundation Model on a regression task and evaluates its ability to predict continuous values from tabular features.

These notebooks provide a compact comparison of how the same model family behaves across two different supervised learning problem types.

## 🥽 Security

- See [SECURITY.md](/SECURITY.md) for vulnerability reporting guidelines.

## 📰 Changelog

Track all notable project changes in [CHANGELOG.md](/CHANGELOG.md).

Recommended:
- Follow a consistent format such as Keep a Changelog
- Create an entry for each release
- Include Added, Changed, Fixed, and Removed sections when relevant

## 🩷 Acknowledgements

- Use [ACKNOWLEDGEMENTS.md](/ACKNOWLEDGEMENTS.md) to credit people, tools, libraries, and communities that helped the project.

### Environnement

- **Python ≥ 3.13.5**
- Dependencies listed in [requirements.txt](/notebooks/requirements.txt)

## 🧪 Project Status

- 🔬 **Statut** : experimental
- 🧭 **Roadmap** : to be defined

## 🔒 License

- See [LICENSE.md](/LICENSE.md).

## 🤝 Contributing

Contributions are welcome.
- See [CONTRIBUTING.md](/CONTRIBUTING.md)
- Code of conduct available in [CODE_OF_CONDUCT.md](/CODE_OF_CONDUCT.md).

## 👤 Author

Gauthier Rammault

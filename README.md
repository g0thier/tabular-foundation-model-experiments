# Tabular Foundation Model Experiments

## Description

This project explores how a [Tabular Foundation Model](https://github.com/soda-inria/tabicl) can be used on classic supervised learning tasks and forecasting. It contains three notebook-based experiments: one for classification, one for regression, and one for time series forecasting.

![Capture](/docs/images/Picture.jpg)

Illustrative photo by <a href="https://unsplash.com/fr/@adrian_trinkaus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Adrian Trinkaus</a> on <a href="https://unsplash.com/fr/photos/photo-de-toles-ondulees-45GmPkD806w?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>.

## Table of Contents

- [Tabular Foundation Model Experiments](#tabular-foundation-model-experiments)
  - [Description](#description)
  - [Table of Contents](#table-of-contents)
  - [🎯 Objective of the project](#-objective-of-the-project)
  - [👥 Target audience](#-target-audience)
  - [🗂️ Repository structure](#️-repository-structure)
  - [🚀 Quick start](#-quick-start)
  - [🐍 Environment](#-environment)
  - [🐍 Notebooks](#-notebooks)
  - [🥽 Security](#-security)
  - [📰 Changelog](#-changelog)
  - [🩷 Acknowledgements](#-acknowledgements)
  - [🧪 Project Status](#-project-status)
  - [🔒 License](#-license)
  - [🤝 Contributing](#-contributing)
  - [👤 Author](#-author)

## 🎯 Objective of the project

The goal is to test a Tabular Foundation Model on common tabular machine learning settings:

- Classification, where the model predicts a discrete target class.
- Regression, where the model predicts a continuous numerical target.
- Forecasting, where the model predicts future values from time series data.

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
│   ├── forecasting_TFM.ipynb
│   └── regression_TFM.ipynb
├── ACKNOWLEDGEMENTS.md
├── CHANGELOG.md
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md
├── LICENSE.md
├── README.md
├── requirements.txt
└── SECURITY.md
```

## 🚀 Quick start

1. Clone the repository.
2. Install the dependencies:

```bash
pip install -r requirements.txt
```

3. Open the `notebooks/` directory.
4. Launch the notebook you want to inspect with JupyterLab, VS Code, or another notebook-compatible environment.
5. Run the cells in order to reproduce the experiment.

## 🐍 Environment

- **Python ≥ 3.13.5**
- Dependencies are listed in [requirements.txt](/requirements.txt).

## 🐍 Notebooks

The `notebooks/` directory contains three Tabular Foundation Model experiments:

- `classification_TFM.ipynb`: uses a KaggleHub-loaded Titanic dataset to test TabICL on a classification task and evaluate categorical label predictions from tabular features.
- `forecasting_TFM.ipynb`: uses a KaggleHub-loaded Air Passengers dataset to test TabICL on a time series forecasting task and compare its predictions with SARIMAX and Prophet baselines.
- `regression_TFM.ipynb`: tests TabICL on a regression task and evaluates continuous value predictions from tabular features.

The notebooks include baseline comparisons adapted to each task:

- Classification metrics: accuracy, precision, recall, and F1 score.
- Regression metrics: MSE, RMSE, MAE, and R².
- Forecasting metrics: MAE, RMSE, and SMAPE.

These comparisons are compact experiment baselines, not exhaustive benchmarks.

## 🥽 Security

- See [SECURITY.md](/SECURITY.md) for vulnerability reporting guidelines.

## 📰 Changelog

Track all notable project changes in [CHANGELOG.md](/CHANGELOG.md).

## 🩷 Acknowledgements

- Use [ACKNOWLEDGEMENTS.md](/ACKNOWLEDGEMENTS.md) to credit people, tools, libraries, and communities that helped the project.

## 🧪 Project Status

- 🔬 **Status**: experimental
- 🧭 **Roadmap**: to be defined

## 🔒 License

- See [LICENSE.md](/LICENSE.md).

## 🤝 Contributing

Contributions are welcome.
- See [CONTRIBUTING.md](/CONTRIBUTING.md)
- Code of conduct available in [CODE_OF_CONDUCT.md](/CODE_OF_CONDUCT.md).

## 👤 Author

Gauthier Rammault

# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]
### Added
- Forecasting experiment notebook using the Air Passengers dataset to evaluate TabICL on a time series forecasting task.
- Baseline comparison for forecasting against SARIMAX and Prophet models.
- Forecasting evaluation metrics: MAE, RMSE, and SMAPE.
- Forecasting dependencies: Prophet and statsmodels.

### Changed
- Moved notebook dependencies to root `requirements.txt`.

## [1.0.0]
### Added
- Initial public version of the project.
- Classification experiment notebook for evaluating TabICL on a supervised tabular classification task.
- Regression experiment notebook for evaluating TabICL on a supervised tabular regression task.
- Benchmark comparison against XGBoost and Random Forest models in both notebooks.
- Project README describing the objective, repository structure, notebook workflow, and setup guidance.
- Notebook dependency setup in `notebooks/requirements.txt`.

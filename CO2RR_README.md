# CO2RR.ipynb README

## Overview

This notebook builds machine-learning models to study structure-property relationships between N-containing molecular modifiers on Cu and their CO2RR product selectivities.

The workflow is divided into two stages:

1. `Cell 1`: data processing, descriptor generation, and feature selection
2. `Cell 2`: predictive model comparison based on the saved feature subsets from Cell 1

The notebook is designed for the dataset in:

`/home/sy/SY/N-体系/FE clac.xlsx`


## Modeling Targets

The following Faradaic efficiency targets are modeled independently:

- `FEaverage H2`
- `FEaverage CO`
- `FEaverage CH4`
- `FEaverage C2H4`
- `FE HCOO- `
- `FE CH3COO- `
- `FE CH3CH2OH `
- `FE n-Propanol`
- `FE C2+`

In this notebook, two target names use the anionic form in the column header but should be interpreted as the corresponding acid products in the discussion:

- `FE HCOO- ` represents **formic acid (`HCOOH`)**
- `FE CH3COO- ` represents **acetic acid (`CH3COOH`)**


## Data Processing Rule

For each molecule, duplicate measurements at different potentials are first organized at the sample level.

For the targets:

- `FEaverage H2`
- `FEaverage CO`
- `FEaverage CH4`
- `FEaverage C2H4`

the modeling target is defined as the **maximum FE value across the two measured potentials** for the same molecule.

For example, if one SMILES has `FEaverage H2 = 24` at one potential and `33` at another potential, the retained sample-level target is `33`.


## Molecular Representation

The notebook uses SMILES strings to generate molecular descriptors. Depending on the current notebook version, descriptors may include:

- RDKit-based physicochemical descriptors
- fragment descriptors
- Morgan fingerprint bits
- manually designed chemistry-related descriptors

Morgan bits are post-processed into more readable fragment-like labels whenever possible.


## Stage 1: Feature Selection

`Cell 1` performs:

1. loading and cleaning the Excel dataset
2. aggregating repeated potential entries to sample-level maxima
3. building the descriptor matrix
4. filtering invalid or redundant descriptors
5. running stepwise feature selection separately for each target
6. saving the best feature subset for each selector model

Typical selector models may include:

- `ridge`
- `lasso`
- `elastic_net`
- `pls`
- `svr_rbf`

The exact selector set depends on the notebook version you are running.


## Stage 2: Predictive Model Comparison

`Cell 2` reads the saved feature subsets from Stage 1 and then:

1. takes each target-specific feature subset
2. uses nested or repeated cross-validation logic depending on the notebook version
3. compares multiple predictive models
4. tunes hyperparameters with Optuna
5. chooses the best predictive model for each target and selector-model feature subset
6. exports prediction tables, SHAP-based interpretation results, and figures

Typical predictive models may include:

- `ridge`
- `lasso`
- `elastic_net`
- `pls`
- `svr_rbf`
- `knn`
- `random_forest`
- `extra_trees`
- `gradient_boosting`
- `adaboost`
- `xgboost`
- `lightgbm`
- `catboost`

The actual available model list depends on which packages are installed in the active environment.


## Main Outputs

### Stage 1 outputs

Common Stage 1 outputs include files such as:

- `cell1_descriptor_matrix.csv`
- `cell1_selector_feature_catalog.csv`
- `cell1_feature_selection_steps.csv`
- target-specific feature-selection summaries and plots

These files are used as the input for Stage 2.


### Stage 2 outputs

The main Stage 2 output directory is typically:

`.../outputs/cell2_model_comparison_from_cell1`

or in the newer dated version:

`.../outputs/cell2_model_comparison_from_cell1-20260612`

Main exported tables:

- `cell2_predictive_model_performance.csv`
  - summary of the best predictive model under each target and selector model
- `cell2_all_model_comparisons.csv`
  - all tested predictive models and their CV performance
- `cell2_predictions.csv`
  - sample-level observed vs predicted values from outer test folds
- `cell2_importance.csv`
  - SHAP-based mean absolute importance values
- `cell2_shap_values.csv`
  - exported per-sample SHAP values and matched feature values
- `cell2_predictive_model_results.xlsx`
  - Excel workbook containing the major result tables
- `cell2_predictive_model_summary.md`
  - brief markdown summary of the best-performing models


### Figure outputs

The notebook also exports four figure groups:

- `shap_plots/`
- `fit_plots/`
- `residual_plots/`
- `importance_plots/`

For each target-selector-best_model combination, the notebook typically writes:

- one SHAP summary plot
- one observed-vs-predicted fit plot
- one residual plot
- one feature-importance plot

Example file name:

`FE_C2plus_xgboost_gradient_boosting_fit.png`

which means:

- target: `FE C2+`
- selector model: `xgboost`
- best predictive model: `gradient_boosting`
- plot type: fit plot


## Performance Metrics

The exported model results may include:

- `R²`
- `RMSE`
- `MAE`
- inner-CV metrics
- outer-test metrics

In the current plotting style, the fit plot usually displays:

- `R²`
- `MAE`


## Required Python Packages

Core packages:

- `pandas`
- `numpy`
- `matplotlib`
- `scikit-learn`
- `optuna`
- `rdkit`
- `shap`
- `openpyxl`

Optional model packages:

- `xgboost`
- `lightgbm`
- `catboost`


## Environment Notes

1. `shap` may fail to import if the current environment has a broken `torch` installation.
2. If `xgboost`, `lightgbm`, or `catboost` are not installed, those models will be skipped automatically in the versions that use guarded imports.
3. The notebook is intended to run on a Linux server environment and is configured to use multi-core CV where possible.


## Suggested Usage

1. Run `Cell 1` first to generate the cleaned descriptor matrix and selected feature subsets.
2. Confirm that the Stage 1 output files were written successfully.
3. Run `Cell 2` to compare predictive models and generate interpretation plots.
4. Use:
   - `cell2_predictive_model_performance.csv`
   - `cell2_predictions.csv`
   - `cell2_importance.csv`
   - `cell2_shap_values.csv`
   for downstream analysis and figure preparation.


## Notes for Manuscript Preparation

- Use the fit plot for predictive performance presentation.
- Use the SHAP summary plot for feature-effect interpretation.
- Use the importance plot when a simpler ranking-style figure is needed.
- If publication-quality restyling is needed, the exported CSV files can be reused to redraw fit and importance figures without rerunning the full modeling workflow.

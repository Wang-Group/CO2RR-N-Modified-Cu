# CO2RR-N-Modified-Cu
This repository contains two Jupyter notebooks for analyzing nitrogen-containing molecular modifiers used in Cu-catalyzed CO2 electroreduction. The notebooks can be run independently, except that the two cells in `CO2RR.ipynb` must be run in order.

| Notebook | What it does | Details |
| --- | --- | --- |
| [`map.ipynb`](map.ipynb) | Places tested modifiers and a commercial N-containing molecule library on a joint Morgan-fingerprint/t-SNE map; summarizes three structural clusters. | [`map_README.md`](map_README.md) |
| [`CO2RR.ipynb`](CO2RR.ipynb) | Builds molecular descriptors, selects features, compares regression models for product Faradaic efficiencies, and exports SHAP analyses. | [`CO2RR_README.md`](CO2RR_README.md) |

## Input data

- **Both notebooks:** `FE clac.xlsx`, sheet `total`, containing molecular identifiers, SMILES, screening-round information, and CO2RR measurements. `map.ipynb` reads `No`, `CAS No.`, `SMILES`, and the available `Round 1`-`Round 5` columns; `CO2RR.ipynb` also reads potentials and product Faradaic efficiencies.
- **`map.ipynb` only:** `mcule_instock_n_containing_organic_molecules.csv`, a commercial molecular library with a `smiles` column.

The input paths are currently configured for the original Linux project directory. Set the path variables near the start of each notebook to the locations of your own files before running them. The large commercial-library CSV may need to be obtained separately rather than stored in a standard GitHub repository.

## Main outputs

- **`map.ipynb`:** `commercial_plus_co2rr_n_map_revised.png`, combined and source-specific t-SNE coordinate CSVs, and cluster feature/representative-molecule summaries. See [`map_README.md`](map_README.md) for the complete file list.
- **`CO2RR.ipynb`, cell 1:** `cell1_feature_selection/`, including `cell1_descriptor_matrix.csv`, `cell1_selector_feature_catalog.csv`, feature-selection tables, and plots.
- **`CO2RR.ipynb`, cell 2:** `cell2_model_comparison_from_cell1-20260612/`, including model-performance and prediction tables, SHAP values, a results workbook, and fit/SHAP/importance figures. Cell 2 reads the files produced by cell 1. See [`CO2RR_README.md`](CO2RR_README.md) for details.

All outputs are written below the output directories configured in the notebooks. The chemical-space map uses structural fingerprints, not Faradaic efficiencies; its coordinates should not be read as performance values.

## Runtime environment

Use Python 3 with Jupyter Notebook or JupyterLab. Both notebooks require `numpy`, `pandas`, `matplotlib`, `rdkit`, `scikit-learn`, and `openpyxl`. `CO2RR.ipynb` additionally requires `scipy`, `optuna`, and `shap`; `xgboost`, `lightgbm`, and `catboost` enable additional models when installed. The modeling notebook is configured for parallel cross-validation (`CV_N_JOBS = 30`), so reduce this setting on smaller machines.

No environment lockfile is provided; package versions should be recorded when reproducing published results.

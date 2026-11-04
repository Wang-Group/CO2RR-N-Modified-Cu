# Chemical Space Map of N-Containing CO2RR Modifiers

`map.ipynb` visualizes the chemical space covered by experimentally tested nitrogen-containing modifiers alongside a commercial library of N-containing organic molecules. It generates a joint t-SNE map, cluster summaries, and coordinate tables.

The map is based on **molecular structure only**. Faradaic efficiencies are not used for clustering or t-SNE; proximity on the map does not imply similar catalytic performance.

## Requirements

- Python 3 and Jupyter Notebook or JupyterLab
- `numpy`, `pandas`, `matplotlib`, `rdkit`, `scikit-learn`, and `openpyxl`

Install these packages in a compatible Python environment before running the notebook. The notebook consists of one executable code cell.

## Input files

| File | Required fields | Purpose |
| --- | --- | --- |
| `FE clac.xlsx`, sheet `total` | `No`, `CAS No.`, `SMILES` | Experimentally tested CO2RR modifiers |
| `mcule_instock_n_containing_organic_molecules.csv` | `smiles` | Commercial-library background |

The Excel sheet may also contain `Round 1` through `Round 5` indicator columns. The code reads the first indicator equal to `1` in column order and uses this mapping for plot labels:

| Excel column | Plot label |
| --- | --- |
| `Round 1` or `Round 2` | `Initial dataset` |
| `Round 3` | `Round 1` |
| `Round 4` | `Round 2` |
| `Round 5` | `Round 3` |

Rows without a matching indicator are labeled `Unassigned`. Check this mapping if the round definitions in your Excel file differ.

## Configuration and execution

At the beginning of `map.ipynb`, set `ROOT`, `EXCEL_FILE`, `COMMERCIAL_LIB`, and `OUT_DIR` to the locations of your input and output files. The current notebook contains a machine-specific Linux `ROOT` path; it must be changed when running elsewhere. For example:

```python
ROOT = Path("/path/to/project")
EXCEL_FILE = ROOT / "FE clac.xlsx"
COMMERCIAL_LIB = ROOT / "commercial_n_library" / "mcule_instock_n_containing_organic_molecules.csv"
OUT_DIR = ROOT / "ML" / "outputs" / "commercial_plus_co2rr_n_map_revised"
```

Open the notebook and run its code cell from the top. The output directory is created automatically. Key settings are `MAX_COMMERCIAL = 5000`, `FP_RADIUS = 2`, `FP_NBITS = 256`, and `N_CLUSTERS = 3`.

## Processing and interpretation

1. The commercial library is deduplicated by its original `smiles` strings. If it has more than 5,000 rows, 5,000 are sampled with a fixed random seed **before** invalid structures are removed.
2. The Excel columns used for identifiers, structures, and round labels are forward-filled. Experimental rows are deduplicated by `No`, keeping the first occurrence.
3. Both sources are filtered to valid SMILES containing at least one carbon and one nitrogen atom. The two sources are not deduplicated against each other.
4. RDKit generates 256-bit, radius-2 Morgan fingerprints and structural feature flags. K-means clusters the combined fingerprints into three groups. The `Class 1`-`Class 3` numbers are assigned in descending order of cluster size, not predefined chemical identities.
5. t-SNE projects the combined fingerprints into two dimensions. The commercial molecules are colored by cluster; tested modifiers are overlaid by screening round. A small seeded jitter is applied **only to the plotted modifier markers** to reduce overlap. Exported coordinates are not jittered.

t-SNE axes and absolute distances should not be interpreted as quantitative chemical or performance metrics. Cluster descriptions summarize prevalent structural features, not catalytic mechanisms.

## Outputs

All outputs are written to `OUT_DIR`:

| File | Contents |
| --- | --- |
| `commercial_plus_co2rr_n_map_revised.png` | Joint t-SNE figure |
| `combined_tsne_coordinates.csv` | Coordinates and annotations for both sources |
| `commercial_library_tsne_coordinates.csv` | Commercial-library coordinates |
| `n_modifiers_tsne_coordinates.csv` | Tested-modifier coordinates |
| `cluster_feature_summary.csv` | Cluster sizes, feature fractions, and mean structural counts |
| `cluster_representative_examples.csv` | Eight nearest-to-centroid examples per cluster |
| `cluster_feature_summary_readable.txt` | Human-readable cluster summaries |

The coordinate CSVs include `Dimension_1`, `Dimension_2`, `Cluster`, `Source_Label`, and a comma-separated `Fingerprint_Product` bit vector. To compare runs, keep the input files, preprocessing rules, fingerprint settings, and random seeds unchanged.

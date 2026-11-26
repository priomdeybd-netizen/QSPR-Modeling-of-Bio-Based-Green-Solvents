# Machine learning-driven screening and QSPR modeling of bio-based green solvents

Code, curated dataset and frozen results for the manuscript *Machine Learning-Driven Screening and QSPR Modeling of Bio-Based Green Solvents to Replace DMF, NMP and DMAc* (Dey, Talukder, Roy; Shahjalal University of Science and Technology, Sylhet).

Pipeline: RDKit descriptors -> nested repeated cross-validation (7 regressors, 3 feature sets) -> y-scrambling and cluster-split validation -> applicability domain and prediction intervals -> multi-criteria screening (TOPSIS, Pareto, Monte-Carlo robustness, weight and hazard sensitivity).

## Quick start (Google Colab, no installation)
1. Open `notebooks/01_Green_Solvent_QSPR_ALL_IN_ONE_v7.ipynb` in Google Colab.
2. `Runtime -> Run all` (full run, `FAST=False`, takes a long time).
3. The notebook downloads `green_solvent_outputs.zip` with all tables, figures and the model card.

Local use: `pip install -r requirements.txt`, then run the notebook in Jupyter (remove the `google.colab` download lines).

## Repository layout
| Path | Content |
|---|---|
| `notebooks/01_...ALL_IN_ONE_v7.ipynb` | Full analysis, extras (weight/hazard sensitivity, Cyrene hold-out, tables). Dataset is embedded. |
| `notebooks/02_optional_ORCA_helper.ipynb` | Generates ORCA inputs (r2SCAN-3c) and parses outputs into `qm_descriptors.csv` (ORCA must be run separately). |
| `notebooks/03_reference_finder.ipynb` | Crossref lookup to assemble reference candidates (verify every DOI). |
| `notebooks/04_data_update_hazard_holdout.ipynb` | Merge literature values, PubChem hazard screen, hold-out workflow. |
| `data/curated_dataset_v7.csv` | 102 solvents; the `source` column states the origin of every value and which `cmr` flags are verified. |
| `data/holdout_values.csv` | Literature hold-out values (Cyrene; Sherwood et al., Chem. Commun. 2014, 50, 9650). |
| `data/TODO_primary_values_and_ECHA_check.csv` | Worksheet for replacing web-table values with primary literature and recording ECHA entries. |
| `data/hazard_review_pubchem_screen.csv` | Raw PubChem GHS screen (union of notifications; not the harmonised classification). |
| `results/run6_dataset_v7/` | Frozen outputs of the final full run (FAST=False, seed 42) on dataset v7: tables, figures 1-11, predictions with intervals and applicability domain, trained models, model card. |
| `docs/` | SI draft (OECD QSAR principles table, model card text, limitations) and the submission checklist. |

## Data provenance and caveats (read before reuse)
- Kamlet-Taft, Hansen, permittivity and boiling-point values come from the Stenutz *Tables for Chemistry* web compilation (secondary source); Hansen values converted from (cal/mL)^0.5 to MPa^0.5. Primary-literature replacement is in progress.
- `cmr` flags are verified only where the `source` column says `VERIFIED`; flags marked `SCREENED` come from PubChem GHS aggregates and are not a harmonised ECHA classification; blank means unknown (scored 0.5 in the ranking). Bio-origin labels are not verified.
- No flash-point data were sourced, so no flash-point model is trained; the boiling-point model (CV Q2 = 0.42) is not usable and is not relied on.
- All results are computational. The models are for ranking candidates for experimental follow-up, not for safety or regulatory decisions.

## Citation
See `CITATION.cff`. Archived on Zenodo: https://doi.org/10.5281/zenodo.24527393

## License
Code: MIT (`LICENSE`). Choose a data license before release (CC BY 4.0 is suggested for `data/`); note that the underlying property values originate from third-party compilations and publications cited in the `source` column.

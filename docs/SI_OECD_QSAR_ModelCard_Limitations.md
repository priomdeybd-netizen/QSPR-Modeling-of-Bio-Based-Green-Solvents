# Supporting Information draft: OECD QSAR validation, model card, limitations

*Numbers below are from the final FAST=False run (5 CV repeats, 50 y-scrambles, seed 42). Remaining [brackets] are yours to fill (GitHub/Zenodo DOI).*

## S1. OECD principles for the validation of (Q)SAR models

| OECD principle | How it is met in this work | Where to find it |
|---|---|---|
| 1. Defined endpoint | Seven endpoints: Kamlet-Taft alpha, beta, pi*; Hansen dD, dP, dH (MPa^0.5); log10 dielectric constant; boiling point; flash point only if sourced data exist. Values come from the `source` column of the curated table (secondary web tables; primary-literature replacement stated in Limitations). | curated_dataset.csv |
| 2. Unambiguous algorithm | Fully scripted pipeline: RDKit descriptors (2D, MACCS, Morgan counts, optional ORCA QM), 7 regressors, nested CV, fixed seed 42, package versions logged. Code and data archived at [GitHub/Zenodo DOI]. | notebook, model_card.json |
| 3. Defined applicability domain | Leverage (h* = 3(k+1)/n in a 5-PC space) combined with max Tanimoto similarity >= 0.25 to the training set. Predictions outside the domain are flagged and their CV-residual 90% intervals are doubled. | predictions_with_intervals_and_AD.csv |
| 4. Goodness-of-fit, robustness, predictivity | Nested repeated CV (inner 3-fold grid search, outer 5-fold, 5 repeats) with a mean-predictor baseline; Q2 and RMSE reported per endpoint; y-scrambling (50 permutations, p = 0.02 for all endpoints); Butina cluster-split CV (cutoff 0.7) as a stricter test. Endpoints with CV Q2 < 0.5 are labelled "not usable" and excluded from decisions. | qspr_best_models.csv, y_scrambling.csv, cluster_cv.csv |
| 5. Mechanistic interpretation | SHAP analysis of the best model per endpoint, related to known solvent physics (polarity, H-bond donors/acceptors, size). With QM features, dipole and HOMO-LUMO gap are interpreted directly. | fig. SHAP, [section] |

## S2. Model card

- **Intended use:** early-stage, rank-ordering screening of candidate bio-based dipolar aprotic solvents versus DMF/NMP/DMAc. Not for regulatory decisions, safety claims or process design without experiment.
- **Training data:** 102 solvents; labelled coverage per endpoint: bp 27, eps 69, Hansen 58, alpha 57, beta 57, pi* 54, flash 0 . Sources in the `source` column.
- **Models/features:** best per endpoint chosen by nested CV from Ridge, SVR-RBF, GPR, RF, ExtraTrees, GradBoost on RDKit2D, +MACCS, Morgan counts, [+QM].
- **Performance (nested CV, best model per endpoint):**

| Endpoint | n | Best model / features | Q2 | RMSE | RMSE baseline | Cluster-CV Q2 | Scrambled max Q2 | Usable (Q2>=0.5) |
|---|---|---|---|---|---|---|---|---|
| alpha | 57 | Ridge / RDKit2D+MACCS | 0.867 | 0.123 | 0.347 | 0.911 | 0.039 | yes |
| beta | 57 | ExtraTrees / RDKit2D | 0.746 | 0.127 | 0.256 | 0.766 | 0.048 | yes |
| pi* | 54 | GradBoost / RDKit2D | 0.704 | 0.140 | 0.263 | 0.673 | 0.145 | yes |
| dD (MPa^0.5) | 58 | GradBoost / RDKit2D+MACCS | 0.722 | 0.767 | 1.498 | 0.649 | -0.036 | yes |
| dP (MPa^0.5) | 58 | GPR / RDKit2D | 0.661 | 3.559 | 6.373 | 0.452 | 0.062 | yes |
| dH (MPa^0.5) | 58 | Ridge / RDKit2D+MACCS | 0.745 | 3.979 | 8.022 | 0.673 | 0.231 | yes |
| log10 eps | 69 | ExtraTrees / RDKit2D+MACCS | 0.691 | 0.266 | 0.487 | 0.581 | -0.002 | yes |
| bp (C) | 27 | GPR / RDKit2D+MACCS | 0.422 | 49.9 | 68.8 | 0.219 | 0.188 | **no** |
| flash point | 0 | not modelled (no sourced data) | - | - | - | - | - | no |

  Cluster-split Q2 drops most for dP (0.661 to 0.452) and eps (0.691 to 0.581), so those predictions generalise less well to new scaffolds. The boiling-point model is not usable and is not relied on.
- **Screening method:** performance distance (Hansen Ra to nearest of DMF/NMP/DMAc and Kamlet-Taft difference), hazard, bio-origin, process window; TOPSIS (0.45/0.25/0.20/0.10), Pareto set, Monte-Carlo robustness (1000 draws over weights and prediction error).
- **Out-of-scope:** ionic liquids, mixtures (blend design uses linear mixing only), solvents outside the applicability domain, temperature dependence.
- **Version/Reproducibility:** frozen predictions and code at [Zenodo DOI]; random seed 42.

## S3. Limitations (paste into the Discussion)

1. **Small, secondary data.** The training table (about 100 solvents) was assembled from a secondary compilation (Stenutz Tables for Chemistry); primary values (e.g. Kamlet-Taft from Jessop et al. 2012 and Hansen handbook values) should replace them before submission. Noise in these values limits attainable accuracy.
2. **Sparse bio-based coverage.** The most interesting candidates (Cyrene, NBP, dimethyl isosorbide, PolarClean, CPME) have few or no measured labels, so their predictions are extrapolations; the applicability-domain flag and intervals must be read with this in mind.
3. **Hazard flags are partly unverified.** CMR status was checked against supplier SDS / NITE GHS / ECHA notifications only for the rows marked "cmr VERIFIED"; absence of a classification in a supplier SDS is not proof of safety (many substances have "no data"). Ranking is sensitive to this criterion, as shown by the rank-with/without-hazard analysis.
4. **Boiling and flash points.** The boiling-point model has CV Q2 = 0.42 (not usable) and no flash-point model could be trained, so both are weak points of the process-window criterion.
5. **No experimental validation yet.** Unless the prospective Kamlet-Taft measurements or a literature hold-out are completed, the results are retrospective cross-validation only.
6. **Scoring weights are subjective.** Monte-Carlo weight perturbation quantifies, but does not remove, this dependence.
7. **Linear mixing for blends** ignores non-ideal behaviour.
8. **Environmental/LCA data** (E-factor, biodegradability, life-cycle impacts) are not included; "green" here means hazard plus bio-origin plus performance proxies.

## S4. Screening result summary (final run with v6 dataset; use for the Results text)

Hazard inputs: 10 solvents confirmed from SDS/NITE/ECHA notification, 20 more screened from PubChem GHS aggregates (not harmonised ECHA classification), the rest unknown (scored 0.5).

| Rank | Solvent | TOPSIS | Median MC rank | P(top-3) | Rank without hazard | Notes |
|---|---|---|---|---|---|---|
| 1 | GVL | 1.000 | 2 | 0.82 | 1 | Pareto; CMR class absent in supplier SDS |
| 2 | Cyrene | 0.999 | 6 | 0.34 | 2 | outside AD; predicted values |
| 3 | Methyl levulinate | 0.908 | 3 | 0.59 | 4 | predicted values |
| 4 | 2-Methylfuran | 0.884 | 4 | 0.44 | 5 | hazard screened only |
| 5 | Dimethyl succinate | 0.874 | 5 | 0.27 | 6 | |
| 6 | NBP | 0.854 | 7 | 0.16 | 8 | hazard screened only |
| 8 | Propylene carbonate | 0.826 | 7 | 0.03 | 12 | |
| 16 | Dimethyl isosorbide | 0.665 | 23 | 0.01 | 3 | hazard unknown; outside AD |

Robust conclusion: GVL is the stable top candidate (P(top-3) = 0.82, also first without the hazard term); Cyrene, methyl levulinate, 2-methylfuran and dimethyl succinate form a second tier. Ranks of solvents with unknown hazard are depressed by the 0.5 penalty; resolve hazard data before interpreting positions beyond the top tier. Model performance (Table S1 / Section S2) is unchanged because no property labels changed.

## S5. Literature hold-out test (Cyrene)

Cyrene had **no labels in the training set**, so its literature values form a genuine hold-out (6 endpoints, one solvent). Literature: Kamlet-Taft from Sherwood et al., *Chem. Commun.* 2014, 50, 9650 (SI); Hansen values as cited there (reproduced in the Cygnet accepted manuscript, Table 3; originally predicted by HSPiP, not measured).

| Property | Predicted (90% interval) | Literature | Abs. error | Inside interval |
|---|---|---|---|---|
| alpha | -0.07 (-0.28, 0.13) | 0.00 | 0.07 | yes |
| beta | 0.40 (0.21, 0.59) | 0.61 | 0.21 | no (just outside) |
| pi* | 0.90 (0.71, 1.09) | 0.93 | 0.03 | yes |
| dD | 18.5 (17.4, 19.7) | 18.8 | 0.3 | yes |
| dP | 12.3 (6.5, 18.0) | 10.6 | 1.7 | yes |
| dH | 6.1 (2.3, 9.9) | 6.9 | 0.8 | yes |

Five of six literature values fall inside the 90% intervals; beta is underpredicted (0.40 vs 0.61, error larger than the model RMSE of 0.13), which is consistent with Cyrene being outside the applicability domain. Caveat: the Cyrene Hansen values are themselves computed, so agreement there tests consistency with a predictor, not with experiment; this is one solvent, so it is supporting evidence only, not a full prospective validation.

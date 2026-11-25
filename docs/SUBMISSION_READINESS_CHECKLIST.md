# Is the paper ready to submit? Not yet. Work needed, in order of impact

## A. Must fix before any submission (reviewers will reject without these)
1. **Primary data.** Replace Stenutz web-table values with primary literature (Jessop 2012 and Kamlet-Taft originals; Hansen handbook; CRC/Marcus). Fill `TODO_primary_values_and_ECHA_check.csv` (columns TODO_*), cite the DOI per value.
2. **Hazard verification.** For every solvent in the TODO file, look up ECHA (echa.europa.eu > Information on chemicals > C&L Inventory; use *harmonised* Annex VI entry first, else notified). Record the entry. Hazard is 25% of the ranking weight.
3. **Real reference list** with in-text citations (the draft has none). Use a reference manager.
4. **Insert figures 1-10** with captions; add the SI (OECD table, model card, limitations: `SI_OECD_QSAR_ModelCard_Limitations_FILLED.md`).
5. **Zenodo DOI + GitHub** for code, curated data and frozen predictions.
6. **Supervisor/co-author read-through**, authorship and affiliation.

## B. What makes it stronger (most journals will ask)
1. **Experimental or independent validation:** measure Kamlet-Taft (UV-vis with Reichardt's dye, 4-nitroaniline, N,N-diethyl-4-nitroaniline) for ~6 solvents (GVL, Cyrene, DMI, methyl levulinate, dimethyl succinate, NBP) plus DMF/NMP, OR collect literature values for 15-20 held-out solvents from papers not used in training. One Cyrene hold-out is not enough.
2. **Application test vs DMF** for the top 2 candidates (e.g. polymer dissolution, amide coupling yield). Even one reaction or solubility test greatly raises impact.
3. **More labelled bio-based solvents** (the weakest coverage); retrain and rerun.
4. **QM descriptors** (ORCA) only if someone can run them; otherwise drop from claims.
5. **Sensitivity of ranking** to weights is already shown; add a short comparison with an existing solvent guide (CHEM21).

## C. Journal fit (after A is done)
Realistic for the current work (computational + literature hold-out): *ACS Sustainable Chem. Eng.*, *Green Chemistry* (needs B1/B2), *J. Chem. Inf. Model.*, *Molecular Informatics*, *J. Cheminformatics*. With experimental validation: Green Chemistry or ACS Sustainable Chem. Eng. are plausible. Check each journal's current scope and article-type rules yourself.

# DprE1 bioactivity prediction: how far can public data take us?

**Status:** work in progress, not peer reviewed. Author: Suraj Gade (M.Sc. Chemistry, Sanjivani University). Supervisor input pending on scope and authorship.

## Question
Can machine-learning models predict the potency (pIC50) of inhibitors of DprE1, a Mycobacterium tuberculosis drug target, from public ChEMBL data, and how much does the answer depend on how the models are validated?

## Main finding (so far)
On 130 compounds with exact M. tuberculosis DprE1 biochemical IC50 values:
- Random Forest (descriptors, MACCS, Morgan fingerprints) and a Tanimoto kNN show at most a weak signal within chemical series (random-split R2 about 0.03 to 0.09 vs a mean-predictor baseline of -0.07).
- On held-out scaffolds, no model beats the baseline (scaffold-split R2 about -0.13 to -0.30; baseline -0.16), under three validation protocols and with or without 12 review-derived compounds.
- A compound's nearest analog carries modest information (correlation 0.42, 95% bootstrap interval 0.22 to 0.57).
- Source paper explains about 28% of pIC50 variance, and one paper supplies 55% of the compounds, so source, chemical series and assay conditions are tangled together.
- SHAP interpretation is deliberately not run, because the models show no skill to explain.

## Data
- ChEMBL target CHEMBL3804751 (M. tuberculosis H37Rv), IC50, ChEMBL release 37, retrieved 3 October 2026 through the web client (live API: re-running later may return different data, so use the saved CSVs).
- 156 raw records -> 140 exact values -> 132 in nM -> 130 unique compounds (median of repeats).
- 17 assays from 12 source documents; two documents look like reviews (12 compounds), checked in a sensitivity analysis.
- BindingDB export (`bindingdb_dpre1.tsv`, downloaded 3 October 2026): every row is sourced from ChEMBL, so it adds no independent data.

## Files
| File | What it is |
|---|---|
| DprE1_ML_v1.ipynb | Full notebook, with outputs |
| dpre1_raw_with_sources.csv | Raw ChEMBL records with assay and paper IDs |
| dpre1_clean.csv | Cleaned data, one row per compound |
| model_eval_all_splits.csv | R2, RMSE, MAE for every split (random and repeated scaffold) |
| model_eval_scaffold_fixed.csv | Same for the fixed-size scaffold protocol |
| split_assignments*.json | Exact test compounds in every split |
| bindingdb_dpre1.tsv | BindingDB export used for cross-checking |
| eda_summary.png, fig_split_distributions.png | Figures |
| requirements.txt | Package versions |

## How to reproduce
1. Open the notebook in Google Colab. Upload `bindingdb_dpre1.tsv` to `/content` when asked.
2. Runtime -> Run all. (Seed 42 throughout.)
3. Check: section 2b prints 130 unique compounds; section 17 shows baseline scaffold R2 = -0.155 for all_130.

Environment: Python (Colab), pandas 2.2.3, numpy 2.1.3, scikit-learn 1.6.1, RDKit 2026.03.6, matplotlib 3.10.0.

## Validation protocols
1. Random 80/20 splits (20 repeats).
2. Scaffold-grouped splits (20 repeats; whole Murcko scaffold families held out; test size varies).
3. Fixed-size scaffold splits (20 repeats; test about 20%; families over 15 compounds always in training).
Every model is compared with a mean-predictor baseline.

## Limitations
Small dataset; one paper supplies 55% of compounds; source, series and assay are confounded; almost no independent repeated measurements; Murcko scaffolds hide the nitro group; the nitro flag is only a rough proxy for covalent mechanism; stereochemistry differs between ChEMBL and BindingDB keys.

## Open items
Check whether 7 compounds reported in ug/mL can be converted to nM with molecular weight (section 19d); trace review-derived records to primary papers; documented literature-gap search; decision on scope with supervisor.

## AI-use disclosure
An AI assistant (Claude) was used for explanations, code debugging and feedback on drafts. The author typed, ran and checked the code and results. Wording to be finalised with the supervisor and the target journal's policy.

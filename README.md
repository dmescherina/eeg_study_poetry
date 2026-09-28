# Anticipatory and Theme-Specific Neural Oscillations Predict Aesthetic Evaluation of Poetry

[![Paper](https://img.shields.io/badge/PNAS-10.1073%2Fpnas.2536387123-1f6f78)](https://www.pnas.org/doi/10.1073/pnas.2536387123)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

Code accompanying the PNAS paper *"Anticipatory and theme-specific neural oscillations predict aesthetic evaluation of poetry"* (Meshcherina, Chaudhuri & Bhattacharya, PNAS 2026), https://doi.org/10.1073/pnas.2536387123.

An archived, citable version of this repository is available on Zenodo: https://doi.org/10.5281/zenodo.23011758.

This repo contains the analysis pipeline used to extract EEG spectral features, train and evaluate predictive models (ordinal regression and LightGBM), interpret models with SHAP, and address reviewer questions on genre-vs-content and dimension-general effects.

The underlying EEG dataset and experimental protocol are described in a companion data descriptor: Chaudhuri & Bhattacharya, *Sci. Data* 12, 1898 (2025), https://doi.org/10.1038/s41597-025-06189-w. An interactive results dashboard is available at https://mainpy-wgbwxndukangpkthdwbfq6.streamlit.app/.

## Pipeline overview

The notebooks are numbered roughly in the order they should be run. A few share a number because they represent alternative or sequential passes over the same stage (see notes below).

| Notebook | Purpose |
|---|---|
| `1_load_data_and make_features.ipynb` | Loads ICA-pruned `.set` EEG files (MNE), runs Morlet wavelet time-frequency decomposition, and computes spectral power features per electrode cluster / frequency band / temporal window. |
| `2_feature_pc_analysis.ipynb` | Pivots the raw feature table into the trial-by-feature matrix, exploratory PCA/clustering over the 90 spectrotemporal features. |
| `3_check_target_scores_distribution.ipynb` | Loads behavioural rating data, checks distributions of the five aesthetic dimensions across Haiku/Senryu/Control. |
| `4_ordinal_classifier_within_categories_and_target.ipynb` | Linear ordinal models (sklearn Logistic Regression + GridSearchCV, and statsmodels `OrderedModel`) per condition/dimension. |
| `4_ordinal_models_linear_and_lightgbm.ipynb` | Combined pipeline: trains both linear ordinal baselines and LightGBM models across all 15 condition × dimension combinations, including cross-validation. |
| `5_resaving_ordinal_lightgbm_with_joblib.ipynb` | Re-fits/saves final LightGBM and statsmodels ordinal models as `.joblib` objects (see `models/`) for downstream SHAP analysis. |
| `6_shap_interpretability_with_power_transform.ipynb` | SHAP decomposition of the LightGBM models (with power-transformed features); produces the feature-importance tables used in Fig. 3. |
| `6_coef_data_to_streamlit_dashboard.ipynb` | Combines linear coefficients and LightGBM SHAP values into the format used by the Streamlit dashboard. |
| `07_question_5a_genre_vs_content.ipynb` | Reviewer response: item-level analysis testing whether Haiku/Senryu differences reflect content rather than genre label (quartile comparisons, RSA). Produces SI Fig. S4 / S7. |
| `08_question_5b_pca_general_factor.ipynb` | Reviewer response: PCA on poem-level ratings identifying the shared "general aesthetic factor" (PC1), and whether neural features predict it. Produces Fig. 4. |

**Note on naming:** notebooks `4_*` and `6_*` come in pairs that were developed iteratively — `4_ordinal_classifier_within_categories_and_target.ipynb` and `5_resaving_ordinal_lightgbm_with_joblib.ipynb` feed into `4_ordinal_models_linear_and_lightgbm.ipynb`, which is the consolidated version used for the final reported numbers. `07`/`08` (zero-padded) are the later reviewer-driven notebooks and are self-contained.

## Data

Raw EEG (`.set` files) and behavioural rating data are **not included** in this repo — see the data descriptor above for access. Processed feature tables expected by the notebooks:

- `mean_power_morlet_wavelets_with_delta_theta.csv` — trial-level spectral power per cluster × time period × frequency band (long format).
- `mean_power_by_epochs_granular.csv` — same features in wide/epoch format used for modelling.
- `target.csv` — participant ratings across the five aesthetic dimensions, keyed by participant and stimulus.

Paths in the notebooks (e.g. `../project_eeg/data/...`) reflect the original local directory structure and will need adjusting to wherever you place these files.

## Models and outputs

- `models/` — saved LightGBM (`lightgbm_model_<condition>_<dimension>.joblib`) and statsmodels ordinal (`statsmodels_ordinal_<condition>_<dimension>.joblib`) models for each of the 15 condition × dimension combinations.
- `lgb_results_matrix_with_cv.joblib` / `..._intermediate.joblib` — cross-validated performance matrices (ROC-AUC, kappa) for all model pairs, including cross-condition/cross-dimension transfer (Fig. 2).
- `model_performance_roc.csv`, `model_performance_kappa.csv`, `model_comparison_linear.csv` — tabulated performance metrics.
- `lightgbm_roc_auc_heatmap.png`, `lightgbm_kappa_heatmap.png` — figure source plots.
- `linear_coef_sign.csv`, `feature_importance_correlations.csv`, `top_features_comparison.csv`, `all_models_feature_comparison.csv` — coefficient and SHAP feature-importance summaries feeding Fig. 3 and the dashboard.

## Environment

Dependencies are listed in `eeg_env_requirements.txt` (Python, generated via `pip freeze`). Key packages: `mne`, `lightgbm`, `shap`, `statsmodels`, `scikit-learn`, `pandas`, `seaborn`/`matplotlib`.

```bash
pip install -r eeg_env_requirements.txt
```

## Citation

If you use this code, please cite the PNAS paper, this repository's Zenodo archive, and the companion data descriptor:

- Meshcherina, D., Chaudhuri, S., & Bhattacharya, J. (2026). Anticipatory and theme-specific neural oscillations predict aesthetic evaluation of poetry. *PNAS*. https://doi.org/10.1073/pnas.2536387123
- Meshcherina, D., Chaudhuri, S., & Bhattacharya, J. (2026). Code for "Anticipatory and theme-specific neural oscillations predict aesthetic evaluation of poetry" [Software]. Zenodo. https://doi.org/10.5281/zenodo.23011758
- Chaudhuri, S., & Bhattacharya, J. (2025). *Sci. Data* 12, 1898. https://doi.org/10.1038/s41597-025-06189-w

## License

This work is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/). See [`LICENSE`](LICENSE) for the full legal text.

You are free to share and adapt the material for any purpose, provided you give appropriate credit, link to the license, and indicate if changes were made.

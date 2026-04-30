# CPSM_PRAD — Prostate Cancer Survival Analysis

MTLR-based survival modeling for TCGA prostate adenocarcinoma (PRAD) using the [CPSM](https://bioconductor.org/packages/CPSM/) Bioconductor package.

## Overview

This repository applies the Cancer Patient Survival Model (CPSM) pipeline to TCGA-PRAD, identifying prognostic genes and building predictive survival models using Multi-Task Logistic Regression (MTLR).

**Key features:**
- Progression-Free Interval (PFI) endpoint from Liu et al. (2018) TCGA-CDR supplement (~17% event rate vs ~2% for OS)
- Gleason score as the primary clinical feature (AJCC staging is uninformative for prostate cancer)
- Five MTLR model architectures comparing clinical, genomic, and combined predictors
- 1,222 univariate survival-significant genes identified (p<0.05)

## Pipeline

| Step | Function | Description |
|------|----------|-------------|
| 1 | Data acquisition | TCGA-PRAD via TCGAbiolinks, PFI from Liu et al. mmc1.xlsx |
| 2 | `data_process_f()` | Survival time conversion (days → months) |
| 3 | `tr_test_f()` | 80/20 train-test split |
| 4 | `train_test_normalization_f()` | Log2 + quantile normalisation |
| 5a | `Lasso_PI_scores_f()` | LASSO feature selection + PI score |
| 5b | `Univariate_sig_features_f()` | Univariate Cox screening |
| 6 | `MTLR_pred_model_f()` | 5 MTLR models (clinical / PI / PI+clinical / genes / genes+clinical) |
| 7–11 | Visualisation | Survival curves, KM overlay, nomogram, risk group prediction |

## Results

| Model | Train C-index | Test C-index |
|-------|:---:|:---:|
| Model 1 — Clinical (Age + Gleason) | 0.68 | 0.68 |
| Model 2 — PI score | 0.84 | 0.70 |
| Model 3 — PI + Clinical | 0.85 | 0.71 |
| Model 4 — Univariate genes | 0.80 | 0.65 |
| Model 5 — Genes + Clinical | 0.83 | 0.69 |

Best model: **Model 3 (PI + Clinical)** — test C-index 0.71.

## Requirements

- R ≥ 4.4
- [CPSM](https://bioconductor.org/packages/CPSM/), TCGAbiolinks, SummarizedExperiment, survival, glmnet, MTLR, readxl
- Liu et al. TCGA-CDR supplement (`mmc1.xlsx`) — [download](https://doi.org/10.1016/j.cell.2018.02.052)

## Usage

1. Place `mmc1.xlsx` in the working directory
2. Source the pipeline:

```r
source("CPSM_TCGA_PRAD_Pipeline.R")
```

The script auto-detects cached TCGA data in the working directory and loads from `df.rds` or the GDC cache.

## Data

- **Source:** [TCGA-PRAD](https://portal.gdc.cancer.gov/projects/TCGA-PRAD) via TCGAbiolinks
- **Samples:** ~497 primary tumour patients (after deduplication)
- **Endpoint:** PFI (Progression-Free Interval) — 85 events (17.1%)
- **Genes:** Top 20,000 by variance → 1,222 univariate significant (p<0.05)

## References

1. Liu J et al. An Integrated TCGA Pan-Cancer Clinical Data Resource. Cell. 2018;173(2):400-416. doi:10.1016/j.cell.2018.02.052
2. Kaur H, Das P, Camphausen K, Shankavaram U. CPSM: R-package of an Automated Machine Learning Pipeline for Predicting the Survival Probability of Single Cancer Patient. bioRxiv. 2024. doi:10.1101/2024.11.14.623597

## Author

Denver Ncube

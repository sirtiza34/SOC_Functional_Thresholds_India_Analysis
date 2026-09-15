# SOC_Functional_Thresholds_India_Analysis

This repository reproduces the analytical results related to the Research Paper "**Integrating functionally-based soil organic carbon threshold models with reconstructed historical baseline to evaluate a soil degradation boundary in India**" in a self-contained Jupyter notebook:

- **`SOC_Functional_Thresholds_India_Analysis.ipynb`** — the core quantitative pipeline (historical baseline, tiered functional thresholds, literature-threshold benchmarking, and boundary definition).

## What `SOC_Functional_Thresholds_India_Analysis.ipynb` computes

1. **Historical SOC baseline**
   for ~41,507 historical (~10 km) grid cells, the fraction of pre-disturbance ("no land-use", NoLU at 10000 BC) SOC retained by 2010, and the resulting historical depletion metric `D_2010`, aggregated to ecoregion level by area-weighted mean.

2. **Contemporary functional thresholds**
   SOC concentration thresholds at which four soil functions (physical: bulk density; chemical: pH, cation exchange capacity; ecosystem: net primary productivity) begin to deteriorate, applied via a pre-computed, tiered (unit-specific > sub-ecoregion > ecoregion > national) assignment across 1,156 ecoregion x soil-order x land-use units (ECUs) and 50.48 million 250 m pixels.
   
3. **Literature-threshold validation**
   the empirically-derived thresholds above are benchmarked against two SOC concentration values commonly cited in the literature as generic global degradation thresholds: 2.00% and 1.10% SOC by mass (20 and 11 g C/kg).

4. **Degradation boundary definition**
   a three-line evidence synthesis (multi-function convergence, historical depletion, functional transition probability) classifying land area as within bounds, approaching boundary, or beyond boundary, computed under both the empirical and the two literature-referenced threshold bases.

5. **Historical depletion vs. contemporary functional risk**
   ecoregion-level correlation between historical SOC depletion and current functional risk under all three threshold bases.

Sensitivity (8 alternative model specifications) and resilience-classification results, which are computationally expensive and unaffected by the analyses above, are loaded from pre-computed summary files for completeness rather than recomputed.

## Data Availability
The datasets required to run the scripts are archived on Zenodo accessible at https://doi.org/10.5281/zenodo.18884892. The archive contains the processed spatial datasets used for $SOC_{stock}$ modelling and simluation, including climate forcings, LUH2 land-use forcings, and other static geo-environmental features required to reproduce the simulations.

**Data Citation:** _Majid, S. I. (2026). Geostatistical Dataset for Topsoil Organic Carbon Modelling in India Using CMIP6 Climate Projections and LUH2 Land Use Data [Data set]. Zenodo. https://doi.org/10.5281/zenodo.18884892_

## Scope note

Two chemical/ecosystem threshold models and the resulting tiered threshold assignment are fit by hierarchical Bayesian partial-pooling regression (Markov Chain Monte Carlo or MCMC (No-U-Turn Sampler, four chains)) in the full project pipeline and are loaded here as pre-fitted results (`data/step11_thresholds.csv`, `data/step5_threshold_uncertainty.csv`) rather than refit, since MCMC sampling is computationally expensive and not the subject of this notebook. Every other analytical step is computed in full in this notebook from the raw exported raster/tabular layers in `data/`.

## Data

All inputs are provided in `data/` (~1.0 GB total, dominated by the 700 MB pixel-level parquet file, a 280 MB spatially-declustered pixel sample used for illustrative scatter maps, and a 127 MB distance-to-threshold pixel sample used by Fig. S3):

| File | Description |
|---|---|
| `step13_soc_stack.tif` | Historical SOC stock reconstruction, 7 epochs (NoLU, 900-2010 CE), ~10 km grid |
| `step10_feature_stack.tif` | Historical feature stack (used here only for its valid-pixel mask) |
| `ecoregion_id.tif` | Ecoregion identifier raster, 250 m grid |
| `ecu_support_table.csv` | Ecoregion id-to-name lookup and sample-support metadata |
| `step11_thresholds.csv` | Hierarchical Bayesian threshold fits (national and ecoregion level) |
| `step5_threshold_uncertainty.csv` | Tiered threshold assignment (median, lower/upper 95% CI) per ECU |
| `master_analysis_database.parquet` | Pixel-level contemporary database, 50.48M rows |
| `step19_sensitivity_summary.csv` | Pre-computed 8-scenario sensitivity summary |
| `step18_group_a_resilience_opportunity.csv`, `step18_group_b_continued_loss_risk.csv` | Pre-computed resilience-class ecoregion lists |

The notebook additionally reads the ecoregion-level and pixel-sample intermediate result tables in `data/` (`step1_*`-`step20_*`), which are the saved outputs of the corresponding pipeline steps; the largest is `step6_functional_state_sample.csv` (280 MB, the spatially block-declustered 395,750-pixel sample carrying per-pixel `P(SOC<T)` for each response domain).

The historical SOC layers are a depth-integrated 0-30 cm reconstruction built following the global SOC-debt reconstruction method of Sanderman, Hengl & Fiske (2017, *PNAS*). Contemporary soil layers are SoilGrids v2.0 (Poggio et al., 2021, *SOIL*).

## Running

```
pip install numpy pandas rasterio pyarrow scipy matplotlib pillow jupyter
jupyter nbconvert --to notebook --execute --inplace SOC_Functional_Thresholds_India_Analysis.ipynb
```

Runtime is dominated by loading the 50.48M-row parquet file and the 280 MB pixel sample; each
notebook runs in a few minutes on a standard laptop. Outputs (CSVs and figures) are written to
`outputs/`.

## Outputs

From `SOC_Functional_Thresholds_India_Analysis.ipynb`:

| File | Content |
|---|---|
| `historical_baseline_ecoregion.csv` | Ecoregion-level historical SOC baseline (F_2010, D_2010) |
| `literature_threshold_comparison.csv` | National % land area below threshold, empirical vs. literature bases (manuscript Table 7) |
| `boundary_definition_comparison.csv` | Degradation-boundary area classification under all three threshold bases (manuscript Table 8) |
| `historical_risk_correlation_comparison.csv` | Ecoregion-level correlation, historical depletion vs. functional risk (manuscript Table 9) |

## Relationship to the full project

The  notebook is a standalone, self-contained subsets of a larger three-phase analysis pipeline; together they reproduce all analyses reported in the manuscript and its Supplementary Information, computed directly from raw exported data rather than depending on any other publication.

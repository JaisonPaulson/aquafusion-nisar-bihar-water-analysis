# Dual-Frequency NISAR SAR Fusion for Surface-Water Mapping in the Ganga Alluvial Plain, Bihar, India

Exploratory analysis of NISAR L-band and S-band Global Coherence and Backscatter (GCOV) data. The notebook inspects the source grids, extracts HH/HV power, compares low-backscatter evidence between frequency bands, and tests equal-weight and local-contrast-weighted screening scores.

> **Status:** These outputs are exploratory candidate layers. They are not validated water classifications, official inundation extents, or change-detection products.

## Repository contents

- [`00_AquaFusion_Data_Inspection.ipynb`](https://github.com/JaisonPaulson/aquafusion-nisar-bihar-water-analysis/blob/main/00_AquaFusion_Data_Inspection.ipynb) — analysis notebook and reproducible workflow.
- `outputs/maps/` — PNG figures summarizing the acquisition, backscatter previews, threshold sensitivity, fusion comparisons, band agreement, and candidate area summaries.

## Data and study window

The analysis uses NISAR Level-2 GCOV HDF5 products for L-band and S-band from the 30 October 2026 acquisition. The tested groups are `science/LSAR/GCOV/grids/frequencyA` and `science/SSAR/GCOV/grids/frequencyA`.

Both grids use EPSG:32645 (WGS 84 / UTM zone 45N), with 10 m pixel spacing in each direction. Their native raster dimensions and extents differ slightly. A shared 800 × 800 pixel window around the selected Bihar study centre was used for the comparative analysis. The nearest centre pixel was valid in both band masks.

Place the source HDF5 files in this structure:

```text
DATA/
├── L_BAND/   # one L-band GCOV .h5 product
└── S_BAND/   # one S-band GCOV .h5 product
```

The notebook's initial inspection cell expects the files under `~/Documents/AQUAFUSION_NISAR/DATA/`. If your data are elsewhere, update the `base` path near the beginning of the notebook. Where the notebook selects the first `.h5` file in each folder, keep only the intended product there or set the paths explicitly.

## Requirements

Run the notebook in JupyterLab with Python 3. The workflow uses:

- `h5py`
- `numpy`
- `matplotlib`
- `scipy`
- `rasterio` for GeoTIFF export

Install any missing packages in the same Python environment used by the notebook kernel. GeoTIFF export is optional if you only want to reproduce the plots and summary values.

## Run the analysis

1. Download or copy the matching L-band and S-band GCOV HDF5 files into the input folders above.
2. Open `00_AquaFusion_Data_Inspection.ipynb` in JupyterLab and check the `base` directory and selected filenames.
3. Run the notebook cells in order from the top. The workflow inspects metadata and masks, checks the study-centre footprint, extracts HH/HV power, constructs a common analysis window, and then calculates exploratory candidate scores and comparisons.
4. Review the printed shapes, valid-pixel counts, and plots before using any output. If exporting GeoTIFFs, run the export cell after the analysis arrays have been created in the current kernel.
5. Open exported GeoTIFFs in QGIS and confirm the CRS, extent, NoData display, and band interpretation before styling or further analysis.

The notebook is the authoritative source for the processing steps. The map PNGs are visual summaries and may contain display scaling chosen for readability.

## Method summary

The two frequency grids were checked for CRS, pixel spacing, coordinate extents, covariance terms, and mask values. The official study-centre coordinate was checked against the common footprint and the nearest-pixel masks. HH and HV covariance power layers were extracted, converted to decibels for display and threshold calculations, and compared over the common 800 × 800 window using jointly valid samples.

Per-band low-HH candidate screens use each band’s 10th-percentile HH value as its threshold. A separate exploratory fusion score combines normalized HH/HV low-backscatter evidence from both bands. The notebook compares an equal-weight reference with an adaptive local-contrast-weighted score. In this implementation, local variability affects the channel weights; the resulting score is a relative screening index from 0 to 1, not a probability of water. Band-to-band candidate agreement and threshold sensitivity are also examined.

## Figures

- `scene_footprint.png` — ASF Data Search scene footprint in regional context; this is a screenshot, not a GIS-derived footprint.
- `backscatter_panels.png` — L-band and S-band HH/HV backscatter power previews.
- `band_candidates.png` — per-band low-HH and combined low-HH/low-HV screening previews.
- `adaptive_comparison.png` — equal-weight reference, local-contrast-weighted score, and their difference.
- `threshold_sensitivity.png` — low-HH candidates at the 5th, 10th, and 15th percentile cutoffs.
- `band_agreement.png` — L-band-only, S-band-only, and shared low-HH candidates at the 10th-percentile cutoffs.
- `area_summary.png` — area comparison for exploratory candidate definitions.

## Results available so far

The shared window contains 640,000 pixels at a nominal 10 m × 10 m spacing. The notebook reported 640,000 jointly valid pixels for the analyzed window. At the per-band 10th-percentile HH thresholds, the cutoffs were −15.02 dB for L-band and −14.51 dB for S-band. The two-band low-HH intersection contained 35,651 pixels, with a Jaccard overlap of 0.386.

The equal-weight score’s top 10% contained 64,000 pixels (6.4000 km²). The two-band low-HH consensus contained 35,651 pixels (3.5651 km²), and its overlap with the equal-weight top-decile contained 34,033 pixels (3.4033 km²). Areas are computed from the nominal 100 m² pixel area and describe threshold-selected candidates only.

## Inputs, outputs, and interpretation

The notebook reads source HDF5 data without modifying those source files. Optional GeoTIFF outputs are written to `~/Documents/AQUAFUSION_NISAR/OUTPUTS/` by the export workflow. The exported layers include an adaptive score and a categorical L-band/S-band HH agreement layer. The agreement layer uses codes 0 (neither band), 1 (L-band only), 2 (S-band only), and 3 (both bands). Confirm exact output filenames and metadata from the notebook's export-cell output.

Low radar backscatter can be associated with open water, but it can also result from other surfaces, local scene conditions, or radar effects. The thresholds are data-dependent, and the score has not been calibrated or independently validated. Do not report candidate pixel counts or areas as confirmed water area without validation against suitable reference data.

Only one acquisition date has been analyzed so far. The current work therefore does not measure temporal change. Change detection requires additional dates, consistent processing and co-registration, and an explicit change-analysis method. The current PNG figures and candidate GeoTIFFs should be described as preliminary screening products.

## References

1. NASA/ISRO Synthetic Aperture Radar (NISAR). *NISAR Level-2 GCOV Product Specification and User Guide* (cite the version accompanying the downloaded product).
2. Alaska Satellite Facility DAAC. *NISAR Data Search and product access*. https://search.asf.alaska.edu/
3. NASA/JPL. *NISAR mission*. https://nisar.jpl.nasa.gov/
4. NISAR L2 GCOV training materials, linked in the project’s ASF/mission documentation: https://github.com/dharmendraisro/nisar-polarimetric-training

## Code

Public notebook: https://github.com/JaisonPaulson/aquafusion-nisar-bihar-water-analysis/blob/main/00_AquaFusion_Data_Inspection.ipynb

# Mainshock-Discrimination-using-ML

Code and data for the paper *Trustworthy Mainshock Discrimination:
Calibration, Conformal Prediction, and Declustering-Window Sensitivity*.

The goal: given an earthquake, decide whether it's a mainshock or a
foreshock/aftershock — but do it in a way you can actually trust, with
calibrated probabilities, conformal prediction sets, and an honest look at
how much the "ground truth" depends on the declustering window you pick.

## Data

Everything is built from public catalogs, pulled straight from the APIs
(no third-party dumps):

- **Global** — USGS ComCat, 1930–2026, M≥4.0 (~542k events)
- **Southern California** — SCEDC, 1981–2026, M≥2.5 (~81k events)
- **Himalayan arc** — subset of the global catalog (20–32°N, 85–98°E)

Run `build_datasets.py` and it downloads, cleans, and homogenizes
magnitudes to Mw for you. Everything is cached, so it's safe to re-run.

## Pipeline

Scripts are numbered in run order. Each one skips work that's already done.

| Step | Script | What it does |
|------|--------|--------------|
| 1 | `build_datasets.py` | download + clean the three catalogs |
| 2 | `step3_labeling.py` | label events under 17 declustering windows |
| 3 | `step4_features.py` | backward-looking physics features |
| 4 | `step5_training.py` | splits, imputation, CatBoost/XGBoost/LightGBM |
| 5 | `step6_conformal.py` | calibration + split/Mondrian/adaptive conformal |
| 6 | `step7_sensitivity.py` | the (R,T) sensitivity grid + SHAP stability |
| 7 | `step8_decision.py` | decision layer + sequence replays |
| 8 | `step9_figures.py` | all the figures |
| 9 | `step10_transfer.py` | cross-region transfer to the Himalaya |
| 10 | `step11_ablation_stats.py` | ablation + DeLong + bootstrap CIs |
| 11 | `step12_baselines.py` | statistical baselines vs the model |

## Running it

I ran everything on Google Colab (free tier). Steps 4 and 6 are faster on
a GPU; the rest are fine on CPU.

```bash
pip install -r requirements.txt
python build_datasets.py
python step3_labeling.py
# ... and so on down the table
```

## Notes

- The catalogs are fetched live, so numbers may shift slightly as USGS
  revises older events.
- Raw data belongs to USGS (ComCat) and SCEDC/Caltech
  (doi:10.7909/C3WD3xH1) — please cite them if you use this.

## Citation

If this is useful, please cite the paper (details once published).

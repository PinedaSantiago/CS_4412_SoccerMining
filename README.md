# Architects of Play: Discovering Soccer Passing Archetypes in the EPL

**CS 4412 — Data Mining | Spring 2026**
**Author:** Santiago Pineda

---

## Project Overview

This project applies the KDD (Knowledge Discovery in Databases) process to discover latent passing archetypes in the English Premier League. Rather than relying on season-long averages or positional labels, we cluster at the **match level** to capture how players' passing roles shift based on tactical context.

Data is sourced from FBref via the `soccerdata` Python API, covering player-match passing records across the 2024–25 and 2025–26 EPL seasons.

---

## Discovery Questions

| # | Question |
|---|----------|
| DQ1 | What natural passing archetypes emerge from match-level player performances? |
| DQ2 | How do match contexts (opponent pressing intensity, game state) associate with different archetypes? |
| DQ3 | Which players exhibit archetype versatility across matches versus consistent specialization? |

---

## Milestones

| Milestone | Status | Description |
|-----------|--------|-------------|
| M1 | ✅ Complete | Project proposal, discovery questions, data plan |
| M2 | ✅ Complete | EDA, preprocessing, transformation, K-Means clustering |
| M3 | 🔲 Upcoming | Apriori association rule mining, final report |

---

## M2 Results Summary

### Discovered Archetypes (K-Means++, k=2, Silhouette=0.305)

| Archetype | N | % | Passes/90 | Prog. Ratio | Press Index | Top Positions |
|-----------|---|---|-----------|-------------|-------------|---------------|
| Deep Builder | 1,580 | 66.1% | 51.2 | 0.321 | 0.165 | CB, GK, CM |
| Direct Outlet | 810 | 33.9% | 35.0 | 0.469 | 0.310 | FW, LW, CAM |

### Key Findings
- **DQ1:** Two macro-archetypes emerge that are *not* equivalent to positional labels — a CM in a high-press system can behave as a Direct Outlet
- **DQ2:** High opponent pressing (PPDA < 9) reduces pass volume by ~5 passes/90 and shifts profiles toward more direct play
- **DQ3:** Players like A. Onana (CM) shift archetype by context; O. Watkins (FW) is a confirmed specialist

---

## Repository Structure

```
├── 01_data_extraction.py       # Data pull from FBref via soccerdata API
├── 02_eda.py                   # Exploratory data analysis (7 figures)
├── 03_preprocessing.py         # Imputation + Isolation Forest anomaly detection
├── 04_clustering.py            # StandardScaler + K-Means++ clustering (4 figures)
├── 05_build_notebook.py        # Assembles the Jupyter notebook
├── 06_build_pdf.py             # Generates the PDF summary
│
├── data/
│   ├── epl_passing_clean.csv   # Raw extracted dataset (2,516 instances)
│   ├── epl_preprocessed.csv    # Clean dataset after imputation + anomaly removal (2,390)
│   ├── epl_anomalies.csv       # Flagged anomalous instances (126)
│   └── epl_clustered.csv       # Final dataset with cluster + archetype labels
│
├── outputs/
│   ├── fig_01_distributions.png
│   ├── fig_02_positional_profiles.png
│   ├── fig_03_correlation_heatmap.png
│   ├── fig_04_context_effects.png
│   ├── fig_05_player_variance.png
│   ├── fig_06_data_quality.png
│   ├── fig_07_scatter_matrix.png
│   ├── fig_08_anomaly_viz.png
│   ├── fig_09_imputation.png
│   ├── fig_10_elbow_silhouette.png
│   ├── fig_11_cluster_profiles.png
│   ├── fig_12_cluster_scatter.png
│   ├── fig_13_archetype_context.png
│   ├── cluster_summary.csv
│   └── preprocessing_report.txt
│
└── CS4412_M2_Santiago_Pineda.ipynb   # Full M2 notebook (self-contained, outputs embedded)
```

---

## How to Run

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn reportlab
```

### Run the full pipeline in order

```bash
python 01_data_extraction.py    # generates data/epl_passing_clean.csv
python 02_eda.py                # generates fig_01 through fig_07
python 03_preprocessing.py      # generates fig_08, fig_09, preprocessed data
python 04_clustering.py         # generates fig_10 through fig_13, clustered data
python 05_build_notebook.py     # assembles CS4412_M2_Santiago_Pineda.ipynb
python 06_build_pdf.py          # generates CS4412_M2_Summary_Santiago_Pineda.pdf
```

Each script depends on the output of the previous. Running them in order from a clean directory fully reproduces all figures, datasets, and deliverables.

### Or just open the notebook

The notebook (`CS4412_M2_Santiago_Pineda.ipynb`) is fully self-contained with all outputs already embedded. Open it in Jupyter and all figures and results are visible immediately without running any cells.

---

## Features & Engineering

| Feature | Description | Source |
|---------|-------------|--------|
| `passes_att_p90` | Total attempted passes per 90 minutes | FBref match logs |
| `padj_passes` | Possession-adjusted pass volume | Engineered: `(raw × 0.50) / (poss% / 100)` |
| `prog_dist_ratio` | Progressive passing distance as % of total | FBref match logs |
| `press_index` | % of passes made under defensive pressure | FBref match logs |
| `opp_ppda` | Opponent pressing intensity (passes per defensive action) | FBref match logs |
| `score_delta` | Final goal difference from player's team perspective | FBref match logs |

---

## Preprocessing Decisions

| Step | Decision | Rationale |
|------|----------|-----------|
| Missing values | Median imputation by position group | Preserves positional profile distributions; MAR assumption |
| Anomaly detection | Isolation Forest (contamination=0.05) | Red card proxies (~4%) + blowout matches suggest ~5% upper bound |
| Scaling | StandardScaler (Z-score) | K-Means uses Euclidean distance; prevents high-range features from dominating |

---

## M3 Plan

M3 will apply **Apriori association rule mining** to formally quantify the relationship between match context and archetype adoption.

- Each match instance becomes a transaction of discretized context bins + archetype label
- Target rules: `{High_Press, Losing}` → `{Deep_Builder}`, `{Low_Press, Winning}` → `{Direct_Outlet}`
- Parameters: min support = 0.10, min confidence = 0.60, lift > 1.0

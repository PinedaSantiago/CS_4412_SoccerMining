# Architects of Play: Discovering Soccer Passing Archetypes in the EPL

**CS 4412 — Data Mining | Spring 2026**
**Author:** Santiago Pineda

---

## Project Overview

This project applies the KDD process to discover latent passing archetypes in the English Premier League. Rather than relying on season-long averages or positional labels, I clustered at the **match level** to capture how players' passing roles shift based on tactical context.

Data is sourced from FBref via the `soccerdata` Python API, covering player-match passing records across the 2024–25 and 2025–26 EPL seasons.

> **Sample size note:** This analysis covers 40 EPL players across 8 positions. Findings are internally consistent and statistically robust within this sample. Archetype percentages and player-specific conclusions should not be extrapolated to the full EPL squad without replication on the complete player pool.

---

## Discovery Questions

| # | Question |
|---|----------|
| DQ1 | What natural passing archetypes emerge from match-level player performances? |
| DQ2 | How do match contexts (opponent pressing intensity, game state) associate with different arch# Architects of Play: Discovering Soccer Passing Archetypes in the EPL

**CS 4412 — Data Mining | Spring 2026**
**Author:** Santiago Pineda

---

## Project Overview

This project applies the KDD process to discover latent passing archetypes in the English Premier League. Rather than relying on season-long averages or positional labels, I clustered at the **match level** to capture how players' passing roles shift based on tactical context.

Data is sourced from FBref via the `soccerdata` Python API, covering player-match passing records across the 2024–25 and 2025–26 EPL seasons.

> **Sample size note:** This analysis covers 40 EPL players across 8 positions. Findings are internally consistent and statistically robust within this sample. Archetype percentages and player-specific conclusions should not be extrapolated to the full EPL squad without replication on the complete player pool.

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
| M2 | ✅ Complete | EDA, preprocessing, transformation, K-Means clustering (k=2) |
| M3 | ✅ Complete | Extended clustering, Apriori rules, Decision Tree, Naive Bayes |
| M4 | 🔲 Upcoming | Polish, critical assessment, final report |

---

## M3 Results Summary

### Discovered Archetypes (K-Means++, k=4)

| Archetype | N | % | Passes/90 | Prog.Ratio | Press Idx | Top Positions |
|-----------|---|---|-----------|------------|-----------|---------------|
| Safety Valve | 671 | 28.1% | 39.6 | 0.266 | 0.112 | GK, CB, LB |
| Direct Outlet | 648 | 27.1% | 32.9 | 0.471 | 0.317 | FW, LW, CAM |
| Press Resistant | 503 | 21.0% | 49.7 | 0.423 | 0.255 | CM, CAM, RB |
| Deep Builder | 568 | 23.8% | 63.9 | 0.337 | 0.182 | CB, CM, RB |

### Key Findings

- **DQ1:** Four archetypes confirmed. `Passes/90` is the primary discriminator; `Press Index` and `Prog.Ratio` define the secondary splits. Naive Bayes accuracy = 97% confirms clusters are well-separated despite modest silhouette (0.24)
- **DQ2:** 25 association rules found. High press exposure + high verticality → Direct Outlet (lift=2.72). Very high volume → Deep Builder (lift=3.57). Pressing intensity is a stronger context driver than game state
- **DQ3:** GKs are the strongest specialists (95%+ Safety Valve). CMs (Onana, Rice) cross between Press Resistant and Deep Builder by context — confirmed versatile

---

## Repository Structure

```
├── 01_data_extraction.py       # Data pull from FBref via soccerdata API
├── 02_eda.py                   # Exploratory data analysis (7 figures)
├── 03_preprocessing.py         # Imputation + Isolation Forest anomaly detection
├── 04_clustering.py            # M2: StandardScaler + K-Means++ k=2
├── 05_build_notebook.py        # Assembles M2 Jupyter notebook
├── 06_build_pdf.py             # Generates M2 PDF summary
├── 07_extended_clustering.py   # M3: k=2..8 comparison, DBSCAN, Hierarchical
├── 08_association_rules.py     # M3: Apriori association rule mining
├── 09_classification.py        # M3: Decision Tree + Naive Bayes
├── 10_build_m3_notebook.py     # Assembles M3 Jupyter notebook
├── 11_build_m3_pdf.py          # Generates M3 PDF summary
│
├── data/
│   ├── epl_passing_clean.csv       # Raw extracted dataset (2,516 instances)
│   ├── epl_preprocessed.csv        # After imputation + anomaly removal (2,390)
│   ├── epl_anomalies.csv           # Flagged anomalous instances (126)
│   ├── epl_clustered.csv           # M2: k=2 cluster labels
│   └── epl_k4_clustered.csv        # M3: k=4 archetype labels
│
├── outputs/
│   ├── fig_01 – fig_13             # M2 EDA + clustering figures
│   ├── fig_14_k_extended.png       # M3: k=2..8 three-metric comparison
│   ├── fig_15_clustering_comparison.png  # K-Means vs DBSCAN vs Hierarchical
│   ├── fig_16_k4_profiles.png      # k=4 archetype characterization
│   ├── fig_17_association_rules.png# Support/confidence/lift scatter
│   ├── fig_18_top_rules.png        # Top rules by lift
│   ├── fig_19_decision_tree.png    # Shallow decision tree visualization
│   ├── fig_20_feature_importance.png # DT feature importances
│   ├── fig_21_naive_bayes.png      # NB class-conditional heatmap
│   ├── association_rules.csv       # Full rule table (25 rules)
│   └── cluster_summary.csv
│
├── CS4412_M2_Santiago_Pineda.ipynb
└── CS4412_M3_Santiago_Pineda.ipynb   # M3 notebook (self-contained, outputs embedded)
```

---

## How to Run

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy reportlab
```

### Run the full M3 pipeline

```bash
# M2 pipeline (required first)
python 01_data_extraction.py
python 02_eda.py
python 03_preprocessing.py
python 04_clustering.py

# M3 additions
python 07_extended_clustering.py    # Extended k analysis + algorithm comparison
python 08_association_rules.py      # Apriori rule mining
python 09_classification.py         # Decision Tree + Naive Bayes

# Build deliverables
python 10_build_m3_notebook.py
python 11_build_m3_pdf.py
```

The M3 notebook (`CS4412_M3_Santiago_Pineda.ipynb`) is fully self-contained with all 21 figures embedded. Open in Jupyter and all results are visible immediately.

---

## Features

| Feature | Description | Source |
|---------|-------------|--------|
| `passes_att_p90` | Attempted passes per 90 minutes | FBref |
| `padj_passes` | Possession-adjusted pass volume: `(raw × 0.50) / (poss%)` | Engineered |
| `prog_dist_ratio` | Progressive passing distance % of total | FBref |
| `press_index` | % of passes under defensive pressure | FBref |
| `opp_ppda` | Opponent pressing intensity | FBref |
| `score_delta` | Final goal difference (player's team) | FBref |

---

## M3 Technique Summary

| Technique | Parameters | Purpose |
|-----------|------------|---------|
| K-Means++ k=4 | n_init=50, random_state=42 | Primary archetype discovery |
| DBSCAN | eps=0.50, min_samples=15 | Algorithm comparison |
| Hierarchical Ward | k=4 | Algorithm comparison (ARI=0.38 vs K-Means) |
| Apriori | support=0.08, confidence=0.50, lift=1.2 | Context→archetype rules |
| Decision Tree | max_depth=3 (interpretable), depth=5 (accurate) | Interpretable rules |
| Naive Bayes | GaussianNB | Feature probability structure |

---

## M4 Plan

- Full-season archetype trajectory per player
- Expanded context variables (formation, opponent ranking)
- Critical assessment: which findings generalize beyond the 40-player sample?
- Publication-quality polish and final narrativeetypes? |
| DQ3 | Which players exhibit archetype versatility across matches versus consistent specialization? |

---

## Milestones

| Milestone | Status | Description |
|-----------|--------|-------------|
| M1 | ✅ Complete | Project proposal, discovery questions, data plan |
| M2 | ✅ Complete | EDA, preprocessing, transformation, K-Means clustering (k=2) |
| M3 | ✅ Complete | Extended clustering, Apriori rules, Decision Tree, Naive Bayes |
| M4 | 🔲 Upcoming | Polish, critical assessment, final report |

---

## M3 Results Summary

### Discovered Archetypes (K-Means++, k=4)

| Archetype | N | % | Passes/90 | Prog.Ratio | Press Idx | Top Positions |
|-----------|---|---|-----------|------------|-----------|---------------|
| Safety Valve | 671 | 28.1% | 39.6 | 0.266 | 0.112 | GK, CB, LB |
| Direct Outlet | 648 | 27.1% | 32.9 | 0.471 | 0.317 | FW, LW, CAM |
| Press Resistant | 503 | 21.0% | 49.7 | 0.423 | 0.255 | CM, CAM, RB |
| Deep Builder | 568 | 23.8% | 63.9 | 0.337 | 0.182 | CB, CM, RB |

### Key Findings

- **DQ1:** Four archetypes confirmed. `Passes/90` is the primary discriminator; `Press Index` and `Prog.Ratio` define the secondary splits. Naive Bayes accuracy = 97% confirms clusters are well-separated despite modest silhouette (0.24)
- **DQ2:** 25 association rules found. High press exposure + high verticality → Direct Outlet (lift=2.72). Very high volume → Deep Builder (lift=3.57). Pressing intensity is a stronger context driver than game state
- **DQ3:** GKs are the strongest specialists (95%+ Safety Valve). CMs (Onana, Rice) cross between Press Resistant and Deep Builder by context — confirmed versatile

---

## Repository Structure

```
├── 01_data_extraction.py       # Data pull from FBref via soccerdata API
├── 02_eda.py                   # Exploratory data analysis (7 figures)
├── 03_preprocessing.py         # Imputation + Isolation Forest anomaly detection
├── 04_clustering.py            # M2: StandardScaler + K-Means++ k=2
├── 05_build_notebook.py        # Assembles M2 Jupyter notebook
├── 06_build_pdf.py             # Generates M2 PDF summary
├── 07_extended_clustering.py   # M3: k=2..8 comparison, DBSCAN, Hierarchical
├── 08_association_rules.py     # M3: Apriori association rule mining
├── 09_classification.py        # M3: Decision Tree + Naive Bayes
├── 10_build_m3_notebook.py     # Assembles M3 Jupyter notebook
├── 11_build_m3_pdf.py          # Generates M3 PDF summary
│
├── data/
│   ├── epl_passing_clean.csv       # Raw extracted dataset (2,516 instances)
│   ├── epl_preprocessed.csv        # After imputation + anomaly removal (2,390)
│   ├── epl_anomalies.csv           # Flagged anomalous instances (126)
│   ├── epl_clustered.csv           # M2: k=2 cluster labels
│   └── epl_k4_clustered.csv        # M3: k=4 archetype labels
│
├── outputs/
│   ├── fig_01 – fig_13             # M2 EDA + clustering figures
│   ├── fig_14_k_extended.png       # M3: k=2..8 three-metric comparison
│   ├── fig_15_clustering_comparison.png  # K-Means vs DBSCAN vs Hierarchical
│   ├── fig_16_k4_profiles.png      # k=4 archetype characterization
│   ├── fig_17_association_rules.png# Support/confidence/lift scatter
│   ├── fig_18_top_rules.png        # Top rules by lift
│   ├── fig_19_decision_tree.png    # Shallow decision tree visualization
│   ├── fig_20_feature_importance.png # DT feature importances
│   ├── fig_21_naive_bayes.png      # NB class-conditional heatmap
│   ├── association_rules.csv       # Full rule table (25 rules)
│   └── cluster_summary.csv
│
├── CS4412_M2_Santiago_Pineda.ipynb
└── CS4412_M3_Santiago_Pineda.ipynb   # M3 notebook (self-contained, outputs embedded)
```

---

## How to Run

### Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy reportlab
```

### Run the full M3 pipeline

```bash
# M2 pipeline (required first)
python 01_data_extraction.py
python 02_eda.py
python 03_preprocessing.py
python 04_clustering.py

# M3 additions
python 07_extended_clustering.py    # Extended k analysis + algorithm comparison
python 08_association_rules.py      # Apriori rule mining
python 09_classification.py         # Decision Tree + Naive Bayes

# Build deliverables
python 10_build_m3_notebook.py
python 11_build_m3_pdf.py
```

The M3 notebook (`CS4412_M3_Santiago_Pineda.ipynb`) is fully self-contained with all 21 figures embedded. Open in Jupyter and all results are visible immediately.

---

## Features

| Feature | Description | Source |
|---------|-------------|--------|
| `passes_att_p90` | Attempted passes per 90 minutes | FBref |
| `padj_passes` | Possession-adjusted pass volume: `(raw × 0.50) / (poss%)` | Engineered |
| `prog_dist_ratio` | Progressive passing distance % of total | FBref |
| `press_index` | % of passes under defensive pressure | FBref |
| `opp_ppda` | Opponent pressing intensity | FBref |
| `score_delta` | Final goal difference (player's team) | FBref |

---

## M3 Technique Summary

| Technique | Parameters | Purpose |
|-----------|------------|---------|
| K-Means++ k=4 | n_init=50, random_state=42 | Primary archetype discovery |
| DBSCAN | eps=0.50, min_samples=15 | Algorithm comparison |
| Hierarchical Ward | k=4 | Algorithm comparison (ARI=0.38 vs K-Means) |
| Apriori | support=0.08, confidence=0.50, lift=1.2 | Context→archetype rules |
| Decision Tree | max_depth=3 (interpretable), depth=5 (accurate) | Interpretable rules |
| Naive Bayes | GaussianNB | Feature probability structure |

---

## M4 Plan

- Full-season archetype trajectory per player
- Expanded context variables (formation, opponent ranking)
- Critical assessment: which findings generalize beyond the 40-player sample?
- Publication-quality polish and final narrative

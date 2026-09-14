# Data Provenance and Reproduction Guide

## Reliability-Aware Rank Aggregation for Deployment-Oriented Predictive-Maintenance Model Selection

This document describes the datasets used in the RDRA research project, their methodological roles, acquisition procedures, preprocessing, generated artefacts and relationship to the final computational analysis.

The project uses two principal predictive-maintenance datasets:

1. **AI4I 2020 Predictive Maintenance Dataset**
2. **NASA C-MAPSS FD001**

The two datasets have deliberately different research roles.

AI4I is the primary complete RDRA development and validation environment.

NASA C-MAPSS FD001 provides complementary cross-task evidence in a sequential Remaining Useful Life prediction setting.

---

## 1. Data Flow Overview

The primary computational data flow is:

```text
AI4I 2020
    ↓
Notebook 1
    ↓
ai4i_ipmef_final_outputs/
    ↓
Notebook 6
    ↓
novelty_rank_regret_outputs_revised/
```

The complementary C-MAPSS flow is:

```text
NASA C-MAPSS FD001
    ↓
Notebook 4
    ↓
cmapss_rul_ipmef_outputs/
    ↓
Notebook 6
```

The supplementary anomaly-triggered branch is:

```text
NASA C-MAPSS FD001
    ↓
Notebook 4
    ↓
cmapss_rul_ipmef_outputs/
    ↓
Notebook 5
    ↓
cmapss_anomaly_triggered_ipmef_outputs/
```

---

# 2. AI4I 2020 Predictive Maintenance Dataset

## 2.1 Role in the Research

AI4I 2020 is the primary dataset used for complete RDRA development and validation.

The dataset supports binary machine-failure classification.

It provides the repeated experimental evidence required to construct and validate five deployment dimensions:

1. Predictive Quality
2. Robustness
3. Probability Reliability
4. Computational Efficiency
5. Operational Impact

AI4I supports:

- candidate-model evaluation;
- repeated experimentation;
- class-imbalance handling;
- probability calibration;
- cost-sensitive threshold selection;
- controlled sensor corruption;
- robustness analysis;
- criterion-specific ranking;
- LORO reliability estimation;
- empirical reliability-weight construction;
- Equal Borda;
- primary RDRA;
- VQ1;
- VQ2; and
- VQ3.

---

## 2.2 Source

Notebook 1 retrieves AI4I from the UCI Machine Learning Repository.

The configured source is:

```text
https://archive.ics.uci.edu/ml/machine-learning-databases/00601/ai4i2020.csv
```

Dataset reference:

```text
S. Matzka,
"AI4I 2020 Predictive Maintenance Dataset,"
UCI Machine Learning Repository, 2020.
DOI: 10.24432/C5HS5C
```

Because the source dataset is retrieved directly by Notebook 1, the raw AI4I CSV does not need to be duplicated inside the repository for the computational workflow.

---

## 2.3 AI4I Candidate Configuration

The primary AI4I experiment evaluates two model families:

```text
Random Forest
XGBoost
```

Three imbalance-handling strategies are evaluated:

```text
None
Class Weighting
SMOTE
```

Three probability-calibration strategies are evaluated:

```text
None
Platt Calibration
Isotonic Calibration
```

The resulting configuration space contains:

2 × 3 × 3 = **18** candidate configurations.

---

## 2.4 Experimental Repetitions

The final Notebook 1 experiment uses 20 random seeds:

```text
42
100
2024
1234
9999
7
21
77
314
2025
11
55
88
144
512
777
909
1337
2048
4096
```

Repeated experimentation is essential to the RDRA methodology because criterion reliability is estimated from the reproducibility of candidate rankings across repetitions.

If the evidence were averaged immediately into one value per candidate, repetition-level ranking variation would be lost and empirical reliability could not be estimated.

---

## 2.5 AI4I Operational-Cost Assumptions

The Notebook 1 experimental configuration uses analytical asymmetric error penalties:

```text
False-positive penalty: 1.0
False-negative penalty: 10.0
```

These values are analytical scenario assumptions used for comparative evaluation.

They should not be interpreted as measured industrial monetary costs.

---

## 2.6 AI4I Controlled Corruption Protocol

The primary AI4I robustness experiment evaluates four controlled data-quality corruption families.

### Gaussian Noise

Severity levels:

```text
0.05
0.10
0.20
0.30
```

### MCAR Missingness

Severity levels:

```text
0.05
0.10
0.20
0.30
```

### Static Sensor Offset

Severity levels:

```text
0.05
0.10
0.20
0.30
```

### Outlier Contamination

Severity levels:

```text
0.01
0.03
0.05
0.10
```

The outlier magnitude is configured relative to training-feature standard deviation.

The static sensor offset condition represents a **static sensor bias**.

It must not be interpreted as gradual temporal drift.

---

## 2.7 AI4I Corruption Repetition

The configured number of corruption repetitions is:

```text
5
```

for stochastic corruption conditions.

Static Sensor Offset is deterministic within the implemented condition and is not repeated in the same way as the stochastic corruption procedures.

---

## 2.8 AI4I Generated Output

Running:

```text
notebooks/01_AI4I_IPMEF_Final_Updated.ipynb
```

generates:

```text
ai4i_ipmef_final_outputs/
```

The reference output is retained in this repository under:

```text
outputs/ai4i_ipmef_final_outputs/
```

Principal files include:

```text
01_clean_candidate_results.csv
02_robustness_raw_results.csv
03_robustness_by_level.csv
04_condition_auretc_by_seed.csv
05_composite_robustness_by_seed.csv
06_composite_robustness_summary.csv
07_candidate_summary.csv
08_rq1_conventional_vs_deployment_ranking.csv
09_rq1_summary.csv
10_rq3_pareto_candidates.csv
11_rq3_preliminary_fixed_weight_baseline.csv
12_framework_report.json
```

The folder also contains generated figures and model-related artefacts.

---

## 2.9 AI4I Files Required by Notebook 6

Notebook 6 directly consumes:

```text
ai4i_ipmef_final_outputs/01_clean_candidate_results.csv

ai4i_ipmef_final_outputs/02_robustness_raw_results.csv

ai4i_ipmef_final_outputs/05_composite_robustness_by_seed.csv

ai4i_ipmef_final_outputs/07_candidate_summary.csv
```

These files provide both final candidate-level evidence and repetition-level information required by the final RDRA analysis.

---

## 2.10 AI4I Data Limitations

AI4I is a synthetic predictive-maintenance benchmark.

It provides a useful controlled environment for repeated experimentation and data-quality perturbation, but it does not reproduce every characteristic of a live industrial maintenance system.

The empirical reliability weights produced by the experiment are therefore specific to the implemented:

- dataset;
- candidate configurations;
- model-training procedure;
- metric definitions;
- corruption protocol;
- deployment dimensions; and
- experimental repetitions.

They should not be interpreted as universal reliability values.

---

# 3. NASA C-MAPSS FD001

## 3.1 Role in the Research

NASA C-MAPSS FD001 is used as a complementary predictive-maintenance benchmark.

The task is sequential **Remaining Useful Life prediction**.

Candidate models include:

```text
Ridge Regression
Random Forest
XGBoost
LSTM
GRU
```

The current C-MAPSS analysis supports three deployment dimensions:

```text
Predictive Quality
Robustness
Computational Efficiency
```

C-MAPSS is used to investigate deployment-ranking behaviour in a substantially different predictive-maintenance setting from AI4I.

---

## 3.2 Dataset Acquisition

Notebook 4 is configured to download the NASA turbofan degradation archive from:

```text
https://phm-datasets.s3.amazonaws.com/NASA/6.+Turbofan+Engine+Degradation+Simulation+Data+Set.zip
```

The outer archive is stored locally as:

```text
CMAPSSData.zip
```

and extracted to:

```text
CMAPSSData/
```

The downloaded archive contains an inner:

```text
CMAPSSData.zip
```

which is also extracted by the notebook.

If automatic download is unavailable, the notebook can use an uploaded copy of the NASA archive.

---

## 3.3 Required FD001 Files

The FD001 experiment requires:

```text
train_FD001.txt
test_FD001.txt
RUL_FD001.txt
```

Notebook 4 searches the extracted C-MAPSS archive for these files and stops with an error if they cannot be located.

---

## 3.4 C-MAPSS Experimental Configuration

The primary implementation uses:

```text
Dataset subset:             FD001
Temporal window length:     30 cycles
RUL cap:                    125 cycles
Validation fraction:        0.20
Primary model seed:         42
Model seeds:                [42]
Corruption repeats:         5
Latency warm-up runs:       5
Latency repeated runs:      30
Single-sample repetitions:  100
```

The current C-MAPSS implementation is therefore not a 20-seed replication of the AI4I experiment.

This is one reason C-MAPSS is treated as complementary evidence rather than a second complete RDRA validation.

---

## 3.5 C-MAPSS Preprocessing

The C-MAPSS preprocessing workflow includes:

1. loading the standard FD001 files;
2. constructing engine-level Remaining Useful Life targets;
3. applying capped piecewise-linear RUL;
4. separating training and validation engines;
5. identifying and removing near-constant sensors using training data;
6. fitting the standardisation procedure using training data only;
7. constructing temporal windows;
8. preserving engine-level separation between training and validation;
9. producing train, validation and test sequences; and
10. retaining preprocessing metadata for subsequent reproduction.

The engine-level split prevents temporal windows belonging to the same engine from being divided between training and validation data.

---

## 3.6 C-MAPSS Models

The RUL experiment evaluates:

```text
Ridge Regression
Random Forest Regressor
XGBoost Regressor
LSTM
GRU
```

The evaluation includes:

- RMSE;
- MAE;
- NASA asymmetric scoring;
- training time;
- inference latency;
- throughput; and
- model size.

---

## 3.7 C-MAPSS Controlled Degradation

The C-MAPSS robustness analysis includes sequence-oriented degradation conditions such as:

```text
Gaussian Noise
Contiguous Missing-Data Blocks
Sparse Outlier Spikes
Gradual Sensor-Offset Drift
Sensor Freezing
```

These conditions differ from the static AI4I corruption design because C-MAPSS contains temporal degradation sequences.

The analysis evaluates changes in:

- RMSE;
- RMSE retention;
- NASA-score behaviour;
- robustness evidence; and
- deployment ranking.

---

## 3.8 C-MAPSS Generated Output

Running:

```text
notebooks/04_CMAPSS_RUL_IPMEF_Updated.ipynb
```

generates:

```text
cmapss_rul_ipmef_outputs/
```

The reference folder is retained in the repository as:

```text
outputs/cmapss_rul_ipmef_outputs/
```

The folder contains:

- clean candidate results;
- raw corruption results;
- candidate summaries;
- robustness summaries;
- figures;
- processed sequence data;
- preprocessing metadata;
- scaler objects;
- trained model artefacts;
- experiment configuration; and
- execution-environment information.

---

## 3.9 C-MAPSS Files Required by Notebook 6

Notebook 6 directly consumes:

```text
cmapss_rul_ipmef_outputs/02_robustness_raw.csv

cmapss_rul_ipmef_outputs/06_candidate_summary.csv
```

These files provide the evidence used by the complementary C-MAPSS ranking-responsiveness analysis.

---

## 3.10 C-MAPSS Methodological Scope

C-MAPSS does not contain the same repeated five-dimensional experimental structure used for complete AI4I RDRA estimation.

It therefore does not produce an independent five-dimension empirical reliability-weight vector.

The appropriate interpretation is:

> **C-MAPSS FD001 provides complementary cross-task deployment-rank responsiveness evidence.**

It should not be described as:

> **a second complete independent RDRA validation.**

---

# 4. C-MAPSS Anomaly-Triggered Extension

Notebook 5 consumes the C-MAPSS artefacts generated by Notebook 4.

Run:

```text
notebooks/05_CMAPSS_Anomaly_Triggered_IPMEF_Updated.ipynb
```

The resulting output is:

```text
cmapss_anomaly_triggered_ipmef_outputs/
```

The reference output is retained under:

```text
outputs/cmapss_anomaly_triggered_ipmef_outputs/
```

The supplementary branch evaluates:

- Isolation Forest anomaly detection;
- LSTM Autoencoder anomaly detection;
- Hybrid Detector behaviour;
- periodic safeguard conditions;
- critical-state coverage;
- warning-state coverage;
- false-trigger behaviour;
- downstream invocation reduction;
- missed-critical-window rates;
- latency;
- RUL RMSE;
- RUL MAE; and
- statistical comparison.

This branch is retained as a research-development artefact.

It is not required to reproduce the principal final Notebook 6 RDRA evidence.

---

# 5. Final Notebook 6 Data Dependencies

Notebook 6 defines the following upstream paths:

```python
AI4I_BASE = Path("ai4i_ipmef_final_outputs")
CMAPSS_BASE = Path("cmapss_rul_ipmef_outputs")
PIPE_BASE = Path("cmapss_anomaly_triggered_ipmef_outputs")
```

The final output path is:

```python
OUT = Path("novelty_rank_regret_outputs_revised")
```

The principal Notebook 6 workflow requires:

```text
ai4i_ipmef_final_outputs/
cmapss_rul_ipmef_outputs/
```

Although the anomaly-triggered path is defined in Notebook 6, the principal final RDRA computation does not depend on the Notebook 5 anomaly-triggered output.

---

# 6. Final RDRA Output

Running:

```text
notebooks/06_RDRA_Revised_Methodology_Aligned_Refined_FINAL.ipynb
```

generates:

```text
novelty_rank_regret_outputs_revised/
```

The reference final output is retained in:

```text
outputs/novelty_rank_regret_outputs_revised/
```

This folder contains the principal computational evidence for:

- empirical criterion-reliability estimation;
- empirical reliability weights;
- Equal Borda;
- primary RDRA;
- Existing Composite comparison;
- VQ1 criterion uncertainty;
- VQ1 statistical validation;
- reliability-weight sensitivity;
- VQ2 data-quality responsiveness;
- matched-bootstrap responsiveness comparison;
- VQ3 preference uncertainty;
- Pareto and regret analysis;
- exploratory reliability-influence refinement;
- complementary C-MAPSS analysis; and
- final figures and reports.

---

# 7. Reproduction Sequence

The minimum sequence required to rebuild the final computational evidence is:

```text
1. Obtain AI4I 2020
       ↓
2. Run Notebook 1
       ↓
3. Generate ai4i_ipmef_final_outputs/

4. Obtain NASA C-MAPSS FD001
       ↓
5. Run Notebook 4
       ↓
6. Generate cmapss_rul_ipmef_outputs/

7. Make both upstream folders available to Notebook 6
       ↓
8. Run Notebook 6
       ↓
9. Generate novelty_rank_regret_outputs_revised/
```

Notebooks 2, 3 and 5 are retained as supporting or historical research artefacts but are not mandatory dependencies of the principal final RDRA workflow.

---

# 8. Reproducibility Controls

The project uses several controls to improve experimental reproducibility and reduce leakage.

These include:

- a fixed 20-seed AI4I experiment;
- preservation of repetition-level evidence;
- consistent candidate definitions;
- training-based preprocessing;
- engine-level C-MAPSS train/validation separation;
- training-only standardisation;
- tie-aware candidate ranking;
- paired VQ1 criterion-subset comparison;
- matched VQ2 corruption scenarios;
- fixed clean-data normalisation for corruption analysis;
- matched VQ3 preference vectors;
- a fixed final analytical random seed for randomised Notebook 6 procedures; and
- locking of the exploratory reliability-influence configuration before subsequent validation interpretation.

The Notebook 6 analytical seed is:

```python
RNG_SEED = 20260811
```

---

# 9. Generated Data Versus Source Data

The repository distinguishes between:

### Original source datasets

```text
AI4I 2020
NASA C-MAPSS FD001
```

and:

### Research-generated evidence

```text
outputs/ai4i_ipmef_final_outputs/

outputs/cmapss_rul_ipmef_outputs/

outputs/cmapss_anomaly_triggered_ipmef_outputs/

outputs/novelty_rank_regret_outputs_revised/
```

The generated output folders represent experimental and analytical artefacts produced during this research project.

---

# 10. Data Summary

| Data Source | Prediction Task | Research Role | Complete RDRA? |
|---|---|---|---|
| AI4I 2020 | Machine-failure classification | Primary development and VQ1–VQ3 validation | Yes |
| NASA C-MAPSS FD001 | Remaining Useful Life prediction | Complementary cross-task responsiveness evidence | No |
| C-MAPSS anomaly-triggered artefacts | Conditional RUL invocation | Supplementary exploratory extension | No |

---

# 11. Related Documentation

The detailed methodological interpretation of the datasets is provided in:

```text
docs/Literature_Review.pdf

docs/Research_Methodology.pdf

docs/Research_Implementation.pdf

docs/Research_Analysis.pdf
```

The complete project handover description is provided in:

```text
SIT747_Research_Development_Artefacts_Handover_Report.pdf
```

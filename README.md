# Reliability-Aware Rank Aggregation for Deployment-Oriented Predictive-Maintenance Model Selection

## SIT747 Research & Development Artefacts Handover Repository

This repository contains the research and development artefacts produced for the project **Reliability-Aware Rank Aggregation for Deployment-Oriented Predictive-Maintenance Model Selection**.

The central methodology developed in the project is **Reliability-Aware Deployment Rank Aggregation (RDRA)**.

The repository preserves the computational notebooks, experimental outputs, research documentation, dataset provenance and rebuilding information required to understand, reproduce and extend the project.

---

## 1. Project Overview

Predictive-maintenance model selection involves more than identifying the model with the highest conventional predictive-performance score.

A candidate model may achieve strong predictive performance while exhibiting weaker behaviour in other deployment-relevant areas, including:

- robustness to degraded sensor data;
- probability reliability and calibration;
- computational efficiency; and
- operational consequences of prediction errors.

The final research therefore treats predictive-maintenance deployment as a **multi-dimensional model-selection problem**.

Five deployment dimensions are operationalised in the primary AI4I experiment:

1. **Predictive Quality**
2. **Robustness**
3. **Probability Reliability**
4. **Computational Efficiency**
5. **Operational Impact**

These dimensions may produce conflicting rankings of the same candidate model configurations.

The research investigates whether differences in the empirical reproducibility of these criterion-specific rankings should influence their contribution to a final deployment-oriented consensus.

---

## 2. Research Objective

The principal research objective is:

> **To develop and empirically validate a reliability-aware rank-aggregation methodology for deployment-oriented predictive-maintenance model selection that accounts for differences in the stability of evaluation evidence and produces a defensible consensus ranking under evaluation uncertainty.**

---

## 3. Main Research Question

The final Main Research Question is:

> **How can reliability-aware rank aggregation produce a robust and defensible consensus ranking of predictive-maintenance model configurations when deployment criteria provide conflicting and differently stable empirical evidence?**

Three validation questions examine complementary properties of the proposed methodology.

### VQ1 — Criterion Uncertainty: Consistency

> **Does reliability-aware aggregation improve consensus-ranking consistency when the available deployment criteria vary?**

VQ1 evaluates whether the consensus ranking remains reasonably consistent when different subsets of deployment criteria are available.

### VQ2 — Data-Quality Uncertainty: Responsiveness

> **Does the consensus ranking respond appropriately when meaningful changes occur in the underlying deployment evidence due to sensor degradation?**

VQ2 evaluates whether ranking changes correspond to meaningful changes in deployment evidence rather than measuring stability alone.

### VQ3 — Preference Uncertainty: Decision Usefulness

> **How useful is the resulting consensus ranking for downstream deployment decisions when deployment priorities vary?**

VQ3 evaluates whether the resulting candidate shortlist remains useful across changing stakeholder-preference scenarios.

---

## 4. Reliability-Aware Deployment Rank Aggregation

The central conceptual distinction in RDRA is:

**Empirical Reliability Weight ≠ Stakeholder Preference Weight**

An empirical reliability weight represents the **reproducibility of a criterion-specific candidate ranking across repeated experimental evidence**.

It does not represent the subjective importance of the criterion.

For example, a larger Computational Efficiency reliability weight does not imply that computational efficiency is universally more important than Operational Impact. It indicates only that the candidate ordering produced by the Computational Efficiency evidence was more reproducible under the implemented experimental protocol.

The core RDRA workflow is:

```text
Repeated Experimental Evidence
        ↓
Deployment-Dimension Scores
        ↓
Criterion-Specific Candidate Rankings
        ↓
Leave-One-Repetition-Out Reliability Estimation
        ↓
Empirical Reliability Weights
        ↓
Normalised Borda Utilities
        ↓
Reliability-Weighted Aggregation
        ↓
RDRA Consensus Ranking
```

---

## 5. Mathematical Formulation

For candidate *i* and deployment criterion *j*, let:

**r<sub>ij</sub>**

represent the criterion-specific rank.

Rank 1 represents the most desirable candidate under that criterion.

The criterion rank is transformed into a normalised Borda utility:

\[ B_{ij} = \frac{n - r_{ij}}{n - 1} \]

where *n* is the number of candidate configurations.

---

### Empirical Reproducibility (LORO)

The empirical reproducibility of each criterion ranking is estimated using a study-specific **Leave-One-Repetition-Out (LORO)** procedure.

For each criterion *j* and experimental repetition *r*:

1. repetition *r* is held out  
2. a reference candidate ranking is constructed from the remaining repetitions  
3. the held-out candidate ranking is compared with the reference ranking  
4. Kendall rank agreement is calculated  

The criterion stability estimate is:

**s<sub>j</sub> = (1 / R) · Σ<sub>r=1..R</sub> max(0, τ<sub>jr</sub>)**

where:

- *R* is the number of experimental repetitions  
- τ<sub>jr</sub> is the Kendall agreement for criterion *j* and repetition *r*  

Negative Kendall agreement is clipped to zero because inverse ordering does not constitute positive evidence of reproducibility.

---

### Primary Empirical Reliability Weight

**w<sub>j</sub> = s<sub>j</sub> / Σ<sub>k</sub> s<sub>k</sub>**

---

### Final RDRA Score

**S<sub>i</sub> = Σ<sub>j</sub> w<sub>j</sub> · B<sub>ij</sub>**

Candidates are ranked in descending order of **S<sub>i</sub>**.

If no positive reproducibility evidence is available across the criteria, the implementation falls back to equal weighting.


---

## 6. Equal Borda Baseline

Equal Borda is retained as the principal controlled rank-aggregation baseline.

For \(m\) deployment criteria:

\[
w_j^{B} = \frac{1}{m}.
\]

With five deployment dimensions:

\[
w_j^{B} = 0.20.
\]

Equal Borda and RDRA use the same criterion rankings and normalised Borda transformation.

The principal methodological difference is therefore:

```text
Equal Borda:
All criterion rankings receive equal influence.

RDRA:
Criterion influence is proportional to empirical ranking reproducibility.
```

---

## 7. Primary and Exploratory RDRA

The **primary RDRA formulation** corresponds to:

γ = 1

with:

\[
w_j^{(1)}
=
\frac{s_j}
{\sum_k s_k}.
\]

An exploratory reliability-influence formulation is also evaluated:

\[
w_j(\gamma)
=
\frac{s_j^\gamma}
{\sum_k s_k^\gamma}.
\]

Under this formulation:

- \(\gamma=0\) corresponds to the Equal Borda limit;
- \(\gamma=1\) corresponds to primary RDRA; and
- \(\gamma>1\) increases the relative influence of differences in empirical reliability.

A development-only search selected:

\[
\gamma=3.0.
\]

This configuration was locked before subsequent VQ2/VQ3 interpretation.

The selected value occurs at the upper boundary of the predefined development grid and is therefore **not interpreted as globally optimal**.

The primary proposed RDRA method remains the reliability-proportional formulation at:

\[
\gamma=1.
\]

---

## 8. Repository Structure

```text
RDRA-Research-Project/
│
├── README.md
├── README_DATA.md
├── requirements.txt
├── SIT747_Research_Development_Artefacts_Handover_Report.pdf
│
├── notebooks/
│   ├── 01_AI4I_IPMEF_Final.ipynb
│   ├── 02_AI4I_Statistical_Validation.ipynb
│   ├── 03_AI4I_Robustness_and_Decision_Extension.ipynb
│   ├── 04_CMAPSS_RUL_IPMEF.ipynb
│   ├── 05_CMAPSS_Anomaly_Triggered_IPMEF.ipynb
│   └── 06_RDRA_Methodology.ipynb
│
├── outputs/
│   ├── ai4i_ipmef_final_outputs/
│   ├── cmapss_rul_ipmef_outputs/
│   ├── cmapss_anomaly_triggered_ipmef_outputs/
│   └── novelty_rank_regret_outputs_revised/
│
└── docs/
    ├── Research_Progress_Check_Point_1.pdf
    ├── Research_Progress_Check_Point_2.pdf
    ├── Literature_Review.pdf
    ├── Research_Methodology.pdf
    ├── Research_Progress_Check_Point_3.pdf
    ├── Research_Implementation.pdf
    └── Research_Analysis.pdf
```

---

## 9. Notebook Inventory

### 9.1 Notebook 1 — AI4I Experimental Evidence

```text
notebooks/01_AI4I_IPMEF_Final_Updated.ipynb
```

**Role:** Core upstream AI4I experimental-evidence generator.

The notebook evaluates:

- Random Forest;
- XGBoost;
- no imbalance handling;
- class weighting;
- SMOTE;
- no probability calibration;
- Platt calibration;
- isotonic calibration;
- cost-sensitive threshold selection;
- predictive quality;
- probability reliability;
- computational efficiency;
- operational impact;
- controlled sensor corruption;
- robustness retention;
- deployment-oriented ranking;
- Pareto analysis; and
- representative SHAP explainability.

The configuration space contains:

\[
2 \times 3 \times 3 = 18
\]

candidate configurations.

The final implementation uses 20 experimental seeds.

The principal output directory is:

```text
outputs/ai4i_ipmef_final_outputs/
```

This output provides the primary upstream evidence consumed by Notebook 6.

---

### 9.2 Notebook 2 — AI4I Statistical Validation

```text
notebooks/02_AI4I_Statistical_Validation_Updated.ipynb
```

**Role:** Supporting historical statistical-validation artefact.

The notebook includes:

- controlled ablation analysis;
- bootstrap confidence intervals;
- paired statistical testing;
- Wilcoxon signed-rank tests;
- Holm multiple-comparison correction;
- effect-size analysis;
- probability-calibration analysis;
- reliability diagrams; and
- automatic reporting.

Notebook 2 is retained as research-development provenance.

It is not a mandatory dependency of the final Notebook 6 workflow.

---

### 9.3 Notebook 3 — AI4I Robustness and Decision Extension

```text
notebooks/03_AI4I_Robustness_and_Decision_Extension_Updated.ipynb
```

**Role:** Historical and supporting methodological extension.

The notebook contains earlier work on:

- criterion-level ranking interpretation;
- corruption sensitivity;
- leave-one-corruption-out analysis;
- corruption-weight sensitivity;
- severity sensitivity;
- robustness-winner stability;
- feasibility constraints;
- Pareto filtering;
- preference sensitivity;
- downstream decision analysis; and
- opportunity-cost interpretation.

It documents an intermediate research stage before the final consolidation around one Main Research Question and VQ1–VQ3.

---

### 9.4 Notebook 4 — C-MAPSS RUL Evidence

```text
notebooks/04_CMAPSS_RUL_IPMEF_Updated.ipynb
```

**Role:** Core upstream C-MAPSS FD001 experimental-evidence generator.

Candidate model families include:

- Ridge Regression;
- Random Forest;
- XGBoost;
- LSTM;
- GRU.

The notebook evaluates:

- RMSE;
- MAE;
- NASA asymmetric scoring;
- training time;
- inference latency;
- throughput;
- model size;
- Gaussian-noise robustness;
- contiguous missing-data blocks;
- sparse outlier spikes;
- gradual sensor-offset drift; and
- sensor freezing.

The principal output directory is:

```text
outputs/cmapss_rul_ipmef_outputs/
```

This output supplies the complementary C-MAPSS evidence used by Notebook 6.

---

### 9.5 Notebook 5 — C-MAPSS Anomaly-Triggered Extension

```text
notebooks/05_CMAPSS_Anomaly_Triggered_IPMEF_Updated.ipynb
```

**Role:** Supplementary exploratory research artefact.

The notebook evaluates anomaly-triggered RUL prediction using:

- Isolation Forest;
- LSTM Autoencoder;
- Hybrid Detector;
- periodic safeguards;
- critical-state coverage;
- warning-state coverage;
- false triggers;
- downstream invocation reduction;
- missed-critical-window rate;
- estimated end-to-end latency;
- RUL RMSE and MAE; and
- statistical comparison.

The output directory is:

```text
outputs/cmapss_anomaly_triggered_ipmef_outputs/
```

This branch is retained as a supplementary research artefact.

It is not required to reproduce the principal final RDRA results.

---

### 9.6 Notebook 6 — Final RDRA Computational Artefact

```text
notebooks/06_RDRA_Revised_Methodology_Aligned_Refined_FINAL.ipynb
```

**Role:** Primary final computational research artefact.

Notebook 6 implements the final consolidated research methodology, including:

- five-dimensional AI4I deployment evidence;
- criterion-specific rankings;
- LORO empirical reliability estimation;
- empirical reliability-weight construction;
- Equal Borda;
- Existing Composite comparison;
- primary RDRA;
- dominance-consistency checks;
- VQ1 criterion-subset validation;
- VQ1 paired statistical validation;
- reliability-weight sensitivity;
- VQ2 data-quality responsiveness;
- direct matched-bootstrap comparison;
- VQ3 stakeholder-preference uncertainty;
- Pareto and regret analysis;
- Top-3 shortlist analysis;
- exploratory reliability-influence refinement; and
- complementary C-MAPSS responsiveness analysis.

The final output directory is:

```text
outputs/novelty_rank_regret_outputs_revised/
```

---

## 10. Computational Dependency Structure

The computational dependency structure is:

```text
AI4I 2020
    │
    ▼
Notebook 1
    │
    ▼
ai4i_ipmef_final_outputs/
    │
    ├──────────────► Notebook 3
    │
    └──────────────► Notebook 6
                         │
                         ▼
             novelty_rank_regret_outputs_revised/


NASA C-MAPSS FD001
    │
    ▼
Notebook 4
    │
    ▼
cmapss_rul_ipmef_outputs/
    │
    ├──────────────► Notebook 5
    │
    └──────────────► Notebook 6
```

Notebook 2 provides supporting statistical-validation evidence.

Notebook 5 provides supplementary anomaly-triggered prognostics evidence.

The principal final RDRA pathway is therefore:

```text
Notebook 1
     +
Notebook 4
     ↓
Notebook 6
```

---

## 11. Notebook 6 Required Inputs

Notebook 6 reads the following AI4I files:

```text
ai4i_ipmef_final_outputs/
├── 01_clean_candidate_results.csv
├── 02_robustness_raw_results.csv
├── 05_composite_robustness_by_seed.csv
└── 07_candidate_summary.csv
```

It reads the following C-MAPSS files:

```text
cmapss_rul_ipmef_outputs/
├── 02_robustness_raw.csv
└── 06_candidate_summary.csv
```

The final Notebook 6 output directory is:

```text
novelty_rank_regret_outputs_revised/
```

---

## 12. Rebuilding the Final Research Artefact

### Step 1 — Install the Required Python Packages

From the repository root:

```bash
pip install -r requirements.txt
```

The project was developed primarily using Python notebooks in Google Colab.

---

### Step 2 — Generate AI4I Experimental Evidence

Run:

```text
notebooks/01_AI4I_IPMEF_Final_Updated.ipynb
```

This generates:

```text
ai4i_ipmef_final_outputs/
```

The stored reference version is available under:

```text
outputs/ai4i_ipmef_final_outputs/
```

---

### Step 3 — Generate C-MAPSS Experimental Evidence

Run:

```text
notebooks/04_CMAPSS_RUL_IPMEF_Updated.ipynb
```

This generates:

```text
cmapss_rul_ipmef_outputs/
```

The stored reference version is available under:

```text
outputs/cmapss_rul_ipmef_outputs/
```

Dataset acquisition and preprocessing are documented in:

```text
README_DATA.md
```

---

### Step 4 — Run Final RDRA Analysis

Notebook 6 expects the two upstream directories to be accessible using the configured names:

```text
ai4i_ipmef_final_outputs/
cmapss_rul_ipmef_outputs/
```

Then run:

```text
notebooks/06_RDRA_Revised_Methodology_Aligned_Refined_FINAL.ipynb
```

The notebook generates:

```text
novelty_rank_regret_outputs_revised/
```

The stored reference output is available under:

```text
outputs/novelty_rank_regret_outputs_revised/
```

---

## 13. Dataset Roles

### AI4I 2020

AI4I provides the **primary complete RDRA development and validation environment**.

The experiment contains:

- 18 model/configuration candidates;
- 20 experimental repetitions;
- five deployment dimensions;
- repeated ranking evidence;
- controlled corruption evidence; and
- complete VQ1–VQ3 analysis.

### NASA C-MAPSS FD001

C-MAPSS provides **complementary cross-task evidence** in a sequential Remaining Useful Life setting.

The current C-MAPSS branch supports:

- Predictive Quality;
- Robustness; and
- Computational Efficiency.

It does not contain the same repeated five-dimensional structure required to estimate an independent complete RDRA reliability-weight vector.

Therefore, C-MAPSS is interpreted as:

> **Complementary cross-task deployment-rank responsiveness evidence**

rather than as a second complete RDRA validation.

Complete data provenance is documented in:

```text
README_DATA.md
```

---

## 14. Final AI4I Reliability Estimates

The final empirical criterion-reliability estimates are:

| Deployment Dimension | Positive Stability | Reliability Weight |
|---|---:|---:|
| Predictive Quality | 0.651369 | 0.222560 |
| Robustness | 0.380964 | 0.130168 |
| Probability Reliability | 0.788889 | 0.269549 |
| Computational Efficiency | 0.818301 | 0.279598 |
| Operational Impact | 0.287183 | 0.098125 |

These values quantify ranking reproducibility under the implemented experimental protocol.

They do not represent universal importance values or stakeholder preferences.

---

## 15. VQ1 — Criterion-Uncertainty Results

VQ1 evaluates all criterion subsets of sizes 2, 3 and 4.

With five deployment dimensions:

\[
{5\choose2}+{5\choose3}+{5\choose4}
=
10+10+5
=
25.
\]

The full five-dimension ranking is retained as the reference and is not counted as an additional perturbation.

The principal descriptive results are:

| Method | Kendall | Spearman | Top-3 Overlap | Rank Displacement |
|---|---:|---:|---:|---:|
| Existing Composite | 0.624052 | 0.741754 | 0.746667 | 2.448889 |
| Equal Borda | 0.677306 | 0.799260 | 0.720000 | 2.257778 |
| Primary RDRA | **0.746405** | **0.862456** | **0.813333** | **1.773333** |

The paired primary-RDRA versus Equal-Borda results are:

| Measure | Improvement | 95% CI | Holm-adjusted p |
|---|---:|---:|---:|
| Kendall | +0.069 | [0.012, 0.126] | 0.039 |
| Spearman | +0.063 | [-0.007, 0.134] | 0.077 |
| Top-3 overlap | +0.093 | [0.030, 0.156] | 0.022 |
| Rank displacement | 0.484 ranks better | [0.146, 0.822] | 0.022 |

Kendall agreement, Top-3 overlap and rank displacement remain statistically supported after Holm correction.

The Spearman result is directionally positive but is not statistically significant after correction.

---

## 16. Reliability-Weight Sensitivity

A 1,000-perturbation sensitivity experiment produced:

```text
Mean Kendall agreement:           0.964201
Median Kendall agreement:         0.973856
Mean Top-3 overlap:               1.000000
Mean absolute rank displacement:  0.296444
Tie-aware Top-1 survival:         0.699
Mean Top-1 set overlap:           0.699
```

The result indicates high stability of the broader RDRA consensus under the tested moderate weight perturbations.

The Top-3 shortlist is more stable than the exact winner.

---

## 17. VQ2 — Data-Quality Responsiveness

VQ2 evaluates 16 controlled AI4I corruption scenarios across:

- Gaussian noise;
- Missing Completely At Random;
- outliers; and
- static sensor offset.

The analysis compares:

\[
\Delta E =
\text{deployment-evidence change magnitude}
\]

with:

\[
\Delta R =
\text{ranking-change magnitude}.
\]

The locked exploratory refined RDRA produced:

\[
\rho_{\text{RDRA}}=0.678041
\]

with:

\[
p=0.003892.
\]

Equal Borda produced:

\[
\rho_{\text{Borda}}=0.347570.
\]

The direct difference is:

\[
\Delta\rho=0.330471.
\]

The matched-bootstrap 95% confidence interval is:

\[
[0.046026,\;0.702235].
\]

The tested evidence therefore supports statistically stronger responsiveness for the locked exploratory refined RDRA than Equal Borda under the evaluated AI4I data-quality perturbations.

---

## 18. VQ3 — Preference Uncertainty

VQ3 evaluates:

```text
5,000 matched stakeholder-preference scenarios
```

using the same preference vectors for RDRA and Equal Borda.

The analysis includes:

- Top-3 candidate shortlists;
- Pareto analysis;
- regret analysis; and
- joint preference-plus-corruption analysis.

The current evidence shows that RDRA maintained **broadly comparable Top-3 downstream decision usefulness** to Equal Borda under the tested preference scenarios.

No additional RDRA advantage was demonstrated.

This result establishes an important research boundary:

\[
\text{Improved Ranking Consistency}
\not\Rightarrow
\text{Improved Downstream Decision Usefulness in Every Setting}.
\]

The VQ3 result is descriptive and should not be interpreted as formal statistical equivalence or non-inferiority.

---

## 19. Research Development History

### Phase 1 — Early AI4I Deployment-Oriented Evaluation

Research Progress Check Point 1 documents the initial extension of conventional predictive-maintenance evaluation through:

- repeated experimentation;
- class-imbalance handling;
- cost-sensitive thresholding;
- probability calibration;
- robustness evaluation; and
- statistical validation.

This phase primarily corresponds to the foundations retained in Notebooks 1 and 2.

### Phase 2 — Cross-Task Expansion and RDRA Emergence

Research Progress Check Point 2 documents:

- expansion from AI4I classification to C-MAPSS RUL prediction;
- conflicting deployment rankings;
- initial RDRA development;
- ranking-stability analysis;
- corruption analysis;
- criterion sensitivity; and
- downstream decision analysis.

This phase is represented primarily by Notebooks 1–5.

### Phase 3 — Final RDRA Consolidation

The final research direction was consolidated around:

- one Main Research Question;
- primary RDRA;
- Equal Borda as the principal controlled baseline;
- VQ1 consistency;
- VQ2 responsiveness; and
- VQ3 downstream decision usefulness.

Notebook 6 represents the final consolidated computational methodology.

---

## 20. Research Documentation

The `docs/` directory contains the principal written research artefacts.

| Document | Role |
|---|---|
| `Research_Progress_Check_Point_1.pdf` | Early AI4I experimental development |
| `Research_Progress_Check_Point_2.pdf` | Cross-task expansion and RDRA emergence |
| `Literature_Review.pdf` | Final literature synthesis, research gap and novelty positioning |
| `Research_Methodology.pdf` | Final Main RQ, VQ1–VQ3 and experimental methodology |
| `Research_Progress_Check_Point_3.pdf` | Methodological consolidation and final validation progress |
| `Research_Implementation.pdf` | Detailed implementation of the RDRA computational artefact |
| `Research_Analysis.pdf` | Integrated interpretation of final empirical results |

The complete handover description is provided in:

```text
SIT747_Research_Development_Artefacts_Handover_Report.pdf
```

---

## 21. Important Interpretation Boundaries

The following distinctions must be retained when interpreting or extending the research.

### Empirical reliability is not stakeholder preference

\[
\text{Empirical Reliability Weight}
\neq
\text{Stakeholder Preference Weight}
\]

### Primary RDRA is separate from the exploratory refinement

```text
Primary RDRA:              gamma = 1
Exploratory refinement:    gamma = 3
```

### Gamma = 3 is not globally optimal

The selected exploratory value occurs at the upper boundary of the tested development grid.

### C-MAPSS is complementary evidence

C-MAPSS is not a complete second independent RDRA validation.

### VQ3 does not demonstrate universal superiority

The tested VQ3 evidence does not show an additional Top-3 downstream usefulness advantage for RDRA.

### Not every VQ1 metric is statistically significant

The Spearman comparison does not remain statistically significant after Holm correction.

### Reliability weights are protocol-dependent

The reliability weights depend on:

- the dataset;
- candidate models;
- experimental repetitions;
- metric construction;
- corruption protocol; and
- deployment-dimension definitions.

They should not be interpreted as universal predictive-maintenance criterion weights.

---

## 22. Known Limitations

The principal limitations of the current study are:

- the complete RDRA validation is primarily based on AI4I;
- AI4I is a synthetic predictive-maintenance benchmark;
- the five deployment dimensions are not exhaustive;
- the current reliability weights are protocol-specific;
- C-MAPSS does not provide a complete five-dimensional repeated-evidence structure;
- VQ1 contains 25 genuine criterion-subset perturbations;
- VQ2 contains 16 controlled AI4I corruption scenarios;
- VQ3 does not demonstrate an additional Top-3 advantage;
- the exploratory \(\gamma=3\) value lies at the boundary of the tested grid; and
- the framework has not yet been prospectively evaluated in a live industrial maintenance environment.

---

## 23. Long-Term Research Vision

The long-term research direction is to extend RDRA from a benchmark-based computational research artefact into a more general evidence-confidence-aware framework for predictive-maintenance model selection.

A future deployment decision process could separate:

```text
Stage 1:
Empirical reliability-aware candidate shortlisting

Stage 2:
Stakeholder-specific final model selection
```

This preserves the distinction between:

```text
How reliably is the criterion ranking supported by evidence?
```

and:

```text
How important is the criterion in the deployment context?
```

Potential future directions include:

- complete RDRA replication on additional datasets;
- real industrial validation;
- broader investigation of the reliability-influence parameter;
- formal integration of empirical reliability and stakeholder preference;
- dynamic or online reliability estimation; and
- investigation of when ranking improvements translate into downstream decision benefits.

---

## 24. Current Research Conclusion

The current evidence supports the following bounded conclusion:

> **Reliability-aware rank aggregation can produce a more defensible deployment consensus by allowing criterion influence to depend partly on empirical ranking reproducibility rather than assuming identical evidence confidence across all deployment criteria.**

The strongest current evidence concerns:

- ranking consistency under criterion uncertainty;
- shortlist preservation;
- robustness to moderate reliability-weight perturbation; and
- stronger data-quality responsiveness for the locked exploratory refined RDRA.

The current results do not support a claim that RDRA is universally superior to Equal Borda in every decision setting.

---

## 25. Research Contribution Boundary

The project does not claim novelty in:

- Borda scoring;
- weighted rank aggregation;
- Kendall rank correlation;
- Spearman rank correlation;
- multi-criteria decision making;
- probability calibration;
- robustness testing;
- Pareto analysis; or
- regret analysis individually.

The proposed methodological contribution is:

> **The use of criterion-level empirical ranking reproducibility estimated from repeated predictive-maintenance experiments as reliability information within deployment-oriented rank aggregation.**

---

## 26. Reproducibility Files

Dataset provenance and data-reconstruction instructions are provided in:

```text
README_DATA.md
```

Python dependencies are provided in:

```text
requirements.txt
```

The original notebooks also preserve generated execution and experiment-configuration metadata inside their respective output folders where available.

---

## 27. Recommended Reading Order

A future researcher continuing the project should review the repository in the following order:

```text
1. README.md
2. README_DATA.md
3. SIT747_Research_Development_Artefacts_Handover_Report.pdf
4. docs/Literature_Review.pdf
5. docs/Research_Methodology.pdf
6. docs/Research_Implementation.pdf
7. docs/Research_Analysis.pdf
8. notebooks/06_RDRA_Revised_Methodology_Aligned_Refined_FINAL.ipynb
9. notebooks/01_AI4I_IPMEF_Final_Updated.ipynb
10. notebooks/04_CMAPSS_RUL_IPMEF_Updated.ipynb
11. Earlier notebooks and checkpoints for development provenance
```

---

## 28. Handover Status

At the time of this handover:

| Component | Status |
|---|---|
| Literature Review | Complete |
| Research Methodology | Complete |
| AI4I experimental framework | Complete |
| C-MAPSS experimental framework | Complete within defined scope |
| Primary RDRA implementation | Complete |
| Equal Borda baseline | Complete |
| LORO reliability estimation | Complete |
| VQ1 evaluation | Complete |
| VQ1 statistical validation | Complete |
| Reliability-weight sensitivity | Complete |
| VQ2 responsiveness analysis | Complete |
| VQ2 direct bootstrap comparison | Complete |
| VQ3 preference analysis | Complete |
| Complementary C-MAPSS analysis | Complete |
| Research Implementation | Complete |
| Research Analysis | Complete |
| Research handover package | Complete |
| Full independent second-dataset RDRA replication | Future work |
| Prospective industrial validation | Future work |

---

## 29. Handover Summary

This repository preserves the computational, analytical and documentary state of the RDRA research project.

The final computational pathway can be reconstructed from the provided notebooks and documented data sources, while the earlier notebooks and progress checkpoints preserve the evolution and provenance of the research.

The repository therefore provides a baseline from which the methodology can be independently reviewed, reproduced and extended.

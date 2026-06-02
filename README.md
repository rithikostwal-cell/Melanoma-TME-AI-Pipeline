# Deciphering the Tumor Microenvironment: An Integrated Single-Cell RNA-Seq and AI Framework for Novel Biomarker and Therapeutic Target Discovery in Melanoma

[![Python 3.9+](https://img.shields.io/badge/python-3.9+-blue.svg)](https://www.python.org/)
[![Scanpy](https://img.shields.io/badge/Scanpy-1.10+-orange.svg)](https://scanpy.readthedocs.io/)
[![XGBoost](https://img.shields.io/badge/XGBoost-Ensemble-green.svg)](https://xgboost.readthedocs.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 📌 Abstract
* **Background:** Melanoma is a highly immunogenic and therapeutically challenging malignancy. The complex cellular ecosystem of the tumor microenvironment (TME) acts as a critical driver of immune evasion, patient prognosis, and treatment resistance. This project implements an end-to-end explainable machine learning and network biology pipeline designed to deconstruct TME single-cell heterogeneity, discover generalizable candidate biomarkers, and map actionable cell-cell communication networks.
* **Methodology:** High-quality scRNA-seq expression data from melanoma lesions (GSE115978) encompassing 7,186 cells were processed. Following stringent quality control, library size normalization, and highly variable gene (HVG) selection, cells were partitioned via unsupervised Leiden clustering and annotated via signature gene scoring methods. An optimized XGBoost ensemble classifier was constructed for cross-compartment biomarker screening, interpreted via SHAP (SHapley Additive exPlanations) values, and augmented with a PyTorch autoencoder bottleneck network. Downstream analyses included pathway enrichment via `gseapy`, ligand-receptor communication mapping via `LIANA`, and Pearson co-expression profiling. Findings were validated using an independent cross-cohort single-cell matrix (GSE72056) and true empirical patient survival outcomes from The Cancer Genome Atlas Skin Cutaneous Melanoma (TCGA-SKCM) cohort (n=314).
* **Results:** Leiden clustering successfully resolved the cellular atlas into major compartments (Melanoma/Tumor, T-Cells, B-Cells, Macrophages, NK Cells, Endothelial cells, and CAFs). The trained XGBoost model prioritized *CD79A*, *MLANA*, *LYZ*, *MFAP4*, and *CDH5* as top candidate biomarkers. SHAP analysis highlighted a bidirectional pattern for *B2M* capturing major transcriptomic signs of immune evasion. Functional enrichment mapped Coagulation Activation and Epithelial-Mesenchymal Transition (EMT) as highly dysregulated pathways. Cell-cell communication profiling inferred highly significant *SERPINE1* signaling axes to *LRP1* and *PLAUR* receptors mapping from lymphocytes to stromal compartments. *SPARC* was identified as the central co-expression hub gene. Finally, empirical clinical survival validation within the TCGA-SKCM cohort proved that high expression of the target biomarker *CD79A* correlates with a statistically significant survival advantage (**Log-Rank p = 4.4202e-03**), extending median overall survival from 26.7 months to 44.8 months.
* **Conclusion:** This pipeline systematically resolves TME heterogeneity, revealing a robust biomarker signature centered on *CD79A* and *MLANA*. The discovery of the protective prognostic role of *CD79A* links single-cell immune networks directly to patient outcomes, providing a reproducible roadmap for immunotherapeutic stratification.

---

## 🗺️ Pipeline Architecture & Repository Structure
The pipeline follows a modular, reproducible data science structure:

```text
├── tme_analysis_pipeline.py      # Core end-to-end execution script
├── .gitignore                    # Prevents heavy data file tracking
├── README.md                     # Comprehensive project documentation
└── results/                      # Static output directories for figures
    ├── umap_leiden.png           # Leiden community clusters
    ├── umap_annotated.png         # Annotated TME atlas
    ├── dotplot_markers.png       # Canonical marker verification
    ├── xgboost_biomarkers.png    # Feature importance bars
    ├── shap_summary.png          # TreeExplainer beeswarm plots
    ├── pathway_enrichment.png    # GSEA dotplots
    ├── pseudobulk_heatmap.png    # Cross-compartment signature validation
    ├── hub_coexpression.png      # Pearson correlation matrix
    └── survival_kaplan_meier_empirical.png # True empirical clinical survival curves

🛠️ Installation & Dependency Setup
To replicate this analysis, clone the repository and install the necessary libraries.
# Clone the repository
git clone [https://github.com/rithikostwal-cell/Melanoma-TME-AI-Pipeline.git](https://github.com/rithikostwal-cell/Melanoma-TME-AI-Pipeline.git)
cd Melanoma-TME-AI-Pipeline

# Install required dependencies
pip install "scanpy>=1.10" anndata leidenalg python-igraph xgboost scikit-learn shap gseapy lifelines dash plotly liana seaborn networkx torch

## 📊 Key Results & Biological Findings

### 1. Cellular Atlas Construction
Unsupervised Leiden clustering at a 0.5 resolution resolved the low-dimensional single-cell transcriptomic space into distinct functional compartments. Automated scoring successfully assigned cell types using canonical markers, mapping parenchymal tumor profiles away from incoming immune infiltrates and stromal structures.

| Cell Type Compartment | Key Canonical Marker Genes Used for Identification |
| :--- | :--- |
| **Melanoma / Tumor** | *MITF*, *MLANA*, *PMEL*, *DCT*, *TYRP1* |
| **T-Cells** | *CD3D*, *CD3E*, *CD8A*, *CD4*, *TRAC* |
| **B-Cells** | *CD19*, *MS4A1*, *CD79A* |
| **Macrophages** | *CD14*, *FCGR3A*, *LYZ*, *CSF1R* |
| **Cancer-Associated Fibroblasts (CAFs)** | *COL1A1*, *FAP*, *ACTA2* |

### 2. AI-Driven Feature Selection & Model Interpretability
An optimized XGBoost classifier trained on the annotated coordinates isolated the highly variable genes with the highest global predictive weight. The B-cell lineage co-signaling factor CD79A transitioned to the top global biomarker candidate, followed closely by the melanocytic oncogene MLANA.

SHAP explainability mapping via TreeExplainer revealed that high expression of MLANA and S100B strongly drove positive tumor classification. Crucially, the antigen-presentation factor B2M demonstrated a clean bidirectional SHAP distribution, where its down-regulation strongly pushed model outcomes toward a malignant state, computationally capturing the classic footprint of MHC-I down-regulation used by melanoma cells to evade T-cell tracking.

### 3. Intercellular Communication & Network Biology
* **Pathway Enrichment:** GSEA via `gseapy` revealed notable transcriptomic dysregulation within the tumor parenting compartment, emphasizing Coagulation Cascade Activation (Adjusted p = 7.32e-12) and Epithelial-Mesenchymal Transition (EMT) (Adjusted p = 9.40e-08).
* **Ligand-Receptor Crosstalk:** LIANA cellular communication tracing mapped a dominant paracrine communication axis centered on the multi-subunit ligand SERPINE1 (PAI-1) mapping from lymphocytes to stromal subsets, binding directly to LRP1 on CAFs and PLAUR on macrophages.
* **Hub Gene Matrix:** Pearson co-expression network analysis of top features identified SPARC as the central regulatory hub node with the highest global mean connectivity score (r = 0.195).

### 4. Empirical Clinical Validation (TCGA-SKCM)
Cross-cohort screenings within the separate GSE72056 single-cell dataset successfully confirmed biomarker stability. To demonstrate clinical translation, patients from the TCGA-SKCM cohort (n=314) were stratified based on the true empirical median split of bulk CD79A expression values.

The computed Kaplan-Meier survival analysis revealed a highly significant survival advantage for patients with high CD79A transcript levels (Log-Rank p = 4.4202e-03). The high-expression cohort demonstrated a median overall survival of 44.8 months, extending survival by 18.1 months compared to the low-expression cohort (26.7 months). This directly underscores the protective role of tumor-infiltrating B-cells and tertiary lymphoid structures (TLS) in driving anti-tumor immunity.

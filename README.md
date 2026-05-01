<div align="center">

# Mclust-Based Gene Expression Clustering for Immune Checkpoint Analysis

**PCA + Gaussian mixture model clustering on 49,000+ genes to identify immune checkpoint regulators in T2D-associated atherosclerosis, validated with t-SNE, BIC/ICL selection, and STRING network hub gene ranking.**

[![R](https://img.shields.io/badge/R-4.x-276DC3?style=flat&logo=r&logoColor=white)](https://r-project.org)
[![Mclust](https://img.shields.io/badge/Mclust-Clustering-8B0000?style=flat)](https://mclust-org.github.io/mclust/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat)](LICENSE)
[![Domain](https://img.shields.io/badge/Domain-Computational%20Biology-6A5ACD?style=flat)](#)

</div>

---

## The Problem

Type 2 diabetes (T2D) dramatically accelerates atherosclerosis, yet the immune mechanisms driving this interaction remain poorly mapped. Bulk gene expression datasets contain tens of thousands of genes — identifying which ones regulate immune checkpoints in a T2D + high-fat diet context requires unsupervised clustering, statistical filtering, and network-level validation.

This project answers: **which gene clusters and hub genes govern immune checkpoint activity in T2D-associated atherosclerosis?**

---

## Approach

### Analysis Pipeline

| Stage | Method | Purpose |
|-------|--------|---------|
| 1. Differential Expression | limma (FDR < 0.05, \|logFC\| > 1) | Identify significantly changed genes between KO\_HFD and WT\_HFD groups |
| 2. Dimensionality Reduction | PCA (prcomp) | Compress 49,000+ gene expression profiles into principal components |
| 3. Clustering | Mclust — VVV covariance model, G = 1–9 | Fit Gaussian mixture models; BIC/ICL selects optimal cluster count |
| 4. Cluster Validation | t-SNE + BIC/ICL curves | Confirm cluster separation is real and not overfitted |
| 5. Statistical Filtering | Triple FDR-corrected filter (see below) | Retain only genes with robust, reproducible cluster signal |
| 6. Immune Gene Annotation | ImmPort / GO:0002376 | Map filtered genes to curated immune gene reference list |
| 7. Hub Gene Ranking | STRING protein interaction network | Rank immune genes by network connectivity to find top regulators |
| 8. Visualization | pheatmap · ggplot2 violin plots | Show cluster structure and expression distribution by function |

### Triple FDR-Corrected Statistical Filter

All three criteria must pass for a gene to be retained:

| Test | Threshold | What it checks |
|------|-----------|----------------|
| Kruskal-Wallis | BH-adjusted p < 0.05 | Expression differs significantly across all 9 clusters |
| Wilcoxon rank-sum | BH-adjusted p < 0.05 | Immune clusters (1, 2, 5) differ from non-immune clusters |
| Spearman correlation | \|r\| > 0.5 | Gene expression has moderate-to-strong correlation with cluster membership |

The r > 0.5 threshold (vs. the common r > 0.3) was chosen deliberately to reduce false positives.

### Functional Cluster Labels

| Cluster | Biological Function |
|---------|-------------------|
| 1 | Immune Checkpoints |
| 2 | Inflammatory Signals |
| 3 | Lipid Metabolism |
| 4 | Endothelial Activation |
| 5 | Chemokine Signaling |
| 6 | Cell Adhesion |
| 7 | Cytokine Regulation |
| 8 | Fibrosis Pathways |
| 9 | Hypoxia Response |

---

## Results

| Metric | Value |
|--------|-------|
| Genes processed | **49,000+** |
| Optimal cluster model | **9-cluster VVV (BIC/ICL validated)** |
| DE genes (FDR < 0.05, \|logFC\| > 1) | Significant genes identified between KO\_HFD vs WT\_HFD |
| Genes passing triple FDR filter | Kruskal-Wallis + Wilcoxon + Spearman combined |
| Hub genes identified | **3 — Cd4, Cxcl10, Fmo3** |
| Immune gene source | ImmPort / GO:0002376 (curated) |
| Validation methods | **t-SNE · BIC/ICL · STRING network** |

**Cd4** anchors T-cell-mediated immune checkpoint activity. **Cxcl10** connects chemokine signaling to immune recruitment. **Fmo3** links lipid metabolism to the immunoinflammatory axis — a novel bridge between T2D and plaque progression.

---

## Demo

### Video Walkthrough
> *2-minute walkthrough: differential expression → clustering → hub gene discovery.*

[![Watch the Demo](https://img.shields.io/badge/Watch%20Demo-Coming%20Soon-red?style=for-the-badge&logo=youtube)](#)

### Key Outputs

| Output | Description |
|--------|-------------|
| PCA Scree Plot | Variance explained per PC for high-variance gene subset |
| Heatmap (pheatmap) | PCA scores annotated by Mclust cluster assignment |
| Violin Plot | Log₂ expression distribution across all 9 functional clusters |
| DE Results CSV | Full limma output for KO\_HFD vs WT\_HFD |
| STRING Hub Ranking | Network centrality scores for immune-filtered genes |

*Figures will be added here.*

---

## Tech Stack

| Layer | Tool |
|-------|------|
| Language | R 4.x |
| Clustering | Mclust (Gaussian mixture models) |
| Dimensionality reduction | PCA (prcomp) · t-SNE |
| Differential expression | limma |
| Visualization | ggplot2 · pheatmap |
| Network analysis | STRING protein interaction database |
| Immune gene annotation | ImmPort · GO:0002376 (biomaRt) |
| Data wrangling | tidyverse (dplyr · tidyr · readr) |


---
## Setup & Run
**Prerequisites:** R 4.x with the following packages installed
```r
install.packages(c("mclust", "ggplot2", "pheatmap", "tidyverse", "limma"))
# For Bioconductor packages:
BiocManager::install(c("limma", "biomaRt"))
Run scripts in order from the analysis/ directory. Input data files go in data/ — see filenames in the structure above.

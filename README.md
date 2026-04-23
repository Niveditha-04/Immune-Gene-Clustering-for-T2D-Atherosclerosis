# PCA-Mclust Clustering for Immune Gene Identification in T2D-Associated Atherosclerosis

> **Copyright Notice:** This project is under copyright protection. The source code is not publicly available at this time

---

## Overview

This project applies a dual bioinformatics framework to identify key immune-regulatory genes involved in **Type 2 Diabetes (T2D)-associated atherosclerosis**. Atherosclerosis is a progressive vascular disease driven by chronic inflammation and lipid accumulation; when combined with T2D, it forms a high-risk comorbidity that is often resistant to conventional therapies.

The central hypothesis is that **immune checkpoint dysregulation** — involving molecules such as PD-1, CTLA4, and LAG3 — plays a critical but underexplored role in the vascular pathology of diabetic patients. This project uses transcriptomic data to identify the genes and pathways that govern these mechanisms.

---

## Dataset

| Property | Details |
|---|---|
| **Source** | Gene Expression Omnibus (GEO) |
| **Accession** | GSE57329 |
| **Organism** | *Mus musculus* |
| **Cell Type** | Aortic endothelial cells |
| **Conditions** | Wild-type and galectin-3 knockout mice × high-fat or standard chow diet |
| **Sample Size** | 24 samples (6 biological replicates per group; 3 male, 3 female) |
| **Platform** | Affymetrix Mouse Genome 430 2.0 Array (GPL1261) |
| **Transcripts** | ~45,000 transcripts; ~41,000 high-confidence genes retained after filtering |
| **Preprocessing** | Data was RMA-normalized and log₂-transformed at the platform level |

---

## Methodology

The analysis pipeline consists of six stages:

### 1. Data Preprocessing
- Low-expression and zero-variance genes were filtered out
- Genes were annotated using official *Mus musculus* gene symbols
- Duplicate and unannotated entries were removed

### 2. Principal Component Analysis (PCA)
- Dimensionality reduction was performed using PCA to capture major expression trends
- The top four principal components (PC1–PC4) were retained based on a scree plot and explained variance
- PC1 explained the most variance (eigenvalue = 13.81) and was the primary axis of cluster separation

### 3. Model-Based Clustering with Mclust
- PCA-reduced data were clustered using Gaussian Mixture Models (GMM) via the Mclust package
- Covariance structures and cluster numbers (G = 1–9) were evaluated using the **Bayesian Information Criterion (BIC)**
- The best-fitting model (VVV, 9 clusters) was selected; each gene was assigned to one of nine clusters

### 4. Cluster Validation
- **Silhouette scores** were calculated to assess intra-cluster consistency and inter-cluster separation (overall average = 0.81)
- **t-SNE** projections confirmed non-linear cluster separation
- **BIC and ICL plots** supported the 9-cluster VVV model as the optimal fit
- Pairwise PC scatterplots (PC1–PC4) validated cluster elliptical boundaries

### 5. Immune Gene Filtration
After clustering, immune-related genes were isolated using three statistical filters applied in combination:
- Kruskal–Wallis test (p < 0.05)
- Wilcoxon rank-sum test (p < 0.05)
- Spearman correlation coefficient (r > 0.3)

This yielded a refined list of approximately **1,000 immune-associated genes** linked to their respective clusters.

### 6. Functional Enrichment and Network Analysis
- **KEGG & Reactome** pathway enrichment via the Enrichr platform (FDR < 0.05)
- **Molecular function enrichment** via ShinyGO (v0.76), using the GO:MF ontology
- **Protein–protein interaction (PPI) networks** constructed using STRING (*Mus musculus* settings) and visualized in Cytoscape
- **Hub gene ranking** performed using the CytoHubba plugin (Maximal Clique Centrality / MCC scores)
- **Bubble plots** generated using SRplot to visualize pathway fold enrichment and significance

---

## Key Results

### Clustering Structure
Combining PCA with Mclust produced **9 well-separated gene clusters**, each corresponding to a distinct biological functional group:

| Cluster | Functional Label |
|---|---|
| 1 | Immune Checkpoints |
| 2 | Inflammatory Signals |
| 3 | Lipid Metabolism |
| 4 | Endothelial Activation |
| 5 | Chemokine Signaling |
| 6 | Cell Adhesion |
| 7 | Cytokine Regulation |
| 8 | Fibrosis Pathways |
| 9 | Hypoxia Response |

**Cluster 1 (Immune Checkpoints)** showed the broadest and highest gene expression distribution, making it the most biologically active group.

### Top Hub Genes Identified

| Gene | Full Name | Cluster | Biological Role | Validation Basis |
|---|---|---|---|---|
| **FMO3** | Flavin-containing Monooxygenase 3 | Cluster 7 | Detoxification, taurine metabolism | Top KEGG-enriched gene (bubble plot; fold enrichment ~300) |
| **CD4** | T Helper Cell Marker | Cluster 1 | T-helper cell activation, immune signaling | MCC Rank 1 in Cluster 1 (Cytoscape/STRING) |
| **CXCL10** | Interferon gamma-induced protein 10 (IP-10) | Immune Gene Group | Chemokine signaling, leukocyte recruitment | High pathway overlap + MCC Rank 1 across immune gene network |

### Cluster 1 Deep Dive
Cluster 1 contained key immune genes including **Cd4**, **Ncr1**, and **Il17a** as central network hubs. Pathway enrichment pointed to:
- Mucin O-glycan biosynthesis
- Bile acid synthesis
- Calcium signaling
- Ion channel and transporter activity (especially ligand-gated signaling)

**Cd4** ranked highest by MCC score, confirming its central role in T-cell activation and immune inflammatory relevance in the T2D-atherosclerosis context.

### Immune Gene Network (Chemokine Analysis)
Across the full ~1,000 immune gene network, **CXCL10** emerged as the top-ranked hub gene. Enriched pathways included:
- Cytokine–cytokine receptor interaction
- JAK-STAT signaling
- Th17 cell differentiation
- Chemokine signaling
- IL-17 signaling

---

## Visualization Interpretations

These notes summarize what each key plot reveals about the data structure and biological signal.

### PCA Scatterplots (2D & 3D)
- Data distributes most strongly along **PC1** — the biggest gene expression differences between samples lie on this axis
- In PC2, PC3, and PC4 plots, clusters overlap heavily in the center, reflecting that many genes behave similarly across samples
- Mclust divided data into **9 color-coded groups**: compact clusters (pink, red, blue) indicate tight co-expression; stretched clusters (green, orange) suggest gradual transcriptional transitions
- The **3D PCA (PC1–PC2–PC3)** reveals variation invisible in 2D; some clusters group around a core while others spread outward
- When **PC4** appears on an axis, the data is very tightly packed and mostly captures noise or minor variation
- **Final takeaway:** PC1 vs PC2 gives the clearest overall structure due to elongated spread, making it the primary plane for interpretation

### t-SNE Projection
- Reduces the high-dimensional expression matrix into 2D using a non-linear, curved space to reveal cluster topology
- Produces a **spiral-like layout** that clearly separates several clusters — particularly red (Cluster 1) and pink (Cluster 8), which appear as distinct isolated regions
- Central clusters show more mixing and required additional pathway analysis to interpret biologically
- **Final takeaway:** Clusters 1 and 8 show the clearest separation with minimal overlap, confirming their biological distinctiveness

### BIC and ICL Model Selection Plots
- **BIC (Bayesian Information Criterion):** Higher (less negative) = better model fit with less overfitting. The VVV model with 9 clusters achieved the highest BIC, confirming it as the best fit. BIC improves steadily up to 6 clusters, then levels off — beyond 6–7 clusters offers only minor gain. EII and VII models performed poorly, indicating they are too simple for this data.
- **ICL (Integrated Completed Likelihood):** Similar to BIC but additionally penalizes uncertain or overlapping cluster assignments. VVV still performs best, peaking around 8–9 clusters. More fluctuation was observed between models at 4–6 clusters, highlighting areas of weaker group confidence.
- **Final takeaway:** Both BIC and ICL converge on the **9-cluster VVV model** as optimal — BIC measures fit quality, ICL measures grouping confidence

### Pairwise PC Scatterplot (PC1–PC4 Grid)
- Shows all pairwise combinations of the top four principal components, with each color representing a Mclust cluster and ellipses showing multivariate normal contours
- **PC1–PC2** and **PC1–PC3** show the most spread and clearest directional variation with tight ellipses
- **PC3 and PC4** combinations are densely packed in the center — less variance captured, more overlap between clusters
- **Final takeaway:** PC1 vs PC2 quadrant shows tight ellipses and clear cluster separation; PC3–PC4 shows heavy overlap, consistent with PC4 capturing minimal biologically meaningful signal

---

## Tools and Software

| Tool | Purpose |
|---|---|
| R (v4.5.0) | Core analysis environment |
| `prcomp()` | Principal Component Analysis |
| Mclust (R package) | Gaussian Mixture Model clustering |
| `ggplot2` | Visualization (PCA plots, violin plots, heatmaps) |
| `Rtsne` | t-SNE dimensionality reduction |
| `plotly` | Interactive 3D PCA visualization |
| `clPairs()` | Pairwise PC classification plots |
| Enrichr | KEGG and Reactome pathway enrichment |
| ShinyGO (v0.76) | Gene Ontology molecular function enrichment |
| STRING | Protein–protein interaction network construction |
| Cytoscape + CytoHubba | Network visualization and hub gene ranking (MCC) |
| SRplot | Bubble plot visualization of enrichment results |

---

## Visualizations Produced

- 2D and 3D PCA scatterplots colored by Mclust-assigned cluster
- t-SNE projection showing cluster separation
- BIC and ICL model selection plots
- Pairwise PC classification grid (PC1–PC4)
- Silhouette plot (cluster quality assessment)
- PCA heatmap of high-variance genes
- Violin plot of log₂ expression across nine immune functional clusters
- PCA scree plot
- KEGG bubble plot (via SRplot)
- STRING network graphs (Cluster 1 and immune gene group)
- CytoHubba MCC ranking charts

---

## Biological Significance

This framework demonstrates that immune checkpoint genes — particularly those governing T-cell activation and chemokine-mediated leukocyte recruitment — are transcriptionally active in endothelial cells under metabolic and inflammatory stress conditions that mirror T2D-associated atherosclerosis. The identification of **FMO3**, **CD4**, and **CXCL10** as functional hubs provides potential targets for therapeutic intervention in this high-risk comorbid condition.

---

## Code Access Policy

> The source code for this project is **not publicly available** as it is currently under copyright review.
>
> If you would like to access to the analysis scripts, please reach out by contacting me directly or open a GitHub Issue. Code will be shared on a case-by-case basis upon request


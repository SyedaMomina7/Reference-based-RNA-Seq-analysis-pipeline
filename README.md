#  Reference-Based RNA-Seq Data Analysis Pipeline (Galaxy)

> **Transcriptomics | Differential Expression | Functional Genomics**

---

##  Project Overview

This repository presents a **complete, reproducible RNA-Seq analysis pipeline** implemented on the Galaxy platform, following the official Galaxy Training Network tutorial:

🔗 https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html

The workflow performs **reference-based transcriptomic analysis** to identify **differentially expressed genes (DEGs)** and interpret their biological significance through functional enrichment.

---

##  Objectives

- Perform quality control of raw RNA-Seq reads  
- Align reads to a reference genome  
- Quantify gene-level expression  
- Identify differentially expressed genes using DESeq2  
- Visualize expression patterns across samples  
- Perform Gene Ontology (GO) and KEGG pathway enrichment  

---

##  Dataset Description

This analysis uses the **Pasilla dataset**:

- **Organism:** *Drosophila melanogaster*  
- **Conditions:**
  - Untreated (control)
  - Treated (Pasilla knockdown)  
- **Replicates:**  
  - 4 untreated samples  
  - 3 treated samples  

This dataset is widely used for benchmarking RNA-Seq workflows.

---

## Pipeline Architecture

```
Raw FASTQ Reads
      │
      ▼
Quality Control (FastQC + MultiQC)
      │
      ▼
Read Alignment (HISAT2)
      │
      ▼
Read Counting (featureCounts)
      │
      ▼
Differential Expression (DESeq2)
      │
      ├──────────────► Visualization (PCA, Volcano, Heatmap)
      │
      ▼
Functional Enrichment (GO / KEGG)
```

---

##  Tools & Software

| Step | Tool |
|------|------|
| Quality Control | FastQC, MultiQC |
| Alignment | HISAT2 |
| Counting | featureCounts |
| Differential Expression | DESeq2 |
| Visualization | ggplot2, pheatmap |
| Enrichment Analysis | GOSeq / clusterProfiler |
| Platform | Galaxy |

---
##  Repository Structure

```
rnaseq-galaxy-analysis/
├── README.md
├── workflow/
│   └── galaxy_workflow.ga
├── data/
│   ├── raw/
│   └── metadata/
├── results/
│   ├── qc/
│   ├── mapping/
│   ├── counts/
│   ├── deseq2/
│   ├── plots/
│   └── enrichment/
├── docs/
│   ├── methodology.md
│   ├── results.md
│   └── discussion.md
└── figures/
```
---

## Methodology

### 1. Quality Control
Raw FASTQ files were assessed using **FastQC**, and results were aggregated using **MultiQC**.

### 2. Read Alignment
Reads were aligned to the reference genome (*Drosophila melanogaster*, dm6) using **HISAT2**, a splice-aware aligner.

### 3. Gene Quantification
Aligned reads were assigned to genomic features using **featureCounts** to generate a gene count matrix.

### 4. Differential Expression Analysis
Differential gene expression was analyzed using **DESeq2**, which:
- Normalizes count data  
- Estimates dispersion  
- Applies statistical testing  

### 5. Filtering
Genes were considered significant if:
- Adjusted p-value (FDR) < 0.05  

### 6. Functional Enrichment
Significant DEGs were analyzed for:
- Gene Ontology (GO) terms  
- KEGG pathways  

---

## Results Summary

- Clear separation between treated and untreated samples observed in PCA  
- Multiple genes significantly upregulated and downregulated  
- Volcano plot highlights statistically significant DEGs  
- Enrichment analysis identified biologically relevant pathways affected by treatment  

📁 See `results/` and `figures/` for full outputs.

---

## Visualizations

The following plots were generated:

- PCA plot (sample clustering)  
- Volcano plot (significance vs fold change)  
- Heatmap of top DEGs  
- MA plot  

---

# Reference-Based RNA-Seq Data Analysis Pipeline (Galaxy)

> **Domain:** Transcriptomics | Differential Expression | Functional Genomics
> **Platform:** Galaxy Training Network
> **Organism:** *Drosophila melanogaster*
> **Status:** Reproducible · Tutorial-Based · Open Source

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Scientific Background](#scientific-background)
3. [Dataset Description](#dataset-description)
4. [Pipeline Architecture](#pipeline-architecture)
5. [Tools & Software](#tools--software)
6. [Repository Structure](#repository-structure)
7. [Methodology](#methodology)
8. [Results Summary](#results-summary)
9. [Visualizations](#visualizations)
10. [How to Reproduce](#how-to-reproduce)
11. [References](#references)

---

## Project Overview

This repository presents a **complete, reproducible RNA-Seq analysis pipeline** implemented on the Galaxy platform, following the official Galaxy Training Network (GTN) tutorial:

🔗 [Reference-based RNA-Seq data analysis](https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html)

RNA sequencing (RNA-Seq) is a powerful next-generation sequencing technique used to quantify gene expression across the entire transcriptome. This pipeline implements a **reference-based** approach, where sequenced reads are mapped back to a known reference genome rather than assembled de novo. The workflow covers every stage of a standard RNA-Seq experiment — from raw read quality assessment through to biological interpretation via functional enrichment analysis.

The primary goal of this analysis is to identify **differentially expressed genes (DEGs)** between two biological conditions (treated vs. untreated) and to understand the downstream biological processes and pathways that are affected.

---

## Scientific Background

The **Pasilla gene** (*ps*) in *Drosophila melanogaster* encodes an RNA-binding protein that is the ortholog of human **Nova1** and **Nova2** — splicing regulators implicated in neurological diseases. Understanding how knockdown of Pasilla affects genome-wide gene expression provides insight into the regulatory networks governed by this class of RNA-binding proteins.

This dataset was originally published by **Brooks et al. (2011)** and has since become a standard benchmark dataset for RNA-Seq differential expression workflows. It provides a well-characterized biological system with known outcomes, making it ideal for validating analysis pipelines.

---

## Dataset Description

This analysis uses the **Pasilla dataset** derived from *Drosophila melanogaster* RNA-Seq experiments:

| Property | Details |
|----------|---------|
| Organism | *Drosophila melanogaster* |
| Reference Genome | dm6 |
| Conditions | Untreated (control) vs. Treated (Pasilla knockdown) |
| Untreated Replicates | 4 samples |
| Treated Replicates | 3 samples |
| Sequencing Type | Single-end and paired-end reads |
| Data Format | FASTQ |
| Source | NCBI GEO: GSE18508 |

All samples were processed under identical pipeline conditions to ensure comparability. Biological replicates are essential for robust statistical modeling in DESeq2.

---

## Pipeline Architecture

```
Raw FASTQ Reads
        │
        ▼
┌───────────────────────────────┐
│       Quality Control         │
│     FastQC → MultiQC          │
│   (Per-sample + Summary)      │
└──────────────┬────────────────┘
               │
               ▼
┌───────────────────────────────┐
│        Read Alignment         │
│     HISAT2 (splice-aware)     │
│   Reference Genome: dm6       │
└──────────────┬────────────────┘
               │
               ▼
┌───────────────────────────────┐
│        Read Counting          │
│        featureCounts          │
│     → Gene Count Matrix       │
└──────────────┬────────────────┘
               │
               ▼
┌───────────────────────────────┐
│   Differential Expression     │
│          DESeq2               │
└────────┬──────────────┬───────┘
         │              │
         ▼              ▼
┌──────────────┐  ┌─────────────────────┐
│Visualization │  │ Functional          │
│  PCA Plot    │  │ Enrichment          │
│  Volcano     │  │ GO Terms            │
│  Heatmap     │  │ KEGG Pathways       │
│  MA Plot     │  │                     │
└──────────────┘  └─────────────────────┘
```
---

## Tools & Software

| Step | Tool | Version | Purpose |
|------|------|---------|---------|
| Quality Control | FastQC | v0.11+ | Per-read quality metrics |
| QC Aggregation | MultiQC | v1.x | Combined QC report |
| Read Alignment | HISAT2 | v2.x | Splice-aware genome alignment |
| Format Conversion | SAMtools | v1.x | SAM/BAM processing |
| Read Counting | featureCounts | v2.x | Gene-level count matrix |
| Differential Expression | DESeq2 | v1.x | Statistical DE testing |
| Visualization | ggplot2, pheatmap | - | Publication-quality plots |
| Enrichment Analysis | goseq / clusterProfiler | - | GO and KEGG enrichment |
| Workflow Platform | Galaxy | Latest | Reproducible cloud analysis |

---

## Repository Structure

```
rnaseq-galaxy-analysis/
│
├── README.md                          # This file
│
├── docs/
│   ├── methodology.md                 # Detailed methods documentation
│   ├── results.md                     # Full results narrative
│   └── discussion.md                  # Biological interpretation
│
└── result_files/
    ├── Coverage/                      # Genome coverage tracks and outputs
    │
    ├── Functional Enrichment (GO & K..)/
    │   ├── GO enrichment results      # goseq GO term outputs
    │   └── KEGG pathway outputs       # Pathview KEGG visualizations
    │
    ├── Heatmaps & Top DE Genes/       # Heatmap figures and top DEG lists
    │
    ├── Quality Control/               # FastQC and MultiQC reports
    │
    ├── Differential Expression (DESeq2)/
    │   ├── DESeq2 results tables      # Full DE results with p-values
    │   └── DESeq2 plots               # MA plot, dispersion, PCA
    │
    ├── RNA STAR/                      # STAR alignment outputs (BAM files)
    │
    └── featureCounts/                 # Gene count matrix outputs
```

---

## Methodology

### 1. Quality Control

Raw FASTQ files were assessed individually using **FastQC** to evaluate:
- Per-base sequence quality scores (Phred)
- Per-sequence GC content distribution
- Adapter contamination levels
- Sequence duplication rates
- Overrepresented sequences

Results from all samples were aggregated into a single interactive report using **MultiQC**, enabling rapid cross-sample comparison. Samples with mean Phred scores below Q20 or significant adapter contamination were flagged for trimming prior to alignment.

### 2. Read Alignment

Reads were aligned to the *Drosophila melanogaster* reference genome (**dm6**) using **HISAT2**, a fast and splice-aware short-read aligner. HISAT2 is specifically designed for RNA-Seq data and can accurately align reads that span exon-exon junctions — a critical requirement for eukaryotic transcriptome analysis.

Key alignment parameters:
- Strandedness set according to library preparation protocol
- Splicing enabled using known splice sites from dm6 annotation
- Output: sorted BAM files per sample

Alignment rates were recorded and inspected via MultiQC to identify any samples with unexpectedly low mapping efficiency.

### 3. Gene Quantification

Aligned reads in BAM format were assigned to annotated genomic features using **featureCounts** from the Subread package. Reads overlapping annotated exons in the dm6 GTF annotation file were counted per gene to generate a **raw gene count matrix** (genes × samples).

Key considerations:
- Only uniquely mapping reads were counted
- Multi-mapping and ambiguous reads were excluded
- Strandedness matched library preparation protocol

### 4. Differential Expression Analysis

The raw count matrix was analyzed using **DESeq2**, which applies a negative binomial model to account for overdispersion commonly observed in RNA-Seq count data. DESeq2 performs the following steps internally:

1. **Size factor estimation** — normalizes for differences in library depth
2. **Dispersion estimation** — models per-gene biological variability
3. **Model fitting** — fits a generalized linear model per gene
4. **Hypothesis testing** — applies a Wald test to assess differential expression

The design formula used was:
~ condition

Where `condition` encodes treated vs. untreated status.

### 5. Significance Filtering

Genes were considered statistically significant DEGs if they met the following threshold:

| Criterion | Threshold |
|-----------|-----------|
| Adjusted p-value (Benjamini-Hochberg FDR) | < 0.05 |
| Absolute log₂ fold change | > 1.0 (optional) |

Independent filtering was applied by DESeq2 to remove low-count genes before multiple testing correction, improving statistical power.

### 6. Functional Enrichment Analysis

Statistically significant DEGs were subjected to functional enrichment analysis to identify overrepresented:

- **Gene Ontology (GO) terms** — covering Biological Process (BP), Molecular Function (MF), and Cellular Component (CC)
- **KEGG pathways** — to map DEGs onto known biological pathways

Enrichment was tested using a hypergeometric test with FDR correction. The genomic background was set to all expressed genes detected in the experiment.

---

## Results Summary

| Metric | Value |
|--------|-------|
| Total genes tested | ~14,000 |
| Significant DEGs (FDR < 0.05) | 191 |
| Enriched KEGG pathways identified | 127 |
| Top enriched GO term (BP) | Glycogen biosynthetic process |
| Top enriched GO term (MF) | Glutathione transferase activity |
| Top enriched GO term (CC) | Extracellular region |
| Top enriched KEGG pathway | Glycolysis / Gluconeogenesis (dme00010) — 14 DEGs |

Key observations:
- PCA plot revealed clear separation between treated and untreated sample clusters,
  confirming strong transcriptional response to Pasilla knockdown
- Volcano plot highlighted a subset of genes with both high fold change and high
  statistical significance
- Heatmap of top 50 DEGs showed consistent expression patterns within replicates,
  validating biological reproducibility
- GO enrichment (Wallenius method) identified **glycogen biosynthetic process** and
  **glucan biosynthetic process** as the most highly enriched biological processes
  (~80% DE in category)
- KEGG pathway analysis mapped 14 DEGs to the **Glycolysis/Gluconeogenesis** pathway
  (dme00010), with multiple enzymes showing significant upregulation (red) and
  downregulation (green) as visualized via Pathview

---

## Visualizations

| Figure | Description | File |
|--------|-------------|------|
| PCA Plot | Sample-level clustering by principal components | `result_files/ |
| Volcano Plot | Fold change vs. statistical significance | result_files/ |
| Heatmap | Top DEGs expression across all samples | `result_files` |
| MA Plot | Mean expression vs. log fold change | `result_files |

---
## Author
Syeda Momina Assad

## References

 Galaxy Training Network. (2024). Hands-on: Reference-based RNA-Seq data analysis.
   In *Galaxy Training Network — Transcriptomics*.
   https://training.galaxyproject.org/training-material/topics/transcriptomics/tutorials/ref-based/tutorial.html


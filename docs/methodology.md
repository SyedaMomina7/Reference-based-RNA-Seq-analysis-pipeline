#  Methodology

## Overview
This RNA-Seq analysis was performed using the Galaxy platform following a reference-based workflow to identify differentially expressed genes.

---

## 1. Quality Control

Raw FASTQ files were assessed using FastQC, and reports were aggregated using MultiQC.

**Goal:**
- Identify low-quality reads
- Detect adapter contamination
- Assess GC content and duplication levels

---

## 2. Read Alignment

Reads were aligned to the reference genome (*Drosophila melanogaster*, dm6) using HISAT2.

**Key Features:**
- Splice-aware alignment
- Suitable for eukaryotic transcriptomes

---

## 3. Gene Quantification

featureCounts was used to assign aligned reads to genomic features (genes).

**Output:**
- Gene count matrix for all samples

---

## 4. Differential Expression Analysis

DESeq2 was used to:
- Normalize count data
- Estimate dispersion
- Perform statistical testing

**Design formula:**
~ condition

---

## 5. Filtering Criteria

Genes were considered significantly differentially expressed if:

- Adjusted p-value (FDR) < 0.05

---

## 6. Functional Enrichment

Significant genes were analyzed for:

- Gene Ontology (GO)
- KEGG pathways

---

## Summary

This workflow enables robust identification and biological interpretation of transcriptional changes between conditions.

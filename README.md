# edgeR User Guide Practice

A hands-on walkthrough of differential gene expression analysis using the [edgeR](https://bioconductor.org/packages/release/bioc/html/edgeR.html) Bioconductor package, following the official edgeR User Guide.

---

## Project Description

This repository contains R code and count data for practicing RNA-seq differential expression analysis with edgeR. The workflow covers the full pipeline: loading count data, filtering low-count genes, normalizing libraries, estimating dispersion, fitting models, and testing for differential expression.

The analysis uses two datasets:
- **GSE60450** — a mouse mammary gland RNA-seq dataset (lactation study) used in the official edgeR vignette
- **TableS1** — a supplementary count matrix used for additional practice

---

## Repository Contents

| File | Description |
|------|-------------|
| `edgeR_UserGuide.Rmd` | Main R Markdown notebook following the edgeR User Guide |
| `targets.txt` | Sample metadata / experimental design file |
| `srr_ids.txt` | SRA run accession IDs for raw FASTQ files |
| `GSE60450_Lactation-GenewiseCounts.txt` | Gene-level count matrix from the GSE60450 dataset |
| `TableS1_counts_only_matrix.txt` | Count-only matrix extracted from TableS1 |
| `TableS1_edgeR_count_table.txt` | Full edgeR-formatted count table from TableS1 |
| `.gitignore` | Excludes large files (BAM, FASTQ, XLS, PDF, reference genome) |

> **Note:** Raw data files (BAM, FASTQ, `.xls/.xlsx`, reference genome, PDFs) are excluded from this repository due to file size. See Data Sources below for how to obtain them.

---

## Software Requirements

- **R** ≥ 4.5
- **Bioconductor** packages:

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install(c(
    "edgeR",
    "limma",
    "Rsubread",
    "GO.db",
    "org.Mm.eg.db",
    "tweeDEseqCountData"
))
```

- **CRAN** packages:

```r
install.packages("readxl")
```

---

## How to Run the Analysis

### 1. Clone the repository

```bash
git clone git@github.com:Kassaye52/edgeRUserguide.git
cd edgeRUserguide
```

### 2. Download the data

Download `GSE60450_Lactation-GenewiseCounts.txt` from [GEO accession GSE60450](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60450) and place it in the project directory, or use the copy already included in the repo.

### 3. Open and run the notebook

Open `edgeR_UserGuide.Rmd` in RStudio and knit, or run chunk by chunk:

```r
# Key steps in the pipeline
library(edgeR)

# Load counts
rawdata <- read.delim("GSE60450_Lactation-GenewiseCounts.txt", check.names = FALSE)

# Create DGEList object
group <- factor(c("L","L","L","M","M","M"))
y <- DGEList(counts = rawdata[, -1], genes = rawdata[, 1], group = group)

# Filter, normalize, estimate dispersion
keep <- filterByExpr(y)
y <- y[keep, , keep.lib.sizes = FALSE]
y <- normLibSizes(y)
y <- estimateDisp(y)

# Test for differential expression
et <- exactTest(y)
topTags(et)
```

---

## Data Sources

| Dataset | Source |
|---------|--------|
| GSE60450 (mouse mammary gland) | [NCBI GEO GSE60450](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE60450) |
| TableS1 count matrix | Supplementary data from associated publication |
| Raw FASTQ files | [NCBI SRA](https://www.ncbi.nlm.nih.gov/sra) — accession IDs in `srr_ids.txt` |
| mm10 reference genome | [UCSC Genome Browser](https://hgdownload.soe.ucsc.edu/goldenPath/mm10/bigZips/) |

---

## References

- Robinson MD, McCarthy DJ, Smyth GK. edgeR: a Bioconductor package for differential expression analysis of digital gene expression data. *Bioinformatics*. 2010;26(1):139–40.
- Chen Y, Lun ATL, Smyth GK. From reads to genes to pathways: differential expression analysis of RNA-Seq experiments using Rsubread and the edgeR quasi-likelihood pipeline. *F1000Research*. 2016;5:1438.
- [edgeR User Guide (Bioconductor)](https://bioconductor.org/packages/release/bioc/vignettes/edgeR/inst/doc/edgeRUsersGuide.pdf)

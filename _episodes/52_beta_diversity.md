---
title: "Beta diversity"
teaching: 60
exercises: 60
questions:
- "What is beta diversity?"
- "What common alpha diversity indices are there?"
- "How to compare alpha diversity between samples?"
objectives:
- "Running alpha diversity analysis using phyloseq DivNet."
- "Interpreting alpha diversity measures."
- "Statistical comparison of diversity indices usign hill numbers."
- "Extracting data from R objects."
- "Using ggplot2."
keypoints:
- "Different alpha diversity indices emphasize on different aspects of alpha diversity. Make choices based on your questions."
- "Hill numbers are linear while original alpha diversicy index values are not."
---

>## Prerequisites: 
> - R packages:
  - phyloseq
  - ggplot2
  - gridExtra
  - magrittr
  - picante
  - DivNet
  - reshape2
{: .prereq}

## Set up

#### Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Save your new notebook in the same directory as
the rest of your workshop materials (e.g. Viriomic-Workshop/day5_ecology/).

#### Installing packages
Installing phyloseq
~~~
install.packages("BiocManager")
library(BiocManager)
BiocManager::install("phyloseq")
~~~

Installing DivNet
~~~
install.packages(“remotes”)
library(remotes)
remotes::install_github("adw96/breakaway")
remotes::install_github("adw96/DivNet")
~~~

#### Load packages
Create a new R code chunk and load required packages using library().
~~~
```{r}
library(phyloseq)
library(ggplot2)
library(gridExtra)
library(magrittr)
library(picante)
library(DivNet)
library(reshape2)
```
~~~
#### Load data
Load the ASV table, taxonomy table, and sample metadata using the read.table()
function. Use read_tree() to import the phylogenetic tree. We’re loading the data
again because we will actually be using the phyloseq package to calculate some of
the diversity metrics
~~~
```{r}
counts <- read.table("bin_count_table.tsv",
sep = "\t",
header = TRUE,
row.names = 1)
taxonomy <- read.table("taxonomy_columns.txt",
sep = "\t",
header = TRUE,
row.names = 1,
na.strings = c("", "NA"))
sample_data <- read.table("sample_metadata.txt",
sep = "\t",
header = TRUE,
row.names = 2)
# phylogenetic tree of bins
tree <- read_tree("bin_FastTree.newick")
```
~~~

## Alpha diversity with phlyloseq
Alpha diversity metrics measure species richness and evenness within samples.
Unlike ordination and beta diversity, alpha diversity is a within-sample measure that is
independent from other samples (although you could choose to pool samples by

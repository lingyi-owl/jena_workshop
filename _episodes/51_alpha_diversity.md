---
title: "Alpha diversity"
teaching: 30
exercises: 60
questions:
- "What is alpha diversity?"
- "What are the common alpha diversity indices?"
- "How to compare alpha diversity between samples"
objectives:
- "Run alpha diversity analysis using DivNet."
- "Transform alpha diversity indice values into hill numbers and compare them between samples."
keypoints:
- "Different alpha diversity indices emphasize on different aspects of alpha diversity. Make choices based on your questions."
- "Hill numbers are linear while original alpha diversicy index values are not."
---

## Pre-requisites

R packages:
- phyloseq
- ggplot2
- gridExtra
- magrittr
- picante
- DivNet
- reshape2


## Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Save your new notebook in the same directory as
the rest of your workshop materials (e.g. Viriomic-Workshop/day5_ecology/).

## Installing packages
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

{% include links.md %}

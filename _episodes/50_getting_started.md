---
start: True
title: Getting started
teaching: 15
exercises: 0
questions:
- "set up the environment for analysis"
keypoints:
- "set up the environment for analysis"
---

>## Prerequisites: 
> - Installation of R and RStudio
  - R version 3.5 or higher is required (current version is 4.1.0)
> - Intro to RStudio ([Reyes et al Nature 2010](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2919852/))
> - Intro to R Notebooks 
> - R packages:
  - phyloseq
  - ggplot2
  - gridExtra
  - magrittr
  - picante
  - DivNet
  - reshape2
> - Data from pevious workshops:
  - contig abundance table
  - contig taxonomy table
  - sample metadata table
  - taxonomy phylogenetic tree
{: .prereq}

## Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Here is an example:

~~~
---
title: "Alpha diversity"
author: "Ling-Yi Wu"
date: "28/01/2022"
output: html_notebook
---
~~~
{% include links.md %}

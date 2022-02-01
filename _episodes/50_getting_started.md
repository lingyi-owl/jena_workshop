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
> - Intro to R and RStudio ([video](https://youtu.be/lVKMsaWju8w))
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

Save your new notebook in the same directory as the rest of your workshop materials
(e.g. Viriomic-Workshop/day5_ecology/).

## Load packages
Create a new R code chunk either using a keyboard shortcut (Ctrl + Alt + I or
MacOS: Cmd + Option + I) or the green insert button. Load required packages
using the library() function. Here’s my code block:

```{r}
library(featuretable)
library(ggplot2)
library(gridExtra)
library(magrittr)
```
{% include links.md %}

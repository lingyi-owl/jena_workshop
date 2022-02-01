---
start: True
title: Exploring data
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

## Set up
#### Set up an R Notebook
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

#### Load packages
Create a new R code chunk either using a keyboard shortcut (Ctrl + Alt + I or
MacOS: Cmd + Option + I) or the green insert button. Load required packages
using the library() function. Here’s my code block:

~~~
```{r}
library(featuretable)
library(ggplot2)
library(gridExtra)
library(magrittr)
```
~~~

R will throw an error if any of the packages are not installed.

#### Load data

Before you load the data, make sure that the name of the contig column matches in the
contig count table and the taxonomy table. They will not match by default. My labels
for both are “contig_id”
Load the contig count table, taxonomy table, and sample metadata using the read.table()
function:

~~~
```{r}
# ASV table with raw counts
counts <- read.table("data/contig_count_table.tsv",
# tells the function that rows are separated by a tab
sep = "\t",
# logical confirming that the table already has headers
header = TRUE,
# make the first column of the table into row names
row.names = 1)
# taxonomy info for the ASVs
taxonomy <- read.table("data/taxonomy_columns.txt",
sep = "\t",
header = TRUE,
row.names = 1,
na.strings = c("", "NA")
# sample metadata
sample_data <- read.table("data/sample_metadata.txt",
sep = "\t",
header = TRUE,
row.names = 2)
```
~~~

Remember that lines starting with a “#” are comments. You can see here that
comments can be made within R functions without interfering with running the
function.
Also make sure to take note of the file paths: The current working directory of an R
Notebook is the directory in which the R Notebook is saved. For example, because the
sample metadata is saved in the same folder, we don’t have to provide a file path
beyond the name of the file.

## Data Overview with FeatureTable
FeatureTable is an R package designed to hold and manipulate sequencing data.
FeatureTable makes it very easy to filter data and make abundance plots.

#### Building and accessing a FeatureTable object
First, let's make a FeatureTable object with our data:
{% include links.md %}

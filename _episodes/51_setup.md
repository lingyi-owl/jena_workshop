---
title: "Exploring data"
teaching: 30
exercises: 60
questions:
- "What are the differences between absulte abundance and relative abundance?"
- "Why is it necessary to filter out low-abundance features?"
objectives:
- "Using R Notebooks."
- "Loading data and packages into R."
- "Using the FeatureTable package."
- "Saving R objects with save()."
- "Plotting relative abundance."
- "Filtering ASVs to exclude low-abundance features."
keypoints:
- "Plot relative abundance plots to get a feeling with the data."
- "Picking thresholds for filtering can be tricky. Play with the thresholds to filter data based on your questions and your data."
---

>## Prerequisites: 
> - Installation of R and RStudio
  - R version 3.5 or higher is required (current version is 4.1.0)
> - Intro to R and RStudio ([video](https://youtu.be/lVKMsaWju8w))
> - Intro to R Notebooks 
> - R packages:
  - ggplot2
  - FeatureTable
  - gridExtra
  - Magrittr
> - Data from pevious workshops:
  - ASV abundance table
  - taxonomy table
  - sample metadata table
  - taxonomy phylogenetic tree
{: .prereq}

## Set up
#### Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Here is an example:

~~~
---
title: "Exploring data"
author: "Ling-Yi Wu"
date: "25/02/2022"
output: html_notebook
---
~~~

Save your new notebook in the same directory as the rest of your workshop materials
(e.g. Viriomic-Workshop/day5_ecology/).

#### Install packages

Installing environment
~~~
```{r}
conda create --name myenv --file day5_envs.txt
```
~~~


Installing FeatureTable

FeatureTable can be installed using the typical `install.packages()` command, but
you need to download a file first. Download the latest version [here](https://github.com/mooreryan/featuretable/releases/tag/v0.0.10) (you want the
tar.gz file). Then, run this command:
~~~
```{r}
install.packages("/path/to/featuretable_0.0.10.tar.gz", repos = NULL)
```
~~~

#### Load packages
Create a new R code chunk either using a keyboard shortcut (`Ctrl + Alt + I` or
MacOS: `Cmd + Option + I`) or the green insert button. Load required packages
using the `library()` function. Here’s my code block:

~~~
```{r}
library(featuretable)
library(ggplot2)
library(gridExtra)
library(magrittr)
```
~~~

R will throw an error if any of the packages are not installed.

#### Download the data

The input data for the workshop today can be found [here](https://github.com/lingyi-owl/jena_workshop/tree/gh-pages/data).Store these data in the directory of Viriomic-Workshop/day5_ecology/data/.

#### Load data

Before you load the data, make sure that the name of the feature(ASV) column matches in the
feature(ASV) count table and the taxonomy table. They will not match by default. My labels
for both are “ASV”
Load the ASV count table, taxonomy table, and sample metadata using the `read.table()`
function:

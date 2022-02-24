---
title: "Rstudio set up from Conda environment, package installment and data download"
teaching: 0
exercises: 30
questions:
objectives:
- "Create Conda environment from the terminal"
- "Install few extra R packages"
- "Loading data and packages into R."
- "Using the FeatureTable package."

keypoints:
---

>## Prerequisites:  
> - R packages:
  - ggplot2
  - gridExtra
  - Magrittr
  - phyloseq
  - picante
  - reshape2
  - biplotr
  - zCompositions
  - vegan
  - ggdendro
  - ALDEx2
  - ComplexHeatmap
  - FeatureTable 
  - breakaway
  - DivNet
> - Data:
  - ASV abundance table
  - taxonomy table
  - sample metadata table
  - taxonomy phylogenetic tree
  - env data by month table
{: .prereq}


Save your new R script in the same directory as the rest of your workshop materials
(e.g. Viriomic-Workshop/day5_ecology/).

#### Install packages

Installing environment 

Download the environment file from [here](https://raw.githubusercontent.com/lingyi-owl/jena_workshop/gh-pages/data/day5_env.txt). 
Put the file in your working directory(e.g. Viriomic-Workshop/day5_ecology/). Navigate into your working directory in the terminal.
Create the conda environment using the command lines below explicitely. Note that you should use `--file` instead of `-f` in the first line.
Export the library using the third line. Replace prakXXX with your own computer ID.
Next initiate Rstudio from the shell.
~~~
conda create --name day5_env --file day5_envs.txt
conda activate day5_env
export LD_LIBRARY_PATH=/mnt/local/prakXXX/anaconda3/envs/day5_env/lib
rstudio
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

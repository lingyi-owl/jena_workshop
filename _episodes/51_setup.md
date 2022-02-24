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

#### Install environment 

Open the terminal.
Navigate into your working directory in the terminal(e.g. day5/). 
Download the environment file.
Create the conda environment using the command lines below explicitely. Note that you should use `--file` instead of `-f` in the line of `conda create ...`.
Activate the environment.
Export a library into the environment. Replace prakXXX with your own computer ID in the line of `export ...`.
Initiate Rstudio from the shell.
~~~
cd day5/
wget https://raw.githubusercontent.com/lingyi-owl/jena_workshop/gh-pages/data/day5_env.txt
conda create --name day5_env --file day5_envs.txt
conda activate day5_env
export LD_LIBRARY_PATH=/mnt/local/prakXXX/anaconda3/envs/day5_env/lib
rstudio
~~~

#### Install extra packages

In RStudio interface openned from the previous step, install the packages below.

Download the [FeatureTable package](https://github.com/mooreryan/featuretable/releases/tag/v0.0.10) to your working directory.
Install the package in RStudio using the scripts below. Skip updates when installing `breakaway`. It might take a while to install `breakaway`.
~~~
```{r}
# install featuretable
install.packages("featuretable_0.0.10.tar.gz", repos = NULL)
install.packages("remotes")
library(remotes)
# install DivNet
remotes::install_github("adw96/breakaway")
remotes::install_github("adw96/DivNet")
# install biplotr
remotes::install_github("mooreryan/biplotr")
```
~~~

#### Load packages

Load packages in the order below to avoid conflicts.
~~~
```{r}
library(DivNet)
library(ggplot2)
library(gridExtra)
library(Magrittr)
library(phyloseq)
library(picante)
library(reshape2)
library(biplotr)
library(zCompositions)
library(vegan)
library(ggdendro)
library(ALDEx2)
library(ComplexHeatmap)
library(FeatureTable)
library(breakaway)
library(ComplexHeatmap
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

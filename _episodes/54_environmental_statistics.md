---
title: "Beta diversity"
teaching: 60
exercises: 60
questions:
- "What is beta diversity?"
- "How to compare beta diversity between samples?"
objectives:
- "Replacing zeros with zCompositions."
- "Centered log-ratio transformation of the count table."
- "Calculating distance/dissimilarity measures with base R, vegan, and phyloseq."
- "Performing PCA with biplotr."
- "Interpreting beta diversity measures through ordination."
- "Using ggplot2."
keypoints:
- "Understand the similairties and dissimilarites of different beta diversity/distance matrices."
- "Aitchison distance is the distance between samples or features within simplex space. We use Aitchison distance in compositional data analysis."
---

>## Prerequisites: 
> - R packages:
  - FeatureTable
  - ggplot2
  - gridExtra
  - magrittr
  - biplotr
  - zCompositions
  - vegan
  - ggdendro
{: .prereq}

## Set up

#### Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Save your new notebook in the same directory as
the rest of your workshop materials (e.g. Viriomic-Workshop/day5_ecology/).

#### Load packages
Create a new R code chunk and load required packages using library().
~~~
```{r}
library(featuretable)
library(ggplot2)
library(zCompositions)
library(vegan)
library(reshape2)
```
~~~
#### Load data

We’re going to be using the FeatureTable object we saved earlier, so we can just load
that instead of loading all the data separately again.
~~~
```{r}
load("data/pond_featuretable.Rdata")
```
~~~

## ASVs and the environment
So far, apart from filtering and some relative abundance plots, we've been looking at
everything at the sample level. Here, we'll start looking at how individual ASVs are
affected by the environment.
This experiment only had three timepoints, which isn’t many, but it’s enough to
demonstrate a few techniques.

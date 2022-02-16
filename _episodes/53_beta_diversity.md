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
- "Use clr transformation to normalize the data"
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

#### Installing packages
Installing biplotr

Follow the [instructions](https://github.com/mooreryan/biplotr) to install biplotr.


#### Load packages
Create a new R code chunk and load required packages using library().
~~~
```{r}
library(featuretable)
library(ggplot2)
library(gridExtra)
library(magrittr)
library(biplotr)
library(zCompositions)
library(vegan)
library(ggdendro)
```
~~~
#### Load data
Load the ASV table, taxonomy table, and sample metadata using the read.table()
function. Use read_tree() to import the phylogenetic tree. We’re loading the data
again because we will actually be using the phyloseq package to calculate some of
the diversity metrics
~~~
```{r}
load("data/MO_twins/mo1k_featuretable.Rdata")
mo1k_ft
```
~~~

## Beta diversity ordination

#### CLR transformation
~~~
```{r}
mo1k_clr <- mo1k_ft$core_microbiome(
min_sample_proportion = 0.2,
detection_limit = 5)$
# Replace zeros with zCompositions cmultRepl GBM method
replace_zeros(use_cmultRepl = TRUE,
method = "GBM")$
# Then take CLR with base 2
clr()
print(mo1k_clr)
```
~~~

### plot ordination
~~~
```{r}
# perform PCA using the biplotr package and store it as object p
p <- mo1k_clr$pca_biplot(use_biplotr = TRUE,
# give biplotr access to sample metadata
include_sample_data = TRUE,
# includes or excludes arrows on the plot
arrows = FALSE,
# color points by Month
point_color = "family")
# plot the PCA you saved above
p$biplot
```
~~~

### modify plots
~~~
```{r}
# access the PCA plot itself from the saved object
p$plot_elem$biplot_chart_base +
# plot the first and second principal components and
# color the points by month and change the shape to indicate size
# fraction
geom_point(aes(x = PC1, y = PC2, color = family, shape = relationship),
size = 3) +
# set the colors to the first three values in the Kelly color
# change the ggplot2 theme
theme_classic() +
# change the plot title (to nothing, in this case)
ggtitle("PCA of clr-transformed abundances")
```
~~~

---
title: "Exploring data"
teaching: 30
exercises: 60
questions:
- "Using R Notebooks."
- "Loading data and packages into R."
- "Using the FeatureTable package."
- "Saving R objects with save()."
- "Plotting relative abundance."
- "Filtering ASVs to exclude low-abundance features."
keypoints:
- "Having a data structure would bring many benefits in a long term."
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

#### Install packages

Installing FeatureTable

FeatureTable can be installed using the typical install.packages() command, but
you need to download a file first. Download the latest version [here](https://github.com/mooreryan/featuretable/releases/tag/v0.0.10) (you want the
tar.gz file). Then, run this command:
```{r}
install.packages("/path/to/featuretable_0.0.10.tar.gz", repos = NULL)
```

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
~~~
```{r}
pond_ft <- FeatureTable$new(t(counts), taxonomy, sample_data)
```
~~~
This command puts the contig counts, taxonomy calls, and sample metadata into a
feature table object, where they can be easily manipulated. The order of the data is
important here. The t() function means "transpose". We used it here
because FeatureTable expects the rows to be samples and the columns to be contigs.
To get a summary of your FeatureTable object, use this command:
~~~
print(pond_ft)
~~~

Your summary should look like this:
```{r}
FeatureTable: 
  data         -- 24 samples, 3796 features
  feature_data -- 8 covariates
  sample_data  -- 14 covariates
```
Make sure that the dimensions in the summary match correctly to the data! The data
category is the contig count table, the feature_data is the taxonomy, and the sample_data is
the sample metadata.
You can also access specific pieces of your FeatureTable object using the $:
```{r}
crass_ft$data[1:5,1:5]
```
The above command will show you the first 5 rows and columns of the contig count table. Try
on your own to view the first 5 rows of the feature_data, then the first 5 columns of the
sample_data.

#### Saving data
R objects and other data can be saved as R objects to make them easy to use again
later. We’re going to save the FeatureTable object now so we don’t have to build it
again in later analysis.
save(crass_ft, file = "data/crass_featuretable.Rdata")
crass_featuretable.Rdata will be created in whichever directory your R Notebook is
saved. You can save all types of Rdata, not just FeatureTable objects. It’s especially
useful in collaboration so you’re not passing several documents back and forth.
Later, when we want to use the FeatureTable again, use this command:
load("data/crass_featuretable.Rdata")

#### Abundance plots with FeatureTable
Let's take a quick look at the data before we get started. Here's how you make a
very basic plot of contig abundance in each sample:
```{r}
crass_ft$plot()
```

First, let’s talk about the code and then we’ll get to the plot. We used the $ before to
access parts of the FeatureTable object, but we can also use it to access functions. It’s
similar to the %>% function from magrittr, in that it feeds the object preceding the $ to
the following function. Here, we used the $ to apply the FeatureTable plot() function
to crass_ft. Keep in mind that the plot() function here is not the same as the base R
plot() function. When applied to a FeatureTable object, $plot() is actually an alias
for the function featuretable.plot()which is actually a ggplot2 wrapper. You’ll see
more about the $ operator and FeatureTable functions coming up. The topic is also
covered in the FeatureTable vignettes.
Onto the plot. First off, the x axis labels aren’t very informative right now. We can
replace the axis labels with more meaningful names using scale_x_discrete():
```{r}
crass_ft$plot() + scale_x_discrete(labels=crass_ft$sample_data$Sample)
```
{% include links.md %}

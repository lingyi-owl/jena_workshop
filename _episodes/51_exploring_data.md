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
- 'Picking thresholds for filtering can be tricky. Play with the thresholds to filter data based on your questions and your data."
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
title: "Exploring data"
author: "Ling-Yi Wu"
date: "25/02/2022"
output: html_notebook
---
~~~

Save your new notebook in the same directory as the rest of your workshop materials
(e.g. Viriomic-Workshop/day5_ecology/).

#### Install packages

Installing FeatureTable

FeatureTable can be installed using the typical `install.packages()` command, but
you need to download a file first. Download the latest version [here](https://github.com/mooreryan/featuretable/releases/tag/v0.0.10) (you want the
tar.gz file). Then, run this command:
```{r}
install.packages("/path/to/featuretable_0.0.10.tar.gz", repos = NULL)
```

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

~~~
```{r}
# ASV table with raw counts
counts <- read.table("data/asv_count_table.txt",
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
na.strings = c("", "NA"))
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
sample metadata is saved in the directory of "data" in the current working directory, 
we provide a relative file path beyond the name of the file.

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
This command puts the ASV counts, taxonomy calls, and sample metadata into a
feature table object, where they can be easily manipulated. The order of the data is
important here. The `t()` function means "transpose". We used it here
because FeatureTable expects the rows to be samples and the columns to be features.
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
Make sure that the dimensions in the summary match correctly to the data! The `data`
category is the ASV count table, the `feature_data` is the taxonomy, and the `sample_data` is
the sample metadata.
You can also access specific pieces of your FeatureTable object using the $:

~~~
```{r}
pond_ft$data[1:5,1:5]
```
~~~

The above command will show you the first 5 rows and columns of the contig count table. Try
on your own to view the first 5 rows of the feature_data, then the first 5 columns of the
sample_data.

#### Saving data
R objects and other data can be saved as R objects to make them easy to use again
later. We’re going to save the FeatureTable object now so we don’t have to build it
again in later analysis.
~~~
```r
save(pond_ft, file = "data/pond_featuretable.Rdata")
```
~~~
`pond_featuretable.Rdata` will be created in whichever directory your R Notebook is
saved. You can save all types of Rdata, not just FeatureTable objects. It’s especially
useful in collaboration so you’re not passing several documents back and forth.
Later, when we want to use the FeatureTable again, use this command:
load("data/pond_featuretable.Rdata")

#### Abundance plots with FeatureTable
Let's take a quick look at the data before we get started. Here's how you make a
very basic plot of ASV abundance in each sample:

~~~
```{r}
pond_ft$plot()
```
~~~

First, let’s talk about the code and then we’ll get to the plot. We used the `$` before to
access parts of the FeatureTable object, but we can also use it to access functions. It’s
similar to the `%>%` function from magrittr, in that it feeds the object preceding the `$` to
the following function. Here, we used the `$` to apply the FeatureTable `plot()` function
to pond_ft. Keep in mind that the `plot()` function here is not the same as the base R
`plot()` function. When applied to a FeatureTable object, `$plot()` is actually an alias
for the function `featuretable.plot()` which is actually a ggplot2 wrapper. You’ll see
more about the $ operator and FeatureTable functions coming up. The topic is also
covered in the FeatureTable vignettes.

Onto the plot. First off, the x axis labels aren’t very informative right now. We can
replace the axis labels with more meaningful names using `scale_x_discrete()`:

~~~
```{r}
pond_ft$plot() + scale_x_discrete(labels=pond_ft$sample_data$Sample)
```
~~~
{% include links.md %}

The `scale_x_discrete()` function is from ggplot2, which is what featuretable is using to make plots. 
You’ll see more about this later on. For now, let’s talk about the plot’s appearance. Notice that 
only 8 ASVs were assigned colors, while the rest were lumped into the “Other” category. The FeatureTable default is to
highlight only the top 8 most abundant ASVs.The bars are all different heights because they represent raw ASV counts for
each sample, which makes it hard to see patterns, especially in samples with fewer
reads overall. The samples are also a little jumbled because they’re still sorted
alphabetically by SRA accession, rather than sample name. That’s because the
featuretable automatically plots row names on the x axis, which in our case are the
SRA accessions. We replaced the names, but not the underlying plotting, so the
samples stayed in the same order.

#### Relative Abundance
Let’s view ASVs in terms of relative abundance rather than raw counts. To do this in
FeatureTable, run this code:

~~~
```{r}
pond_ft$
# applies the relative abundance function to samples before plotting
map_samples(relative_abundance)$
plot() + scale_x_discrete(labels=pond_ft$sample_data$Sample)
```
~~~
Again we apply the FeatureTable plot function to pond_ft, but we have some new
code in there. Now, `pond_ft` is run through the `map_samples(relative_abundance)`
and the output of that function is fed to `plot()`. If you use the built-in help feature
`(?map_samples())`, you’ll see that the `map_samples()` function is a helper function for
the more general `map()` in FeatureTable and is shorthand for `map(ft, "samples",
relative_abundance)`. For example, you could also write
`pond_ft$map_samples(relative_abundance)` as `map(pond_ft, "samples",
relative_abundance)`.

#### Grouping samples for plotting
Let's break the data apart a bit and see if we can identify related metadata. Looking at
the metadata, the obvious divisions of samples are by Month and Fraction. Let's start
with Month:

~~~
```{r}
pond_ft$
collapse_samples(Month)$
map_samples(relative_abundance)$
# the plot can be customized using ggplot2 functions
plot(num_features = 12, # plot the top 12 features (ASVs)
legend_title = "ASV", # change legend title
xlab = "Month", # change x axis title label
ylab = "Relative Abundance", # change y axis title label
axis.text.x = element_text(angle = 0)) # change x axis text angle
```
~~~
What’s happening to `pond_ft` in this new code? Try to talk yourself through the
process before reading further.

First, we collapse the samples in `pond_ft` by month using `collapse_samples()`.
Then, relative abundance is calculated for each month using `map_samples()`. Finally,
we plot the result.
If you have used ggplot2 before, the arguments in the plot function should be familiar
to you because the FeatureTable plot function is a wrapper for ggplot2. The only
argument that is unique to FeatureTable is `num_features`, which was used to change
the number of highlighted ASVs (i.e. the number of ASVs assigned a color).

The months are out of order, though. Again, we have things in alphabetical order on the x
axis, but in this case we would prefer them to be in chronological order. To fix this, we
can use `scale_x_discrete()` again.

~~~
```{r}
pond_ft$
collapse_samples(Month)$
map_samples(relative_abundance)$
plot(num_features = 12,
legend_title = "ASV",
xlab = "Month",
ylab = "Relative Abundance",
axis.text.x = element_text(angle = 0)) +
# reorder x axis labels
scale_x_discrete(limits=c("October", "November", "December"))
```
~~~

Make a similar chart for size fraction. Are there possible differences in the microbial
community based on size fraction?

## Filtering ASVs

Filtering is important because low abundance ASVs (e.g. singletons, doubletons) are
more likely than high abundance ASVs to be the result of sequencing errors and are
often "noise". ASVs are more robust to this than OTUs, but are still not perfect.
Additionally, unfiltered ASV and OTU tables are sparse (i.e. they contain many zeros),
which is not ideal for most statistics; It is hard to compare samples based on ASVs that
appear in only a few samples.
FeatureTable makes filtering very easy. First, let’s remove ASVs that are in fewer than
25% of samples (0.25 * 24 = 6) or that have a raw count below 20.

~~~
```{r}
pond_core_25 <- pond_ft$core_microbiome(
min_sample_proportion = 0.25,
detection_limit = 20)
```
~~~

Here we used `core_microbiome()` to do the filtering and stored the filtered data as a
new FeatureTable object. To check on the new feature table and see the results of the
filtering run:

~~~
print(pond_core_25) # should have 24 samples and 1658 features
~~~

#### Exploring the core microbiome

In the above examples, I picked 5 for the detection limit (i.e. an ASV with a count of
less than 5 will be removed), but it's an arbitrary cutoff. That being said, any detection
limit is generally arbitrary. The minimum sample proportion is also fairly arbitrary. I
picked it because it was small enough not to exclude an entire month or size fraction
from analysis, but also big enough to filter out rarer OTUs.
Normally, you'd probably want to fiddle around with the detection limit and
minimum sample proportion and see if the data changes too much. You can do that
a bit in the next block of code.

~~~
```{r}
# Set the detection limit required for each ASV to be included
detection_limit <- 20
# Set a range sample proportions required for each ASV to be included
# This will make an array of sample proportions (proportion of samples
# that an ASV must appear in to be included)
# starting with 10% and moving up to 100% by increments of 10%
proportions <- seq(from = 0.1, to = 1, by = 0.1)
# Get the number of remaining ASVs after filtering out ASVs that don't
# meet the detection limit and sample proportion requirements.
core_feature_counts <- sapply(proportions, function(sample_proportion)
{
pond_ft$
core_microbiome(detection_limit = detection_limit,
min_sample_proportion = sample_proportion)$
num_features()
})
# You can also capture what is sort of like the "rare" microbiome
# This one holds all the ASVs that drop out
anti_core_feature_counts <- sapply(proportions,
function(sample_proportion) {
pond_ft$
core_microbiome(detection_limit = detection_limit,
max_sample_proportion = sample_proportion)$
num_features()
})
# Put everything into a single data frame for plotting
core_plot_data <- data.frame(
sample_proportion = proportions * 100,
core_features = core_feature_counts,
anti_core_features = anti_core_feature_counts
)
# Plot!
# core microbiome
core <- core_plot_data %>%
ggplot(aes(x = sample_proportion, y = core_features)) +
geom_point() +
ggtitle("Core microbiome") +
xlab("Min. Sample %") +
ylab("Number of Features") +
theme_bw()
# "rare" microbiome
rare <- core_plot_data %>%
ggplot(aes(x = sample_proportion, y = anti_core_features)) +
geom_point() +
ggtitle("Rare microbiome") +
xlab("Max Sample %") +
ylab("Number of Features") +
theme_bw()
# put the two graphs next to each other in the same window
grid.arrange(core, rare, ncol = 2)
```
~~~

With a detection limit of 20, a lot of ASVs drop out right at the beginning and then the
drop out rate slows. Do you get different patterns if you change the detection limit?
Try to talk yourself through each part of the code and use the built-in
help docs and Google to make sense of the parts of the code you don’t understand.

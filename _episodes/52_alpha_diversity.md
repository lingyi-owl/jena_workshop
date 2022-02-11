---
title: "Alpha diversity"
teaching: 30
exercises: 60
questions:
- "What is alpha diversity?"
- "What common alpha diversity indices are there?"
- "How to compare alpha diversity between samples?"
objectives:
- "Running alpha diversity analysis using phyloseq & DivNet."
- "Interpreting alpha diversity measures."
- "Statistical comparison of diversity indices usign hill numbers."
- "Extracting data from R objects."
- "Using ggplot2."
keypoints:
- "Different alpha diversity indices emphasize on different aspects of alpha diversity. Make choices based on your questions."
- "Hill numbers are linear while original alpha diversicy index values are not."
---

>## Prerequisites: 
> - R packages:
  - phyloseq
  - ggplot2
  - gridExtra
  - magrittr
  - picante
  - DivNet
  - reshape2
{: .prereq}

## Set up

#### Set up an R Notebook
Open RStudio and create a new R Notebook. Rename the notebook in the “title” field
and add fields for author and date. Save your new notebook in the same directory as
the rest of your workshop materials (e.g. Viriomic-Workshop/day5_ecology/).

#### Installing packages
Installing phyloseq
~~~
install.packages("BiocManager")
library(BiocManager)
BiocManager::install("phyloseq")
~~~

Installing DivNet
~~~
install.packages(“remotes”)
library(remotes)
remotes::install_github("adw96/breakaway")
remotes::install_github("adw96/DivNet")
~~~

#### Load packages
Create a new R code chunk and load required packages using library().
~~~
```{r}
library(phyloseq)
library(ggplot2)
library(gridExtra)
library(magrittr)
library(picante)
library(DivNet)
library(reshape2)
```
~~~
#### Load data
Load the ASV table, taxonomy table, and sample metadata using the read.table()
function. Use read_tree() to import the phylogenetic tree. We’re loading the data
again because we will actually be using the phyloseq package to calculate some of
the diversity metrics
~~~
```{r}
counts <- read.table("bin_count_table.tsv",
sep = "\t",
header = TRUE,
row.names = 1)
taxonomy <- read.table("taxonomy_columns.txt",
sep = "\t",
header = TRUE,
row.names = 1,
na.strings = c("", "NA"))
sample_data <- read.table("sample_metadata.txt",
sep = "\t",
header = TRUE,
row.names = 2)
# phylogenetic tree of bins
tree <- read_tree("bin_FastTree.newick")
```
~~~

## Alpha diversity with phlyloseq
Alpha diversity metrics measure species richness and evenness within samples.
Unlike ordination and beta diversity, alpha diversity is a within-sample measure that is
independent from other samples (although you could choose to pool samples by
categories).

#### Create a phyloseq object
~~~
```{r}
# make a and save phyloseq object
crass_phyloseq <- phyloseq(
otu_table(counts, taxa_are_rows = T),
tax_table(as.matrix(taxonomy)),
sample_data(sample_data)
)
save(crass_phyloseq, file = "crass_phyloseq.Rdata")
~~~

#### Plot alpha diversity using measures from phyloseq
~~~
```{r}
observed_otus_plot <- plot_richness(pond_phyloseq,
x = "Sample",
measures = c("Observed"),
color = "Month",
shape = "Fraction") +
geom_point(size = 3) +
theme_bw() +
theme(axis.text.x = element_text(angle = 90)) +
labs(y = "Observed ASVs")
~~~

>## Challenge: Make alpha diversity plots of Chao1, Shannon, and Simpson
> Change the value in "measures" to plot Chao1, Shannon and Simpson.
{: .challenge}

#### Plot alpha diversity using Hill Numbers
~~~
```{r}
shannon <- estimate_richness(crass_phyloseq,
measures = c("Shannon"))
hill_shannon <- sapply(shannon, function(x) {exp(x)}) %>% as.matrix()
row.names(hill_shannon) <- row.names(shannon)
hill_shannon_meta <- merge(hill_shannon, sample_data, by =
"row.names")
colnames(hill_shannon_meta)[colnames(hill_shannon_meta) == "Shannon"]
<- "Hill"
hill_shannon_plot <- ggplot(data = hill_shannon_meta) +
geom_point(aes(x = Sample,
y = Hill,
color = Month,
shape = Fraction),
size = 3) +
theme_bw() +
theme(axis.text.x = element_text(angle = 90)) +
labs(title = "Effective Shannon Diversity Index",
y = "Effective number of species") +
scale_x_discrete(limits = sample_order)
~~~

>## Challenge: Plot alpha diversity using Hill Numbers from Simpson
> 1. get the simpson index values from the phyloseq object and convert to a matrix.
> 2. calculate Hill numbers and convert to a matrix.
> the formula for Hill numbers from Simpson is 1/(1-D).
> 3. give the hill_simpson matrix some row names.
> 4. merge the hill numbers with the sample metadata based on rownames.
> 5. change the name of the column from Simpson to Hill.
> 6. make and store a ggplot of the Simpson Hill numbers
{: .challenge}

#### Arrange all 6 plots on a single grid
You should run that last bit of code (the grid.arrange()) in the R console to get the
plots to appear in the plots tab. From there, you can use the zoom feature to open up
the plots in a new, bigger window.
~~~
```{r}
grid.arrange(observed_otus_plot,
chao1_plot,
shannon_plot,
simpson_plot,
hill_shannon_plot,
hill_simpson_plot,
ncol = 2)
```
~~~

>## Discussion: 
> What do the results of each index tell you about the diversity of the microbial community in each sample?
{: .discussion}

#### Plot alpha diversity using phylogenetic information
Phylogenetic trees can also be taken into account when measuring diversity. Faith's
PD (phylogenetic distance) is one such measure and is equal to the sum of the
lengths of the branches of all members of a sample (or other group) on a phylogeny.
We can calculate it using the `pd` function from the package picante.

~~~
```{r}
faiths <- pd(t(counts), # samples should be rows, ASVs as columns
tree,
include.root = F) # our tree is not rooted
faiths_meta <- merge(faiths, sample_data, by = "row.names")
faiths_plot <- ggplot(data = faiths_meta) +
geom_point(aes(x = Sample,
y = PD,
color = Month,
shape = Fraction),
size = 3) +
theme_bw() +
theme(axis.text.x = element_text(angle = 90)) +
labs(title = "Faith's phylogenetic distance",
y = "Faith's PD")
faiths_plot
```
~~~

## Significance testing
As long as our calculated alpha diversity metrics meet the correct assumptions of ANOVA, we
can use an ANOVA to tell if there are significant differences in sample alpha diversity
based on the categorical data (i.e. month, size fraction). If our data does not meet the
assumptions, we can use a Kruskal-Wallis test instead.

First, we’ll check to see if the Shannon Hill numbers meet the assumptions of an ANOVA. Then, we’ll run either the ANOVA or a Kruskal-Wallis test. 
~~~
```{r}
# test for normal distribution of effective number of species values
from Shannon
hill_shannon_meta$Hill %>% shapiro.test()
# plot distribution of effective number of species values
hill_shannon_meta %>%
ggplot() +
geom_density(aes(x = Hill))
# test for homogeneity of variances
bartlett.test(Hill ~ interaction(Month, Fraction), data = hill_meta)
# use an ANOVA to test for differences among categories
anova_shannon_hill <- aov(Hill ~ Month*Fraction, data =
hill_shannon_meta)
summary(anova_shannon_hill)
~~~
## Alpha diversity with Divnet

The phyloseq package calculates alpha diversity metrics based solely on the numbers
in the ASV table. If you were dealing with true counts obtained from an exhaustive
ecological survey, calculating diversity metrics as above would be completely valid.
Some of the above diversity metrics do try to account for "missing" taxa (e.g. Chao1),
but most are not designed to work with compositional data.

DivNet is compatible with Compositional Data Analysis (CoDA). Like the Chao1 metric,
DivNet uses the existing data to estimate the number of unobserved taxa. But, the
DivNet model is more robust and isn't nearly as reliant on singletons, which are often
the result of sequencing error. DivNet also takes into account that the abundances in
the ASV table are not true counts. DivNet can also leverage sample metadata to
enhance model fitting. For example, the four quadrants sampled in the pond study
are really replicates, leaving size fraction and month as the major sample groups.
With DivNet, we can make that clear to the model and make the diversity estimates
more accurate.

The downside of DivNet is that it's inner-workings are more complicated than other
packages that calculate diversity metrics. The details are in the [DivNet paper](https://academic.oup.com/biostatistics/article/23/1/207/5841114). Briefly,
DivNet uses Monte Carlo estimation to introduce randomness and calculate an
integral for each sample group of interest. DivNet does return plug-in estimates of
diversity measures (the same values you get by treating the ASV abundances as true
counts), but the Monte Carlo estimation allows DivNet to add error bars.

The other downside is that the R version of DivNet has trouble with larger datasets
(like our pond dataset). Luckily, the DivNet algorithm has also now been
implemented in Rust by [Ryan Moore](https://www.mooreryan.com), so you can run larger datasets. You
can find that version of DivNet [here](https://github.com/mooreryan/divnet-rs) and the instructional book [here](https://mooreryan.github.io/divnet-rs-book/).

In this walkthrough, we’ll collapse our ASVs by taxonomy so we can run DivNet
entirely in R. In a real analysis, you would want to run the Rust version DivNet on the
ASV table. With more than 1000 or so ASVs, the R version of DivNet would take a long
time to finish. With more than ~2500, your R is likely to crash. 

{% include links.md %}

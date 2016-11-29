
# Table of contents

- [Overview](#overview)
- [Generating recount-like files](#generating-recount-like-files)
- [Run Rail-RNA](#run-rail-rna)
- [Create recount objects](#create-recount-objects)
- [Submit files](#submit-files)
- [Summary](#summary)
- [Help](#help)


# Overview

If you are interested in contributing your data to [recount](https://jhubiostatistics.shinyapps.io/recount/) you will need to modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new). The form will ask you to submit some information describing your data and information on how we can access the required files. We'll respond as soon as we can and add your data to [recount](https://jhubiostatistics.shinyapps.io/recount/). 

# Generating recount-like files

In order for us to add your files to [recount](https://jhubiostatistics.shinyapps.io/recount/) we first ask you to generate files similar to the ones we already provide. This means that you have to run [Rail-RNA](rail.bio) on your RNA-seq data against the human reference genome hg38. [Rail-RNA](rail.bio) will generate a set of [deliverable files](http://docs.rail.bio/deliverables/) and for [recount](https://jhubiostatistics.shinyapps.io/recount/) we need the cross-sample tables, coverage vectors in bigwig files and the junctions files. Once you have run [Rail-RNA](rail.bio) we need you to run [this set of R scripts](https://github.com/leekgroup/recount-website/tree/master/recount-prep) that create the remaining files we need. Once you have generated the files, you might want to add more phenotype information for your samples. Then you can modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new) and we'll take it from there.

# Run [Rail-RNA](rail.bio)

The first step involves running [Rail-RNA](rail.bio) with your data. You will find more information about how to do so at the [Rail-RNA documentation website](http://docs.rail.bio/). You can run it on the cloud using [Amazon Web Services](http://aws.amazon.com/) [Elastic MapReduce](http://aws.amazon.com/elasticmapreduce/). Note that it's crucial that the [Rail-RNA deliverables](http://docs.rail.bio/deliverables/) include the:

* cross-sample tables: `tsv`,
* coverage vectors: `bw`,
* junction files: `jx`.

For details see the `--deliverables` argument.

# Create recount objects

Once you have the output from [Rail-RNA](rail.bio) you will need to run the [recount-prep](https://github.com/leekgroup/recount-website/tree/master/recount-prep) R scripts. You will most likely need to download the output from [Rail-RNA](rail.bio) to your local system. In order to run these R scripts, you first need to install some dependencies in your system. These are:

* [bwtool](https://github.com/CRG-Barcelona/bwtool)
* [WiggleTools](https://github.com/Ensembl/WiggleTools)
* [wigToBigWig](http://hgdownload.cse.ucsc.edu/admin/exe/)

as well as the following R packages that can be installed with the following R command:

```
source("https://bioconductor.org/biocLite.R")
biocLite(c('recount', 'devtools', 'getopt', 'downloader', 'SummarizedExperiment'
    'Hmisc'))
```

First run [prep_setup.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_setup.R) which downloads some files that will be needed in the other scripts. Then run [prep_sample.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_sample.R) for each sample in your data set. You can run this step in parallel if you want to. Then run [prep_merge.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_merge.R) to create the final recount objects. A bash script example that runs all 3 scripts is available as [example_prep.sh](https://github.com/leekgroup/recount-website/blob/master/recount-prep/example_prep.sh).

If you have more metadata (phenotype information for your samples) than the one included by default in the recount objects, you can add it to the RangedSummarizedExperiment objects once they are created or modify the preparation R scripts accordingly. For example, adding the tissue information, cell line, age, sex and other demographic variables can be of great use to other researchers.

# Submit files

Once you have created all the recount objects please modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new). In it we will ask you for information on how to contact you, information about your data set, and you will need to provide us with instructions on how to access the recount-like files you created. We will download your files, check them and once we approve them, we'll upload them to [recount](https://jhubiostatistics.shinyapps.io/recount/). The files we'll need access to are:

* The bigwig coverage files for each sample created by [Rail-RNA](rail.bio): `coverage_bigwigs/*.bw`
* The junction files created by [Rail-RNA](rail.bio)
* The normalized mean coverage file: `bw/mean.bw`
* counts_exon.tsv.gz
* counts_gene.tsv.gz
* rse_exon.Rdata
* rse_gene.Rdata
* rse_jx.Rdata
* Log files created by the R scripts for reproducibility purposes

The RangedSummarizedExperiment objects contain the sample metadata that we'll use. You should make sure that all three objects have the same metadata.


# Summary

* Run [Rail-RNA](rail.bio) on your data against the human hg38 genome reference.
* Install dependencies for the recount R scripts.
* Download the deliverables from [Rail-RNA](rail.bio) using the same file structure.
* Run the [prep_setup.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_setup.R) R script.
* Run the R script [prep_sample.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_sample.R) for each sample.
* Run the R script [prep_merge.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_merge.R).
* Optionally add more metadata to your phenotype information.
* Make the files accessible to us.
* Modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new).

# Help

If you need help with the submission process please get in touch by posting your question as a new [issue](https://github.com/leekgroup/recount-contributions/issues). Thank you!


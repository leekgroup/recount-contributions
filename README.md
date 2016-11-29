
# Table of contents

- [Overview](#overview)
- [Getting help](#getting-help)
- [Generating recount-like files](#generating-recount-like-files)
- [Run Rail-RNA](#run-rail-rna)
- [Create recount objects](#create-recount-objects)
- [Submit files](#submit-files)
- [Summary](#summary)


# Overview

If you are interested in contributing your *human RNA-seq data sequenced on the Illumina platform* to [recount](https://jhubiostatistics.shinyapps.io/recount/) you will need to modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new), which has you describe your data and tell us how to access the required files. We'll respond as soon as we can and add your data to [recount](https://jhubiostatistics.shinyapps.io/recount/).

# Getting help

If you're confused by any of the instructions below or are having trouble with your submission, feel free to chat with us in the [recount contributions Gitter](https://gitter.im/recount-contributions/Lobby) or [create an issue](https://github.com/leekgroup/recount-contributions/issues).

# Generating recount-like files

Before we can add your RNA-seq data to [recount](https://jhubiostatistics.shinyapps.io/recount/), you'll need to generate files similar to the ones we already provide for each project on SRA. This means you'll have to run [Rail-RNA](rail.bio) on your dataset against the human reference genome hg38. [Rail-RNA](rail.bio) will generate a set of [deliverables](http://docs.rail.bio/deliverables/). For [recount](https://jhubiostatistics.shinyapps.io/recount/), we'll need the cross-sample tables, coverage vectors in bigWig format, and the exon-exon junction files. After the [Rail-RNA](rail.bio) run is complete, you'll need you to execute [this set of R scripts](https://github.com/leekgroup/recount-website/tree/master/recount-prep) that create the remaining files we need. Once they are generated, you may want to add more phenotype information for your samples. Then you can modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new), and we'll take it from there. Details foll

## Run [Rail-RNA](rail.bio)

The first step involves running [Rail-RNA](rail.bio) with your data. You will find more information about how to do this at the [Rail-RNA documentation website](http://docs.rail.bio/). You can run it on the cloud using [Amazon Web Services](http://aws.amazon.com/) [Elastic MapReduce](http://aws.amazon.com/elasticmapreduce/). Note that it's crucial that the [Rail-RNA deliverables](http://docs.rail.bio/deliverables/) include:

* cross-sample tables: `tsv`
* coverage vectors: `bw`
* junction files: `jx`

and also that reads are aligned to the _hg38_ assembly. If you perform the alignment locally, please run [Rail-RNA](rail.bio) using the Bowtie indexes from the [_hg38_ Illumina iGenome](ftp://igenome:G3nom3s4u@ussd-ftp.illumina.com/Homo_sapiens/UCSC/hg38/Homo_sapiens_UCSC_hg38.tar.gz). If you perform the alignment using [Amazon Web Services](http://aws.amazon.com/) [Elastic MapReduce](http://aws.amazon.com/elasticmapreduce/), make sure to use the command-line parameter `-a hg38`. So in `local` mode, a single command to preprocess and align your RNA-seq data should look like this:

        rail-rna go local -x /path/to/hg38/Bowtie/basename /path/to/hg38/Bowtie2/basename \
        -m /path/to/Rail-RNA/manifest/file -o /path/to/output/dir -d tsv,bw,jx

while in `elastic` (cloud) mode, the command should look like this:

        rail-rna go elastic -a hg38 -m /path/to/Rail-RNA/manifest/file -o s3://bucket-name/output-dir \
        -d tsv,bw,jx -c <number of core instances>

# Create recount objects

Once you have the output from [Rail-RNA](rail.bio) you will need to run the [recount-prep](https://github.com/leekgroup/recount-website/tree/master/recount-prep) R scripts. If you've run Rail-RNA in the cloud, you'll have to download its output to your local system. To run the R scripts, you'll first need to install some dependencies. These are:

* [bwtool v1.0](https://github.com/CRG-Barcelona/bwtool)
* [WiggleTools v1.1](https://github.com/Ensembl/WiggleTools)
* [wigToBigWig](http://hgdownload.cse.ucsc.edu/admin/exe/)

as well as the following R/[Bioconductor](https://www.bioconductor.org/) packages that can be installed with the following R command:

```R
source("https://bioconductor.org/biocLite.R")
biocLite(c('recount', 'devtools', 'getopt', 'downloader', 'SummarizedExperiment'
    'Hmisc'))
```

Now run [prep_setup.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_setup.R) which downloads some files that will be needed in the other scripts. Next, run [prep_sample.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_sample.R) for each sample in your data set. You can perform this step in parallel if you like. Finally, run [prep_merge.R](https://github.com/leekgroup/recount-website/blob/master/recount-prep/prep_merge.R) to create the final recount objects. A bash script example that runs all three scripts is available as [example_prep.sh](https://github.com/leekgroup/recount-website/blob/master/recount-prep/example_prep.sh). If you choose to model your script after this one, make sure to change the variable definitions made in it as follows.

        DATADIR: (local) path to Rail-RNA output directory
        BWTOOL: path to bwtool v1.0 executable
        WIGGLE: path to wiggletools v1.1 executable
        WIGTOBIGWIG: path to UCSC wigToBigWig executable
        MANIFEST: path to Rail-RNA manifest file that was used in the `rail-rna` command invocation

If you have more metadata (phenotype information for your samples) than the one included by default in the recount objects, you can add it to the RangedSummarizedExperiment objects once they are created or modify the preparation R scripts accordingly. For example, adding the tissue information, cell line, age, sex and other demographic variables can be of great use to other researchers.

# Submit files

Once you have created all the recount objects, please modify and submit [this form](https://github.com/leekgroup/recount-contributions/issues/new). In it, we ask you for information on how to contact you, information about your dataset, and  instructions on how to access the recount files you created. We will download and check your files. If they're approved, we'll upload them to [recount](https://jhubiostatistics.shinyapps.io/recount/). The files we'll need access to are:

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

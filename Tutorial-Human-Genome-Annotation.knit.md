
<!-- rnb-text-begin -->

---
title: "[Tutorial] Human Genome Annotation"
output: html_notebook
---

##### Tutorial for Tidyverse (Chapter 4)

## **1. Introduction**

### **1.1. What is gene annotation?**
Over the past years, we have learnt that there are a number of chromosomes and genes in our genome. Counting the number of chromosomes is fairly easy but students might find difficult to say how many genes we have in our genome. If you can get an answer for this, could you tell how many genes encode protein and how many do not?

To answer this question, we need to access the database for gene annotation. Gene annotation is the process of making nucleotide sequence meaningful - where genes are located? whether it is protein-coding or noncoding. If you would like to get an overview of gene annotation, please find this [link](http://www.biolyse.ca/what-is-gene-annotation-in-bioinformatics/).

One of well-known collaborative efforts in gene annotation is the [GENCODE consortium](https://www.gencodegenes.org/pages/gencode.html). It is a part of the Encyclopedia of DNA Elements (The ENCODE project consortium) and aims to identify all gene features in the human genome using a combination of computational analysis, manual annotation, and experimental validation [Harrow et al. 2012](https://genome.cshlp.org/content/22/9/1760.full.html). You might find another database for gene annotation, like RefSeq, CCDS, and need to understand differences between them.
<img src='https://media.springernature.com/full/springer-static/image/art%3A10.1186%2F1471-2164-16-S8-S2/MediaObjects/12864_2015_Article_7216_Fig1_HTML.jpg?as=webp' width='auto'>
**Figure 1. Comparison of GENCODE and RefSeq gene annotation and the impact of reference geneset on variant effect prediction** [Frankish et al. 2015](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3431492/). A) Mean number of alternatively spliced transcripts per multi-exon protein-coding locus B) Mean number of unique CDS per multi-exon protein-coding locus C) Mean number of unique (non-redundant) exons per multi-exon protein-coding locus D) Percentage genomic coverage of unique (non-redundant) exons at multi-exon protein-coding loci.

In this tutorial, we will access to gene annotation from the GENCODE consortium and explore genes and functional elements in our genome.

**1.2. Aims**
What we will do with this dataset:

+ Be familiar with gene annotation modality.
+ Tidy data and create a table for your analysis.
+ Apply `tidyverse` functions for data munging.

Please note that there is better solution for getting gene annotation in R if you use a biomart. Our tutorial is only designed to have a practice on `tidyverse` exercise.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBiaW9tYVI6IGdlbmUsIHRyYW5zY3JpcHQsIHBlcHRpZGXsnZgg7J2066aE7J2EIOuzgO2ZmO2VoCDrlYwg65iQ64qUIOunpOy5reyLnO2CrCDrlYwg7Zmc7Jqp7ZWgIOyImCDsnojripQgUiDtjKjtgqTsp4BcbmBgYCJ9 -->

```r
# biomaR: gene, transcript, peptide의 이름을 변환할 때 또는 매칭시킬 때 활용할 수 있는 R 패키지
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



## **2. Explore your data**

### **2.1. Unboxing your dataset**

This tutorial will use a gene annotation file from the GENCODE. You will need to download the file from the GENCODE. If you are using terminal, please download file using `wget`:


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBSdW4gZnJvbSB5b3VyIHRlcm1pbmFsLCBub3QgUiBjb25zb2xlXG4jIHdnZXQgZnRwOi8vZnRwLmViaS5hYy51ay9wdWIvZGF0YWJhc2VzL2dlbmNvZGUvR2VuY29kZV9odW1hbi9yZWxlYXNlXzMxL2dlbmNvZGUudjMxLmJhc2ljLmFubm90YXRpb24uZ3RmLmd6XG4jd2dldDogd2Vi7JeQIOyeiOuKlCBmaWxl7J2EIOuLpOyatFxuIyBPbmNlIHlvdSBkb3dubG9hZGVkIHRoZSBmaWxlLCB5b3Ugd29uJ3QgbmVlZCB0byBkb3dubG9hZCBpdCBhZ2Fpbi4gU28gcGxlYXNlIGNvbW1lbnQgb3V0IHRoZSBjb21tYW5kIGFib3ZlIGJ5IGFkZGluZyAjXG5gYGAifQ== -->

```r
# Run from your terminal, not R console
# wget ftp://ftp.ebi.ac.uk/pub/databases/gencode/Gencode_human/release_31/gencode.v31.basic.annotation.gtf.gz
#wget: web에 있는 file을 다운
# Once you downloaded the file, you won't need to download it again. So please comment out the command above by adding #
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Once you download the file, you can print out the first few lines using the following bash command (we will learn UNIX commands later):

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBSdW4gZnJvbSB5b3VyIHRlcm1pbmFsLCBub3QgUiBjb25zb2xlXG4jIHpjYXQgZ2VuY29kZS52MzEuYmFzaWMuYW5ub3RhdGlvbi5ndGYuZ3ogfCBoZWFkIC03IChmb3Igd2luZG93cylcbmBgYCJ9 -->

```r
# Run from your terminal, not R console
# zcat gencode.v31.basic.annotation.gtf.gz | head -7 (for windows)
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


The file is the [GFT file format](https://www.ensembl.org/info/website/upload/gff.html), which you will find most commonly in gene annotation. Please read the file format thoroughly in the link above.

For the tutorial, we need to load two packages. If the package is not installed in your system, please install it.

+ `tidyverse`, a package you have learnt from the chapter 5.
+ `readr`, a package provides a fast and friendly way to read. Since the file `gencode.v31.basic.annotation.gtf.gz` is pretty large, you will need some function to load data quickly into your workspace. `readr` in a part of tidyverse, so you can just load `tidyverse` to use `readr` functions. 

Let's load the GTF file into your workspace. We will use `read_delim` function from the `readr` package. This is much faster loading than `read.delim` or `read.csv` from R base. However, please keep in mind that some parameters and output class for `read_delim` are slightly different from them.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxubGlicmFyeSh0aWR5dmVyc2UpXG5gYGAifQ== -->

```r
library(tidyverse)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiUmVnaXN0ZXJlZCBTMyBtZXRob2RzIG92ZXJ3cml0dGVuIGJ5ICdkYnBseXInOlxuICBtZXRob2QgICAgICAgICBmcm9tXG4gIHByaW50LnRibF9sYXp5ICAgICBcbiAgcHJpbnQudGJsX3NxbCAgICAgIFxuLS0gQXR0YWNoaW5nIHBhY2thZ2VzIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSB0aWR5dmVyc2UgMS4zLjEgLS1cbuKImiBnZ3Bsb3QyIDMuMy41ICAgICDiiJogcHVycnIgICAwLjMuNFxu4oiaIHRpYmJsZSAgMy4xLjQgICAgIOKImiBkcGx5ciAgIDEuMC43XG7iiJogdGlkeXIgICAxLjEuMyAgICAg4oiaIHN0cmluZ3IgMS40LjBcbuKImiByZWFkciAgIDIuMC4xICAgICDiiJogZm9yY2F0cyAwLjUuMVxuLS0gQ29uZmxpY3RzIC0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLSB0aWR5dmVyc2VfY29uZmxpY3RzKCkgLS1cbnggZHBseXI6OmZpbHRlcigpIG1hc2tzIHN0YXRzOjpmaWx0ZXIoKVxueCBkcGx5cjo6bGFnKCkgICAgbWFza3Mgc3RhdHM6OmxhZygpXG4ifQ== -->

```
Registered S3 methods overwritten by 'dbplyr':
  method         from
  print.tbl_lazy     
  print.tbl_sql      
-- Attaching packages --------------------------------------------------------------------------------------------------------------------------- tidyverse 1.3.1 --
√ ggplot2 3.3.5     √ purrr   0.3.4
√ tibble  3.1.4     √ dplyr   1.0.7
√ tidyr   1.1.3     √ stringr 1.4.0
√ readr   2.0.1     √ forcats 0.5.1
-- Conflicts ------------------------------------------------------------------------------------------------------------------------------ tidyverse_conflicts() --
x dplyr::filter() masks stats::filter()
x dplyr::lag()    masks stats::lag()
```



<!-- rnb-output-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCA9IHJlYWRfZGVsaW0oJ2dlbmNvZGUudjMxLmJhc2ljLmFubm90YXRpb24uZ3RmLmd6JywgZGVsaW0gPSAnXFx0Jywgc2tpcCA9IDUsIHByb2dyZXNzID0gRiwgY29sX25hbWVzID0gRilcbmBgYCJ9 -->

```r
d = read_delim('gencode.v31.basic.annotation.gtf.gz', delim = '\t', skip = 5, progress = F, col_names = F)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiUm93czogMTc1NjUwMiBDb2x1bW5zOiA5XG4tLSBDb2x1bW4gc3BlY2lmaWNhdGlvbiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLVxuRGVsaW1pdGVyOiBcIlxcdFwiXG5jaHIgKDcpOiBYMSwgWDIsIFgzLCBYNiwgWDcsIFg4LCBYOVxuZGJsICgyKTogWDQsIFg1XG5cbmkgVXNlIGBzcGVjKClgIHRvIHJldHJpZXZlIHRoZSBmdWxsIGNvbHVtbiBzcGVjaWZpY2F0aW9uIGZvciB0aGlzIGRhdGEuXG5pIFNwZWNpZnkgdGhlIGNvbHVtbiB0eXBlcyBvciBzZXQgYHNob3dfY29sX3R5cGVzID0gRkFMU0VgIHRvIHF1aWV0IHRoaXMgbWVzc2FnZS5cbiJ9 -->

```
Rows: 1756502 Columns: 9
-- Column specification --------------------------------------------------------------------------------------------------------------------------------------------
Delimiter: "\t"
chr (7): X1, X2, X3, X6, X7, X8, X9
dbl (2): X4, X5

i Use `spec()` to retrieve the full column specification for this data.
i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Can you find out what the parameters mean? Few things to note are:

+ The GTF file contains the first few lines for comments (`#`). In general, the file contains description, provider, date, format. 
+ The GTF file does not have column names so you will need to assign `FALSE for col_names.

This is sort of canonical way to load your dataset into R. However, we are using a GTF format, which is specific to gene annotation so we can use a package to specifically handle a GTF file.

Here I introduce the package [rtracklayer](https://bioconductor.org/packages/release/bioc/html/rtracklayer.html). Let's install the package first. 


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuaWYgKCFyZXF1aXJlTmFtZXNwYWNlKFwiQmlvY01hbmFnZXJcIiwgcXVpZXRseSA9IFRSVUUpKVxuICAgIGluc3RhbGwucGFja2FnZXMoXCJCaW9jTWFuYWdlclwiKVxuXG5CaW9jTWFuYWdlcjo6aW5zdGFsbChcInJ0cmFja2xheWVyXCIpXG5gYGAifQ== -->

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("rtracklayer")
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Then, now you can read the GTF file using this package. Then, you can check the class of the object `d`.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCA9IHJ0cmFja2xheWVyOjppbXBvcnQoJ2dlbmNvZGUudjMxLmJhc2ljLmFubm90YXRpb24uZ3RmLmd6JylcbmNsYXNzKGQpXG5gYGAifQ== -->

```r
d = rtracklayer::import('gencode.v31.basic.annotation.gtf.gz')
class(d)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIFwiR1Jhbmdlc1wiXG5hdHRyKCxcInBhY2thZ2VcIilcblsxXSBcIkdlbm9taWNSYW5nZXNcIlxuIn0= -->

```
[1] "GRanges"
attr(,"package")
[1] "GenomicRanges"
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->

You will find out that this is `GRanges` class. This is from [the package Genomic Range](https://bioconductor.org/packages/release/bioc/html/GenomicRanges.html), specifically dealing with genomic datasets but we are not heading into this in this tutorial. So please find this information if you are serious on this. 

We are converting `d` into a data frame as following:

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCA9IGQgJT4lIGFzLmRhdGEuZnJhbWUoKVxuYGBgIn0= -->

```r
d = d %>% as.data.frame()
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Let's overview few lines from the  data frame, and explore what you get in this object.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuaGVhZChkKVxuYGBgIn0= -->

```r
head(d)
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImRhdGEuZnJhbWUiXSwibnJvdyI6NiwibmNvbCI6MjYsInN1bW1hcnkiOnsiRGVzY3JpcHRpb24iOlsiZGYgWzYgeCAyNl0iXX19LCJyZGYiOiJINHNJQUFBQUFBQUFCdDFYM1c3VE1CUk9tLzZzclRaQWNBRzc1QkxVMERoTmYzWTMxbXBEMnArMkRwVUxWS1dKMTFaMFRralMvVndnOWl4SVBBRFB3TnNnSWZFRUcwNWlKeWRkdTdYVEJvaEluci92SEIrZlkvdllQZHRydEpWOE95OElnaWlrRWdsQlRGTW9wTmQyNitXNklLU1NsQ1NFbEpEeitsTTY2REVGeTdSZnBIM0dWMDVzTWNQTUVCL2pvVVBSUTlxZU1XbEs3OXN5d0FoZ0JlQXl3Q3JBRllDckFOY0Fyak9jOW55VklKRWhRWkFva0pRaFVTR3BRRktGcEFZSmpBREJDQkNNQU1IRnR3RitCL0RXMkxhbTlhSG04RjBOOS9wUTAxM1RwdWlDTm5aSzB2dW95VTFCVUw1Uy9DUFMxOTRFN2RWM3F2OFdZUFV3MHVkK0JpM1JvL3lJaGtIdE01OW15NExrSll0UTVHbjBrb01pQnkrQ29lTGxkWXVaeTFtU3o3Q3grbloxZTVXeGJITjd2N24xZW5OdWQway8vcWdseDl3dDhHUHFZWUlaenJ1MlJoemRIbGd1MStKVGt6QXNyalgyR1N3NHJtYTdIZDAwUW0zZWNVMHJKaEVQV25zTUx1M2pJU2FtZnVhNGVFRGR6YkFZWWNtVGZQN2wzZTNzbDFsN25nRG45TStreGp4bW1NZEhkSGZYUzk2SGtGS3ZJa245dTRwNGRNLzVjWFN4MFJrUnl6WjE3RGdVV3c0ZUdTWTR1UDlwWkh3UHNvMUdXNVkzNWJ1a2NROEpOQitJVytjMzFyZlhWcFNxWEVMM0tJbjdmTExUYW0wY2JBVzVRNzk2UlpiUVA2WGo4ZEkzK0pKK0lPRmIvcWl5V2xGUUxaemtuaFFscGFST0RTWXpKUHBlK016T3p1Wko0YmpIQXN1L0lnclArQzVFOGhSM0NmbG1rS1RMbW15ZDdtck9RTDhWbVR3aFM1VGdiSlFLcXFxeUpQOHBIWDFpYStYeWhGeVl0bVhoZlZmQTBPdE12Ynhyc3JlOFhLZSs1SEdGb3RaUXRhNWNWU0JGUmhWRmxlUWJYVTNyV1U3c3J1K3MrSXVsSmR5OFU5eldkZGpINnp5aUhXRmU1eTB6NFlLRFB3WnlOc2d2SW5peGdJbkI1U2NEdyszenkrZDQ5ODBJbVRteWRmNXJrbkxQTEJ4T3BwdDJTS3krNW5DUzlhNWtaOENueVBrVVdBWUNMekJ1N2RmKzNMcmZJM3BrWGVocnh4clJPdUEzYlRFcW1xSnhENEFRK0lKaTRQRXBFRHNqeXpKcGJRV0RFRjJ0eDdPR0JYQ2xVaXQ0bFZxSGpJNjYyT2F4KzZJd0p0RWtmSENlUGx0ZUpSWXBNN3B1T0pURnp6Rm5teWNTUDB1dnNFcWVDLzZYbVZMQzVRM04xYVJEMjF1Ylg4YkZwc3VhbGpzd0NaMHM2ZjFUbGg0elR0aGpnZ2NqNGprM2lucC9SRDRVSzU0RElhcWdGMW1mQVhnNWNKbmlnYVg1K2pEcERVaDB4Rm8zM04wbHVraC9qWkpsRDZJdG9sSkhjazFYNCtQeXVqbmtrcUJFdmZnTmluL1J5WXNPQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":[""],"name":["_rn_"],"type":[""],"align":["left"]},{"label":["seqnames"],"name":[1],"type":["fctr"],"align":["left"]},{"label":["start"],"name":[2],"type":["int"],"align":["right"]},{"label":["end"],"name":[3],"type":["int"],"align":["right"]},{"label":["width"],"name":[4],"type":["int"],"align":["right"]},{"label":["strand"],"name":[5],"type":["fctr"],"align":["left"]},{"label":["source"],"name":[6],"type":["fctr"],"align":["left"]},{"label":["type"],"name":[7],"type":["fctr"],"align":["left"]},{"label":["score"],"name":[8],"type":["dbl"],"align":["right"]},{"label":["phase"],"name":[9],"type":["int"],"align":["right"]},{"label":["gene_id"],"name":[10],"type":["chr"],"align":["left"]},{"label":["gene_type"],"name":[11],"type":["chr"],"align":["left"]},{"label":["gene_name"],"name":[12],"type":["chr"],"align":["left"]},{"label":["level"],"name":[13],"type":["chr"],"align":["left"]},{"label":["hgnc_id"],"name":[14],"type":["chr"],"align":["left"]},{"label":["havana_gene"],"name":[15],"type":["chr"],"align":["left"]},{"label":["transcript_id"],"name":[16],"type":["chr"],"align":["left"]},{"label":["transcript_type"],"name":[17],"type":["chr"],"align":["left"]},{"label":["transcript_name"],"name":[18],"type":["chr"],"align":["left"]},{"label":["transcript_support_level"],"name":[19],"type":["chr"],"align":["left"]},{"label":["tag"],"name":[20],"type":["chr"],"align":["left"]},{"label":["havana_transcript"],"name":[21],"type":["chr"],"align":["left"]},{"label":["exon_number"],"name":[22],"type":["chr"],"align":["left"]},{"label":["exon_id"],"name":[23],"type":["chr"],"align":["left"]},{"label":["ont"],"name":[24],"type":["chr"],"align":["left"]},{"label":["protein_id"],"name":[25],"type":["chr"],"align":["left"]},{"label":["ccdsid"],"name":[26],"type":["chr"],"align":["left"]}],"data":[{"1":"chr1","2":"11869","3":"14409","4":"2541","5":"+","6":"HAVANA","7":"gene","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"NA","17":"NA","18":"NA","19":"NA","20":"NA","21":"NA","22":"NA","23":"NA","24":"NA","25":"NA","26":"NA","_rn_":"1"},{"1":"chr1","2":"11869","3":"14409","4":"2541","5":"+","6":"HAVANA","7":"transcript","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"ENST00000456328.2","17":"lncRNA","18":"DDX11L1-202","19":"1","20":"basic","21":"OTTHUMT00000362751.1","22":"NA","23":"NA","24":"NA","25":"NA","26":"NA","_rn_":"2"},{"1":"chr1","2":"11869","3":"12227","4":"359","5":"+","6":"HAVANA","7":"exon","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"ENST00000456328.2","17":"lncRNA","18":"DDX11L1-202","19":"1","20":"basic","21":"OTTHUMT00000362751.1","22":"1","23":"ENSE00002234944.1","24":"NA","25":"NA","26":"NA","_rn_":"3"},{"1":"chr1","2":"12613","3":"12721","4":"109","5":"+","6":"HAVANA","7":"exon","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"ENST00000456328.2","17":"lncRNA","18":"DDX11L1-202","19":"1","20":"basic","21":"OTTHUMT00000362751.1","22":"2","23":"ENSE00003582793.1","24":"NA","25":"NA","26":"NA","_rn_":"4"},{"1":"chr1","2":"13221","3":"14409","4":"1189","5":"+","6":"HAVANA","7":"exon","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"ENST00000456328.2","17":"lncRNA","18":"DDX11L1-202","19":"1","20":"basic","21":"OTTHUMT00000362751.1","22":"3","23":"ENSE00002312635.1","24":"NA","25":"NA","26":"NA","_rn_":"5"},{"1":"chr1","2":"12010","3":"13670","4":"1661","5":"+","6":"HAVANA","7":"transcript","8":"NA","9":"NA","10":"ENSG00000223972.5","11":"transcribed_unprocessed_pseudogene","12":"DDX11L1","13":"2","14":"HGNC:37102","15":"OTTHUMG00000000961.2","16":"ENST00000450305.2","17":"transcribed_unprocessed_pseudogene","18":"DDX11L1-201","19":"__NA__","20":"basic","21":"OTTHUMT00000002844.2","22":"NA","23":"NA","24":"PGO:0000019","25":"NA","26":"NA","_rn_":"6"}],"options":{"columns":{"min":{},"max":[10],"total":[26]},"rows":{"min":[10],"max":[10],"total":[6]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


One thing you can find is that there is no columns in the data frame. Let's match which information is provided in columns. You can find the instruction page in the website ([link](https://www.gencodegenes.org/pages/data_format.html)).

Based on this, you can assign a name for 9 columns. One thing to remember is you should not use space for the column name. Spacing in the column name is actually working but not a good habit for your code. So please replace a space with `underscore` in the column name.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBBc3NpZ24gY29sdW1uIG5hbWVzIGFjY29yZGluZyB0byB0aGUgR0VOQ09ERSBpbnN0cnVjdGlvbi5cbmNvbHMgPSBjKCdjaHJvbScsICdzb3VyY2UnLCAnZmVhdHVyZV90eXBlJywgJ3N0YXJ0JywgJ2VuZCcsICdzY29yZScsICdzdHJhbmQnLCAncGhhc2UnLCAnaW5mbycpXG5gYGAifQ== -->

```r
# Assign column names according to the GENCODE instruction.
cols = c('chrom', 'source', 'feature_type', 'start', 'end', 'score', 'strand', 'phase', 'info')
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Now you can set up the column names into the `col_names` parameter, and load the file into a data frame.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCA9IHJlYWRfZGVsaW0oJ2dlbmNvZGUudjMxLmJhc2ljLmFubm90YXRpb24uZ3RmLmd6JywgXG4gICAgICAgICAgICAgICBkZWxpbT0nXFx0Jywgc2tpcCA9IDUsIFxuICAgICAgICAgICAgICAgcHJvZ3Jlc3MgPSBGLFxuICAgICAgICAgICAgICAgY29sX25hbWVzID0gY29scylcbmBgYCJ9 -->

```r
d = read_delim('gencode.v31.basic.annotation.gtf.gz', 
               delim='\t', skip = 5, 
               progress = F,
               col_names = cols)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiUm93czogMTc1NjUwMiBDb2x1bW5zOiA5XG4tLSBDb2x1bW4gc3BlY2lmaWNhdGlvbiAtLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLVxuRGVsaW1pdGVyOiBcIlxcdFwiXG5jaHIgKDcpOiBjaHJvbSwgc291cmNlLCBmZWF0dXJlX3R5cGUsIHNjb3JlLCBzdHJhbmQsIHBoYXNlLCBpbmZvXG5kYmwgKDIpOiBzdGFydCwgZW5kXG5cbmkgVXNlIGBzcGVjKClgIHRvIHJldHJpZXZlIHRoZSBmdWxsIGNvbHVtbiBzcGVjaWZpY2F0aW9uIGZvciB0aGlzIGRhdGEuXG5pIFNwZWNpZnkgdGhlIGNvbHVtbiB0eXBlcyBvciBzZXQgYHNob3dfY29sX3R5cGVzID0gRkFMU0VgIHRvIHF1aWV0IHRoaXMgbWVzc2FnZS5cbiJ9 -->

```
Rows: 1756502 Columns: 9
-- Column specification --------------------------------------------------------------------------------------------------------------------------------------------
Delimiter: "\t"
chr (7): chrom, source, feature_type, score, strand, phase, info
dbl (2): start, end

i Use `spec()` to retrieve the full column specification for this data.
i Specify the column types or set `show_col_types = FALSE` to quiet this message.
```



<!-- rnb-output-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuVmlldyhkKVxuYGBgIn0= -->

```r
View(d)
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


You can find the column names are now all set.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuaGVhZChkKVxuYGBgIn0= -->

```r
head(d)
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbInRibF9kZiIsInRibCIsImRhdGEuZnJhbWUiXSwibnJvdyI6NiwibmNvbCI6OSwic3VtbWFyeSI6eyJBIHRpYmJsZSI6WyI2IHggOSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJ1MVdUVzdUUUJTZS9CVWxvZ1dwRjdBc2RpaFdiTWRKM0c2SW1pcFpRRnFWRkdVWFRaeEpZdUhNV0dPN2xCM2NnalhpQmh5aWdGaXhZc3MxV0RUTTJKNDBjYU9TUWxxcFVrZXk1czM3bS9lK3ozSGVVYU9yRjdvRkFFQUdaRk1wa01reEVlVDJEczJ5Q1VBMnpRNHBrQVY1dnA4eXAyMG04TU5qOW16RWhxdzFwdXAveVl2NU5scjFWL1YyZlUyblJLMGpoRkVzRjN3S3NXZFIyL1dGRlowUy9CZDVQZ3FBTFo3NzJabnlqc04yYWYveU1kcS8vb2oxQVloWEZQZjlTYVFYKzltdk9PN25vdjdiWnhFMzMwdEt1WjZRaUg1NlBXR2RkMy9pTFBUc2dTVHZ0MTgyUzN4cG1tNVdOY1dRZDZYUTZMOTFrU1FMclB0bzBBdXdTNG1GUEkvSnJvZUNBZUdPd2gvRENmTnZOTHFxK2x4bFNnZWRJRWZTZHFYeENGdmhWYTFtZTI5SHI2b2xqWm5IOEFSaTJPT2hrbnpRNmJTT1gwUjFzR1ZXVklYNVJMV21qcStzOWVKbEVDNmQwS1ZzVkhTdHh0T3NwNTI1ZTZKVURyYU8ydlZGeTBKUVVRc2JuZUV3NStjRnJrdW8zNHRzTXMrL0hDVWZqaVM1RHozYldnMHk0WE54bC9DTVVORXJXdFZRRlhVRzd2dTdEUzcvTFBSd01Pa2pLcW54TWE1MFArNmxiSmJMaW5wUHhPMFJvUzBoUWpkcVd0WFU3NG00VFNMMFpiOElYZFVxdW5FSGlQandqMFNVOUpKeGcwU3NsT29La2xZRVB1UjZPZklFTStnT213YzdFYjdHSlpWcTNnQS9EUDBhKzVMeS8rWEZzVFRITy9UaUFTTXZsR3lzSkJNeENYb2tvSmFZK2g0T0VmUURHbEVqM0QwZlVqRUVaaEFlelBRV29XaVdodGMyTTdsajZBbFQxc1pEa3FnclQ4a2JSZFMyeVo0MG4rV20wK252WkFPV0F6M1JnRkFXQnRDSHlwQ3llSFk2VDRROElLNXZFOHlDMG53Z3p5V0NVelNoZUJSZ1hzbWdhSTBEL0xwWTR4ZUU1bWh0eHZMR25KeVBya3hQNDFRNWdRTENJM3MyUXVjYzJFZE9mTmhpSFljTkt5NjFzWUN6d0xTZTRoTWZDcitDUlJ5aENYc0Q1MzhBRGk4UEFvY01BQUE9In0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["chrom"],"name":[1],"type":["chr"],"align":["left"]},{"label":["source"],"name":[2],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[3],"type":["chr"],"align":["left"]},{"label":["start"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["end"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["score"],"name":[6],"type":["chr"],"align":["left"]},{"label":["strand"],"name":[7],"type":["chr"],"align":["left"]},{"label":["phase"],"name":[8],"type":["chr"],"align":["left"]},{"label":["info"],"name":[9],"type":["chr"],"align":["left"]}],"data":[{"1":"chr1","2":"HAVANA","3":"gene","4":"11869","5":"14409","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; level 2; hgnc_id \"HGNC:37102\"; havana_gene \"OTTHUMG00000000961.2\";"},{"1":"chr1","2":"HAVANA","3":"transcript","4":"11869","5":"14409","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000456328.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"lncRNA\"; transcript_name \"DDX11L1-202\"; level 2; transcript_support_level \"1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"},{"1":"chr1","2":"HAVANA","3":"exon","4":"11869","5":"12227","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000456328.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"lncRNA\"; transcript_name \"DDX11L1-202\"; exon_number 1; exon_id \"ENSE00002234944.1\"; level 2; transcript_support_level \"1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"},{"1":"chr1","2":"HAVANA","3":"exon","4":"12613","5":"12721","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000456328.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"lncRNA\"; transcript_name \"DDX11L1-202\"; exon_number 2; exon_id \"ENSE00003582793.1\"; level 2; transcript_support_level \"1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"},{"1":"chr1","2":"HAVANA","3":"exon","4":"13221","5":"14409","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000456328.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"lncRNA\"; transcript_name \"DDX11L1-202\"; exon_number 3; exon_id \"ENSE00002312635.1\"; level 2; transcript_support_level \"1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"},{"1":"chr1","2":"HAVANA","3":"transcript","4":"12010","5":"13670","6":".","7":"+","8":".","9":"gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000450305.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"transcribed_unprocessed_pseudogene\"; transcript_name \"DDX11L1-201\"; level 2; transcript_support_level \"NA\"; hgnc_id \"HGNC:37102\"; ont \"PGO:0000005\"; ont \"PGO:0000019\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000002844.2\";"}],"options":{"columns":{"min":{},"max":[10],"total":[9]},"rows":{"min":[10],"max":[10],"total":[6]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


When you loaded the file, you see the message about the data class. You might want to overview this data.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuc3VtbWFyeShkKVxuYGBgIn0= -->

```r
summary(d)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiICAgIGNocm9tICAgICAgICAgICAgICBzb3VyY2UgICAgICAgICAgZmVhdHVyZV90eXBlICAgICAgICAgICBzdGFydCAgICAgICAgICBcbiBMZW5ndGg6MTc1NjUwMiAgICAgTGVuZ3RoOjE3NTY1MDIgICAgIExlbmd0aDoxNzU2NTAyICAgICBNaW4uICAgOiAgICAgIDU3NyAgXG4gQ2xhc3MgOmNoYXJhY3RlciAgIENsYXNzIDpjaGFyYWN0ZXIgICBDbGFzcyA6Y2hhcmFjdGVyICAgMXN0IFF1LjogMzIxMDE1MTcgIFxuIE1vZGUgIDpjaGFyYWN0ZXIgICBNb2RlICA6Y2hhcmFjdGVyICAgTW9kZSAgOmNoYXJhY3RlciAgIE1lZGlhbiA6IDYxNzMyNzU0ICBcbiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBNZWFuICAgOiA3NTI4ODU2MyAgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgM3JkIFF1LjoxMTE3NjAxODEgIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIE1heC4gICA6MjQ4OTM2NTgxICBcbiAgICAgIGVuZCAgICAgICAgICAgICAgIHNjb3JlICAgICAgICAgICAgICBzdHJhbmQgICAgICAgICAgICAgcGhhc2UgICAgICAgICAgXG4gTWluLiAgIDogICAgICA2NDcgICBMZW5ndGg6MTc1NjUwMiAgICAgTGVuZ3RoOjE3NTY1MDIgICAgIExlbmd0aDoxNzU2NTAyICAgIFxuIDFzdCBRdS46IDMyMTA3MzMxICAgQ2xhc3MgOmNoYXJhY3RlciAgIENsYXNzIDpjaGFyYWN0ZXIgICBDbGFzcyA6Y2hhcmFjdGVyICBcbiBNZWRpYW4gOiA2MTczODM3MyAgIE1vZGUgIDpjaGFyYWN0ZXIgICBNb2RlICA6Y2hhcmFjdGVyICAgTW9kZSAgOmNoYXJhY3RlciAgXG4gTWVhbiAgIDogNzUyOTI2MzIgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIFxuIDNyZCBRdS46MTExNzYzMDA3ICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBcbiBNYXguICAgOjI0ODkzNzA0MyAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgXG4gICAgIGluZm8gICAgICAgICAgXG4gTGVuZ3RoOjE3NTY1MDIgICAgXG4gQ2xhc3MgOmNoYXJhY3RlciAgXG4gTW9kZSAgOmNoYXJhY3RlciAgXG4gICAgICAgICAgICAgICAgICAgXG4gICAgICAgICAgICAgICAgICAgXG4gICAgICAgICAgICAgICAgICAgXG4ifQ== -->

```
    chrom              source          feature_type           start          
 Length:1756502     Length:1756502     Length:1756502     Min.   :      577  
 Class :character   Class :character   Class :character   1st Qu.: 32101517  
 Mode  :character   Mode  :character   Mode  :character   Median : 61732754  
                                                          Mean   : 75288563  
                                                          3rd Qu.:111760181  
                                                          Max.   :248936581  
      end               score              strand             phase          
 Min.   :      647   Length:1756502     Length:1756502     Length:1756502    
 1st Qu.: 32107331   Class :character   Class :character   Class :character  
 Median : 61738373   Mode  :character   Mode  :character   Mode  :character  
 Mean   : 75292632                                                           
 3rd Qu.:111763007                                                           
 Max.   :248937043                                                           
     info          
 Length:1756502    
 Class :character  
 Mode  :character  
                   
                   
                   
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


###**2.2. How many feature types in the GENCODE dataset?**

As instructed in the GENCODE website, the GENCODE dataset provides a range of annotations for the feature type. You can check feature types using `____` function.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCAlPiUgZ3JvdXBfYnkoZmVhdHVyZV90eXBlKSAlPiUgY291bnQoZmVhdHVyZV90eXBlKVxuYGBgIn0= -->

```r
d %>% group_by(feature_type) %>% count(feature_type)
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjgsIm5jb2wiOjIsInN1bW1hcnkiOnsiQSB0aWJibGUiOlsiOCB4IDIiXSwiR3JvdXBzIjpbImZlYXR1cmVfdHlwZSBbOF0iXX19LCJyZGYiOiJINHNJQUFBQUFBQUFCbDFSUzA3RE1CQ2QvQ0JOS1ZSaXp3MmFEYWhTMSswQlVBdFNONmlZMUMwUnFSM1pqbWgzU055RkJSc093REU0QVYxekFGWU5rOFF1WUV1Mm45OTgzM2c4bXA1SDB3Z0FQUEFkQjd3QUlRVER5OEhGQU1CMzhlR0FENjNxWHFQVEtZS0s3T0lPdGNFYmppWWErblRObWNGTHlxakd4eE9hVWNhVGpWUTAzYk50cVloUXM0VFA5MEdSVkR6L3p5aEJtRXhFbWl0VDcvcHFqRmVuYmlGODdVUDc1aG5nNngzZnR3RGJNNERQTjNCZVBzQmRiUzBOUVpJUktYWC9ob3ptUkpGNEljaUtXdTR0d1I5amhyelU5ZHduUE1xeS9MYnpHcWR1UForR1BGcFFvZ3BCWjJxVEc4a093Mk5uUlIveVhLV2NZYnhielRldytuT0VSWndVcktvMzd5WDNCWHZvOVNzTnRibFpIWTNEUDlodFN2cWxUaFhvVkFlVUxYOC9KTWpJSGMzTW42SDRXbGFjaTVTWjRVZkl5bGh4Ull4ZmxQRE1NTFUyMlAwQUF0VjRjMVlDQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["feature_type"],"name":[1],"type":["chr"],"align":["left"]},{"label":["n"],"name":[2],"type":["int"],"align":["right"]}],"data":[{"1":"CDS","2":"567862"},{"1":"exon","2":"744835"},{"1":"gene","2":"60603"},{"1":"Selenocysteine","2":"96"},{"1":"start_codon","2":"57886"},{"1":"stop_codon","2":"57775"},{"1":"transcript","2":"108243"},{"1":"UTR","2":"159202"}],"options":{"columns":{"min":{},"max":[10],"total":[2]},"rows":{"min":[10],"max":[10],"total":[8]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyB0YWJsZShkJGZlYXR1cmVfdHlwZSlcbiMgOCBmZWF0dXJlIHR5cGVzXG4jIENEUyA9IGNvZGluZyBETkEgc2VxdWVuY2U6IGNvZGVzIGZvciBwcm90ZWluc1xuIyBTZWxlbm9jeXN0ZWluZTog7Iuc7Iqk7YWM7J247J2YIO2ZqSDsm5DsnpDqsIAg7IWA66CI64qEIOybkOyekOuhnCDrsJTrgJAg7LKc7JewIOyVhOuvuOuFuOyCsC4g67Cp7IKs7ISgIOuwqeyWtCDsnpHsmqnsnbTrgpgg7ZWt7JWUIOyekeyaqSDsnojsnYxcbiMgVVRSOiBVbnRyYW5zbGF0ZWQgUmVnaW9uICg14oCZIFVUUuydgCBjb2RpbmcgcmVnaW9u7J2YIHN0YXJ0IGNvZG9uIOuwlOuhnCDslZ7sl5Ag7KG07J6sOyAz4oCZIFVUUuydgCBjb2RpbmcgcmVnaW9u7J2YIHN0b3AgY29kb24g67CU66GcIOuSpOyXkCDsobTsnqxcbmBgYCJ9 -->

```r
# table(d$feature_type)
# 8 feature types
# CDS = coding DNA sequence: codes for proteins
# Selenocysteine: 시스테인의 황 원자가 셀레늄 원자로 바뀐 천연 아미노산. 방사선 방어 작용이나 항암 작용 있음
# UTR: Untranslated Region (5’ UTR은 coding region의 start codon 바로 앞에 존재; 3’ UTR은 coding region의 stop codon 바로 뒤에 존재
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


How many feature types provided in the GENCODE? And how many items stored for each feature type? Please write down the number of feature types from the dataset. Also, if you are not familiar with these types, it would be good to put one or two sentences that can describe each type).

### **2.3. How many genes we have?**

Let's count the number of genes in our genome. Since we know that the column `feature_type` contains rows with `gene`, which contains obviously annotations for genes. We might want to subset those rows from the data frame.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDEgPSBmaWx0ZXIoZCwgZmVhdHVyZV90eXBlID09ICdnZW5lJylcbiMgZDEgPSBkW2QkZmVhdHVyZV90eXBlID09ICdnZW5lJywgXVxuYGBgIn0= -->

```r
d1 = filter(d, feature_type == 'gene')
# d1 = d[d$feature_type == 'gene', ]
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


### **2.4. Ensembl, Havana and CCDS.**

Gene annotation for the human genome is provided by multiple organizations with different gene annotation methods and strategy. This means that information can be varying by resources, and users need to understand heterogeniety inherent in annotation databases.

The GENCODE project utlizes two sources of gene annotation.

1. Havana: Manual gene annotation [detailed strategy in here](https://asia.ensembl.org/info/genome/genebuild/manual_havana.html)

2. Ensembl: Automatic gene annotation [detailed strategy in here](https://asia.ensembl.org/info/genome/genebuild/automatic_coding.html)

It provides the combination of Ensembl/HAVANA gene set as the default gene annotation for the human genome. In addition, they also guarantee that all transcripts from the Consensus Coding Sequence (CCDS) set are present in the GENCODE gene set. The CCDS project is a collaborative effort to identify a core set of protein coding regions that are consistently annotated and of high quality. Initial results from the Consensus CDS (CCDS) project are now available through the appropriate Ensembl gene pages and from the CCDS project page at NCBI. The CCDS set is built by consensus among Ensembl, the National Center for Biotechnology Information (NCBI), and the HUGO Gene Nomenclature Committee (HGNC) for human [link](https://asia.ensembl.org/info/genome/genebuild/ccds.html).

<img src='https://pbs.twimg.com/media/BiIoBo9IQAA2LQL?format=jpg&name=small' width='auto'>
**Figure 2. Comparison of CCDS and Gencode** [Source](https://twitter.com/ensembl/status/441959722376499200)
Right. Then now we count how many genes annotated with HAVANA and ENSEMBL.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZCAlPiUgZ3JvdXBfYnkoc291cmNlKSAlPiUgY291bnQoc291cmNlKVxuYGBgIn0= -->

```r
d %>% group_by(source) %>% count(source)
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjIsIm5jb2wiOjIsInN1bW1hcnkiOnsiQSB0aWJibGUiOlsiMiB4IDIiXSwiR3JvdXBzIjpbInNvdXJjZSBbMl0iXX19LCJyZGYiOiJINHNJQUFBQUFBQUFCbDFRelU0Q01Sajg5cWNRTmtGSlBQZ1ViRUwwd25GVkVnOUtpQ2FFYTEwVzJMQzJwTzFHajc2RWorUEJGMlA5V3ZvbDBqMjA4MDJuMDVsOWVWamRaS3NNQUJKSW93Z1NoaERZL1dKNk93VklZeHdpU0dGZzkwOFVYU0d3NU1qdHA0UCtiUDQ2ZTc1NzhtUHZzVmdXOHdMUjBJbVNuMSs0dnZ3TzNGalpjSzI5RTVIWm1odWVieFIvcndMNVFNbVBYQ0N2eWZjTGw2N3JqcUV2aWY0bjdHblpxcktpSWdLWDhGNWZIa3d0QmQ2TWJVY1dKSXRVUUl4YVlWOWFqOHRkSy9ianljVEdkK2VuYitoeGZJN3RtMm5udlJqRnE4UzJGaFNQTmZ5dGF2eHdnYjFkby95Z2FtSG9QeUdyY3lNTkoxMVd5b1lZVnc2T2Z5YlhMaGJiQVFBQSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["source"],"name":[1],"type":["chr"],"align":["left"]},{"label":["n"],"name":[2],"type":["int"],"align":["right"]}],"data":[{"1":"ENSEMBL","2":"245185"},{"1":"HAVANA","2":"1511317"}],"options":{"columns":{"min":{},"max":[10],"total":[2]},"rows":{"min":[10],"max":[10],"total":[2]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



### **2.5. `do.call`**

Since the last column `info` contains a long string for multiple annotations, we will need to split it to extract each annotation. For example, the first line for transcript annotation looks like this:
chr1    HAVANA    transcript    11869    14409    .    +    .    gene_id "ENSG00000223972.5"; transcript_id "ENST00000456328.2"; gene_type "transcribed_unprocessed_pseudogene"; gene_name "DDX11L1"; transcript_type "lncRNA"; transcript_name "DDX11L1-202"; level 2; transcript_support_level "1"; hgnc_id "HGNC:37102"; tag "basic"; havana_gene "OTTHUMG00000000961.2"; havana_transcript "OTTHUMT00000362751.1";

If you would like to split `transcript_support_level` and create a new column, you can use `strsplit` function.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuYSA9ICdjaHIxICAgIEhBVkFOQSAgICB0cmFuc2NyaXB0ICAgIDExODY5ICAgIDE0NDA5ICAgIC4gICAgKyAgICAuICAgIGdlbmVfaWQgXCJFTlNHMDAwMDAyMjM5NzIuNVwiOyB0cmFuc2NyaXB0X2lkIFwiRU5TVDAwMDAwNDU2MzI4LjJcIjsgZ2VuZV90eXBlIFwidHJhbnNjcmliZWRfdW5wcm9jZXNzZWRfcHNldWRvZ2VuZVwiOyBnZW5lX25hbWUgXCJERFgxMUwxXCI7IHRyYW5zY3JpcHRfdHlwZSBcImxuY1JOQVwiOyB0cmFuc2NyaXB0X25hbWUgXCJERFgxMUwxLTIwMlwiOyBsZXZlbCAyOyB0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgXCIxXCI7IGhnbmNfaWQgXCJIR05DOjM3MTAyXCI7IHRhZyBcImJhc2ljXCI7IGhhdmFuYV9nZW5lIFwiT1RUSFVNRzAwMDAwMDAwOTYxLjJcIjsgaGF2YW5hX3RyYW5zY3JpcHQgXCJPVFRIVU1UMDAwMDAzNjI3NTEuMVwiOydcblxuc3Ryc3BsaXQoYSwgJ3RyYW5zY3JpcHRfc3VwcG9ydF9sZXZlbFxcXFxzK1wiJylcbmBgYCJ9 -->

```r
a = 'chr1    HAVANA    transcript    11869    14409    .    +    .    gene_id "ENSG00000223972.5"; transcript_id "ENST00000456328.2"; gene_type "transcribed_unprocessed_pseudogene"; gene_name "DDX11L1"; transcript_type "lncRNA"; transcript_name "DDX11L1-202"; level 2; transcript_support_level "1"; hgnc_id "HGNC:37102"; tag "basic"; havana_gene "OTTHUMG00000000961.2"; havana_transcript "OTTHUMT00000362751.1";'

strsplit(a, 'transcript_support_level\\s+"')
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiW1sxXV1cblsxXSBcImNocjEgICAgSEFWQU5BICAgIHRyYW5zY3JpcHQgICAgMTE4NjkgICAgMTQ0MDkgICAgLiAgICArICAgIC4gICAgZ2VuZV9pZCBcXFwiRU5TRzAwMDAwMjIzOTcyLjVcXFwiOyB0cmFuc2NyaXB0X2lkIFxcXCJFTlNUMDAwMDA0NTYzMjguMlxcXCI7IGdlbmVfdHlwZSBcXFwidHJhbnNjcmliZWRfdW5wcm9jZXNzZWRfcHNldWRvZ2VuZVxcXCI7IGdlbmVfbmFtZSBcXFwiRERYMTFMMVxcXCI7IHRyYW5zY3JpcHRfdHlwZSBcXFwibG5jUk5BXFxcIjsgdHJhbnNjcmlwdF9uYW1lIFxcXCJERFgxMUwxLTIwMlxcXCI7IGxldmVsIDI7IFwiXG5bMl0gXCIxXFxcIjsgaGduY19pZCBcXFwiSEdOQzozNzEwMlxcXCI7IHRhZyBcXFwiYmFzaWNcXFwiOyBoYXZhbmFfZ2VuZSBcXFwiT1RUSFVNRzAwMDAwMDAwOTYxLjJcXFwiOyBoYXZhbmFfdHJhbnNjcmlwdCBcXFwiT1RUSFVNVDAwMDAwMzYyNzUxLjFcXFwiO1wiICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIFxuIn0= -->

```
[[1]]
[1] "chr1    HAVANA    transcript    11869    14409    .    +    .    gene_id \"ENSG00000223972.5\"; transcript_id \"ENST00000456328.2\"; gene_type \"transcribed_unprocessed_pseudogene\"; gene_name \"DDX11L1\"; transcript_type \"lncRNA\"; transcript_name \"DDX11L1-202\"; level 2; "
[2] "1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"                                                                                                                                                       
```



<!-- rnb-output-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBzdHJzcGxpdDog66y47J6Q7Je07J2EIOuCmOuIhOq4sCDsnITtlZwgZnVuY3Rpb25cbiMgXFxzIGluZGljYXRlcyBhIHdoaXRlIHNwYWNlXG5gYGAifQ== -->

```r
# strsplit: 문자열을 나누기 위한 function
# \s indicates a white space
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


After split the string, you can select the second item in the list (`[[1]][2]`).

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuc3Ryc3BsaXQoYSwgJ3RyYW5zY3JpcHRfc3VwcG9ydF9sZXZlbFxcXFxzK1wiJylbWzFdXVsyXVxuYGBgIn0= -->

```r
strsplit(a, 'transcript_support_level\\s+"')[[1]][2]
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIFwiMVxcXCI7IGhnbmNfaWQgXFxcIkhHTkM6MzcxMDJcXFwiOyB0YWcgXFxcImJhc2ljXFxcIjsgaGF2YW5hX2dlbmUgXFxcIk9UVEhVTUcwMDAwMDAwMDk2MS4yXFxcIjsgaGF2YW5hX3RyYW5zY3JpcHQgXFxcIk9UVEhVTVQwMDAwMDM2Mjc1MS4xXFxcIjtcIlxuIn0= -->

```
[1] "1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->

You can find the `1` in the first position, which you will need to split again.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuYiA9IHN0cnNwbGl0KGEsICd0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWxcXFxccytcIicpW1sxXV1bMl1cbnN0cnNwbGl0KGIsICdcXFxcXCInKVxuYGBgIn0= -->

```r
b = strsplit(a, 'transcript_support_level\\s+"')[[1]][2]
strsplit(b, '\\"')
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiW1sxXV1cbiBbMV0gXCIxXCIgICAgICAgICAgICAgICAgICAgIFwiOyBoZ25jX2lkIFwiICAgICAgICAgICBcIkhHTkM6MzcxMDJcIiAgICAgICAgICAgXCI7IHRhZyBcIiAgICAgICAgICAgICAgIFwiYmFzaWNcIiAgICAgICAgICAgICAgICBcIjsgaGF2YW5hX2dlbmUgXCIgICAgICBcbiBbN10gXCJPVFRIVU1HMDAwMDAwMDA5NjEuMlwiIFwiOyBoYXZhbmFfdHJhbnNjcmlwdCBcIiBcIk9UVEhVTVQwMDAwMDM2Mjc1MS4xXCIgXCI7XCIgICAgICAgICAgICAgICAgICAgXG4ifQ== -->

```
[[1]]
 [1] "1"                    "; hgnc_id "           "HGNC:37102"           "; tag "               "basic"                "; havana_gene "      
 [7] "OTTHUMG00000000961.2" "; havana_transcript " "OTTHUMT00000362751.1" ";"                   
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->

From this, you will get the first item in the list (`[[1]][1]`).

Now you would like to apply `strsplit` function across vectors. For this, `do.call` function can be easily implemented to `strsplit` over the vectors from one column. Let's try this.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuaGVhZChkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIHN0cnNwbGl0KGEsICd0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWxcXFxccytcIicpKVtbMl1dKVxuYGBgIn0= -->

```r
head(do.call(rbind.data.frame, strsplit(a, 'transcript_support_level\\s+"'))[[2]])
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIFwiMVxcXCI7IGhnbmNfaWQgXFxcIkhHTkM6MzcxMDJcXFwiOyB0YWcgXFxcImJhc2ljXFxcIjsgaGF2YW5hX2dlbmUgXFxcIk9UVEhVTUcwMDAwMDAwMDk2MS4yXFxcIjsgaGF2YW5hX3RyYW5zY3JpcHQgXFxcIk9UVEhVTVQwMDAwMDM2Mjc1MS4xXFxcIjtcIlxuIn0= -->

```
[1] "1\"; hgnc_id \"HGNC:37102\"; tag \"basic\"; havana_gene \"OTTHUMG00000000961.2\"; havana_transcript \"OTTHUMT00000362751.1\";"
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Now you can write two lines of codes to process two steps we discussed above.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuIyBGaXJzdCBmaWx0ZXIgdHJhbnNjcmlwdHMgYW5kIGNyZWF0ZSBhIGRhdGEgZnJhbWUuXG5kMiA8LSBkICU+JSBmaWx0ZXIoZmVhdHVyZV90eXBlID09ICd0cmFuc2NyaXB0JylcblxuIyBOb3cgYXBwbHkgdGhlIGZ1bmN0aW9ucy4gXG5kMiR0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoZDIkaW5mbywgJ3RyYW5zY3JpcHRfc3VwcG9ydF9sZXZlbFxcXFxzK1wiJykpW1syXV0pXG5cbmQyJHRyYW5zY3JpcHRfc3VwcG9ydF9sZXZlbCA8LSBhcy5jaGFyYWN0ZXIoZG8uY2FsbChyYmluZC5kYXRhLmZyYW1lLCBcbiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBzdHJzcGxpdCggZDIkdHJhbnNjcmlwdF9zdXBwb3J0X2xldmVsLCAnXFxcXFwiJykpW1sxXV0pXG5gYGAifQ== -->

```r
# First filter transcripts and create a data frame.
d2 <- d %>% filter(feature_type == 'transcript')

# Now apply the functions. 
d2$transcript_support_level <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'transcript_support_level\\s+"'))[[2]])

d2$transcript_support_level <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d2$transcript_support_level, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


Now you can check the strsplit works.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuaGVhZChkMiR0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwpXG5gYGAifQ== -->

```r
head(d2$transcript_support_level)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIFwiMVwiICBcIk5BXCIgXCJOQVwiIFwiTkFcIiBcIjVcIiAgXCI1XCIgXG4ifQ== -->

```
[1] "1"  "NA" "NA" "NA" "5"  "5" 
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->

You can use the same method to extract other annotations, like `gene_id`, `gene_name` etc.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIkZ2VuZV9pZCA8LSBhcy5jaGFyYWN0ZXIoZG8uY2FsbChyYmluZC5kYXRhLmZyYW1lLCBcbiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBzdHJzcGxpdChkMiRpbmZvLCAnZ2VuZV9pZFxcXFxzK1wiJykpW1syXV0pXG5cbmQyJGdlbmVfaWQgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQyJGdlbmVfaWQsICdcXFxcXCInKSlbWzFdXSlcbmBgYCJ9 -->

```r
d2$gene_id <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'gene_id\\s+"'))[[2]])

d2$gene_id <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d2$gene_id, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIkZ2VuZV9uYW1lIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQyJGluZm8sICdnZW5lX25hbWVcXFxccytcIicpKVtbMl1dKVxuXG5kMiRnZW5lX25hbWUgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQyJGdlbmVfbmFtZSwgJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d2$gene_name <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'gene_name\\s+"'))[[2]])

d2$gene_name <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d2$gene_name, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIkZ2VuZV90eXBlIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQyJGluZm8sICdnZW5lX3R5cGVcXFxccytcIicpKVtbMl1dKVxuXG5kMiRnZW5lX3R5cGUgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQyJGdlbmVfdHlwZSwgJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d2$gene_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'gene_type\\s+"'))[[2]])

d2$gene_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d2$gene_type, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIkdHJhbnNjcmlwdF90eXBlIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQyJGluZm8sICd0cmFuc2NyaXB0X3R5cGVcXFxccytcIicpKVtbMl1dKVxuXG5kMiR0cmFuc2NyaXB0X3R5cGUgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQyJHRyYW5zY3JpcHRfdHlwZSwgJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d2$transcript_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'transcript_type\\s+"'))[[2]])

d2$transcript_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d2$transcript_type, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


## **3. Exercises**

Here I list the questions for your activity. Please note that it is an exercise for `tidyverse` functions, which you will need to use in your code. In addition, you will need to write an one-line code for each question using pipe `%>%`.
For questions, you should read some information thoroughly, including:

+ [Gene biotype](https://www.gencodegenes.org/pages/biotypes.html)
+ [0 or 1 based annotation in GTF, BED format](https://www.biostars.org/p/84686/)
+ [Why some features have 1 bp length?](https://www.biostars.org/p/61823/)
+ [What is the meaning of zero-length exons in GENCODE?](https://www.biostars.org/p/209854/)Also fun to have a review for [microexons](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5863539/)
+ [Transcript support level(TSL)](https://asia.ensembl.org/info/genome/genebuild/transcript_quality_tags.html#tsl)

### **3.1. Annotation of transcripts in our genome**

1. Computes the number of transcripts per gene. What is the mean number of transcripts per gene? What is the quantile (25%, 50%, 75%) for these numbers? Which gene has the greatest number of transcript?

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxubTwtZDIgJT4lIGdyb3VwX2J5KGdlbmVfbmFtZSwgZmVhdHVyZV90eXBlKSAlPiUgY291bnQoKVxuYGBgIn0= -->

```r
m<-d2 %>% group_by(gene_name, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->




<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxubWVhbihtJG4pXG5gYGAifQ== -->

```r
mean(m$n)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIDEuODMzMDc0XG4ifQ== -->

```
[1] 1.833074
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxucXVhbnRpbGUobSRuLCBjKDAuMjUsIDAuNSwgMC43NSkpXG5gYGAifQ== -->

```r
quantile(m$n, c(0.25, 0.5, 0.75))
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiMjUlIDUwJSA3NSUgXG4gIDEgICAxICAgMiBcbiJ9 -->

```
25% 50% 75% 
  1   1   2 
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGdyb3VwX2J5KGdlbmVfbmFtZSwgZmVhdHVyZV90eXBlKSAlPiUgXG4gIGNvdW50KCkgJT4lXG4gIHVuZ3JvdXAoKSAlPiUgXG4gIHN1bW1hcml6ZShnZW5lX25hbWVbd2hpY2gubWF4KG4pXSlcbmBgYCJ9 -->

```r
d2 %>% group_by(gene_name, feature_type) %>% 
  count() %>%
  ungroup() %>% 
  summarize(gene_name[which.max(n)])
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbInRibF9kZiIsInRibCIsImRhdGEuZnJhbWUiXSwibnJvdyI6MSwibmNvbCI6MSwic3VtbWFyeSI6eyJBIHRpYmJsZSI6WyIxIHggMSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJsMVFUV3ZETUF5VjAzaWhnWlhBRHZzTDY2RW1aWU9SYzhmT282ZkNHTU5OM1NZMGxVUGkwaHozeTl2SndZWmlnMng5dkNmcGVmMnhlVTAzS1FCTUlHWU1KcHhjNEt1djRxMEFpQ01LR01Rd3RlOUFvS2N4QVpEZEZaTDFaNTdueXhEUFVaNVVIMkNmRHdyVnI2MThYNnE2ck1SSkRpODQvd200MDA1ZmhPYy9ra1YvZE4zb2hFUEtSdmJoa0hRbmpSVDdqdmdVWFFOS29sdFRheVJTWk5Yd2dNeTZJSkdkMFc2eVc1VFZHWStMNWJ1ZDRMNEIzSHJNMloxdlowWTMxNHU3WGc4S0R6VXF2M3dqdDZweHdZd2tqNHBGMjlWb3ZCVEs5c0pvSXowdUxYWGpNNk00dVA0RGNoN3o3Y1VCQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_name[which.max(n)]"],"name":[1],"type":["chr"],"align":["left"]}],"data":[{"1":"RF00019"}],"options":{"columns":{"min":{},"max":[10],"total":[1]},"rows":{"min":[10],"max":[10],"total":[1]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. Compute the number of transcripts per gene among gene biotypes. For example, compare the number of transcript per gene between protein-coding genes, long noncoding genes, pseudogenes.


<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcihnZW5lX3R5cGUgPT0gJ3Byb3RlaW5fY29kaW5nJykgJT4lIGdyb3VwX2J5KGdlbmVfbmFtZSwgZmVhdHVyZV90eXBlKSAgJT4lIGNvdW50KClcbmBgYCJ9 -->

```r
d2 %>% filter(gene_type == 'protein_coding') %>% group_by(gene_name, feature_type)  %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjE5OTQyLCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjE5LDk0MiB4IDMiXSwiR3JvdXBzIjpbImdlbmVfbmFtZSwgZmVhdHVyZV90eXBlIFsxOSw5NDJdIl19fSwicmRmIjoiSDRzSUFBQUFBQUFBQnUxY2JZOGp4M0htM3I3ZVNwWU4rRXYraEJiVDc5MGZoNXpsTExYa0xrMHloOXRQeGtVK08wTGtQZUYwUXBKditiVkJma1JndVpyTFlUOVZROS90UllhQkFDT0EybjZlcnE2cWZxdXVIZzV2MWJ3Mmw2OHZSNlBSOGVqazZHaDBmRXJGMGVsa21Xd2FqVTVlRURnYW5ZeGU1ci8vUVVLLzNVcU9ScitoUC8renF6aXAxYmd0NWNsMFZ6NnU5V0pYUEtYaVhPM0FlVzNhZXI3Uk8zaFcyd3oza3JhOTIreTExZlc2bENmcnZWRGQxSk85dWd6bW1rUERvY1dXeFZiZDF1T2kvMWFWaWtVektSV0xaYW00cThHN2xZWnlBMlh3ZExVdVBTWFFnSkYxYzdNREYxdXdYSUx1TmZSOE00WHk3YjQ4aHE2TUo3WGEyOG1nWWtnelpMQ1pSc0JxTEFLSHdDTUlDQ0tDQkdDTTdvMlplMk9sVUJETmp0SHNHTTJPMGV3WXpZN1I3QVROVHBqWmllSjFPQkFUQm5CVUp1amVCTjJib0hzVDlHaUNIalhZMlFidE5HaW5RVHZYMkdiS0FDcVlvb0lXeFZvVWExRjFpMTFvd2V1YkJvYUhRTVVRcnl1N2I0dkdyTkt3U2x2djRKYzcrSFU5ZVZCY2hMZDNyTkxYSEk0WkRMdzJpTm9KVTh3NnF4RVlCQmFCUStBUmROcG9YODRVbERXVVRiRS9NK05sY1c2MldkV0xJamdIQlhQWXUvUFpRakhFNnpyMXgvWDR2aFJYUmRlcUx2SXJpb3Y3OEVQb2RiMVdBb1ByRzFoTm16RURuZFJsUGFub3YyU3VGR09VcnE1MGp6SElLSzA5bDlGVnNseEdHMVZkV2NhazZMZ3RuWkxuakNIeUFLTjdqQkdNNjdXU21wV1NtcjJYclh5TW5MRlY4SktKWWpTSWtUTEtxUjRqWmJRMlFvOE53aC9yZW5wY2txMThESHg4ckU5S01JRkdnN2VLY3NSc05HSlViZEppeEd4S2dUT3VrcXZGVVYrRmpITDJ5akZHTzJHZG1DZ1pML1VZTGRiWWxtRnJ6RG55aDdmcWphSHoza3NteUY3NFhrK0RYSmt1YUtrbjJGNHJsd1NUeDlrS3h2Umt4Snk2YUdXL2twV3RFbzB6WXp6dEE4bVFQNW96M2w1NXh1aWVIaXRYdUhjcVhnWE9tQ2hsaUdIelJZTWF1ZlZBdTBjd0tvbHhEdG9LZjRLaGtiZWNrWHNuQktPRVRMUWlhb1ZVOVpna1BJeVZqQnVSclBGK1JlVkZqSXBHcnA4dHcyVnNKV1l3MnFpRVptZWtIaWRuSnpyWjkraWlpQURSQnhFQllvNGJxc2ZZSGhNNWs2U3RZTHhrWENWYUJibmZpWWtIR05kalBHZThFZjNLakJHTUhKL004SDZGVUVrWkdRRzJERzhWZy9RNXl2VzhaYmcvS2NoeFRsR3NxRlJKZjVKU1lwOG1SVkhDYzBhT3hwWmhQaWNyVjIveVNkcktEUE9aWEU2Q2lVSDZFNFdNcXBRNFFZZ1JhMTVWT2ZKekdTTjZTb3ptK3lJenJzZjRYcXZRazVIV2pZaUhLaC9Ed3AvTXVCN2plMHprakpjK08zRjZFbU9VWkt5MDdpemZneXFIYk5IS0t5ZGxsTy9KeVBIeEt2WmtrbVNzMUJQRUtheVV6TFVVTFZYSGJhazh6cXhmeWxwaFhWbW5lNHpVWTMxUEp2QVZucGtrWlNpR0M1a2taWnlTL2NvTWIrWGthbEcrc3R4RHJhSmt0REo4L2VoOG5uSVpVd2wvdEkxUnlPVFZ3c1pRZTVISjBINExrakhKQ3NhS3lFWk1GTlpOVUdMdnVKd0pzOUZ3c1JLN2dBNFpIbitVZHlKUFVJSE9QZDZMYUx4WTg5R0pHRVdNRnBxak42SVhNY2d4M0o0eVRJYWlhT0Mya25iOFJOTzBDL2pPSmNid3ZhTnpnR1o5ejB3U3JUSWpXdEUrZFp3Uk1VSFRCdXN4ZE9ZNnpvZ29xcFgzZkgvbFJSY0VZd3pmcDhSRUt4aGJWU3lMSTBiMVpCUWZWVnJPNG02bGRSVFJXT3NrY214YThpSUgwTVltZnNKcXV0M3d0YUVkcmFrRERKc0x1aW53MVVKTVBNUVl3ZkJJUzFxVUY0d1Z1Y1FUWTNwTXY1WG5qRGpmdFE5V3pLQVBZdTlvbjBUYzBFR0xiRkJUYWlPczA5SE5NejFqbGJpNVBERmNodWFRNmFGZHFuaFBUYkRpTG1NckplS3pwVU9HenpKZEs4Vzl3QmxIZVMrdU9rZVJoT2VyeEJpK20xeU1ZdS80RUp4a2t1ZTJmS1JWSmhobitBbENqTGkxRVJNcklVT0xnL2xEbWJISXVpbGlra3pnakhpKzRXT3loeGltZVJ1akRHZkVjd21mak94cHN0WUtQWG1ja1FtYU5pWm5UQ1Z5VWJvMWlYT1FMbHRpN3dTYXdTajB1T2dFNDFYVlk1U3c1WlcwTGtjc1VFQXlCeGltT1NhUit3VWFEY25RaFptMWlsVWxWbVkweEFrR3MvY3ZNa01KTmZYaUFLWDdGQTdhRStWNmpPOHhvY2ZFSHBNWVk4VGpnaGkwT09LZkdNdVluQ2FwSHNQMWVLT0ZqRXhDYUI5b3ZqVlNWVlY4MHlWYWQwRXd4bGVDY1lvSFpHSkVBcDhvYm5IcnhEaCtOTk9aNzJXcnpQUmxBbVBrWTZKRVdTMi80Q1JkaVhTQ0dKSEFFeU1POU13WXdlUXR4bHZKUlU2TVNMeVRObHI2STBOcjB0Wks2MDVjODFNK2pkZzZwTTJqb3RDY21jZ1pMZWFVb29LVFRCUStHeVd1ZThsb09hZEdYdmNTblVaR3RIS1ZsTWxKSTlmanhDWHhpZUV5WG9UTko0YkxSQzNHeDBTUk9CR1QrUEZFQjQ4YzU2RGtMZ2hlK2tQN2l4L2ZtZkU5R2FFNTJzaVA1a1FIbHBoM1lrUlBvM2RpeDhYb2VCS1NrcndhcENTUFptSWNDK3hxKzV3SVcxSEhLV2RGemNRWVBocVo0UkZweHpqR2lFdGlab0lXbXAyUk11THhGOFdJU3VyWk1rd1BwV1NLeTFDR1libUhOTXVlZXhoaUVLM3lTY05sb3JqY0VaTXM5NUFDRzA4bkZFVjVIUVdqZVFRZ3h2RUxLZDI4RlkxaFlveXhRcytXNGEzRVEwVzZNVk5DS3BpY3J1c2V3L3h4bWo5K3B6aExHNHpMQkJPRVA1UllDbHRSSk16RU9Kc2s0d3ozaHk2QW9oZko4SFNDR0pwbTNpcDUvaFdQMHZteFBaUFIrUkVDV3duYUdwNjZiQm51b2ZiaUdxSjBxTVFNNm1TVDBMTmwwSmJKengyWURBVlJ4VDAwMXZ0aS9Zc25Ka2N0eFlXSWlqMG1NU2J4Nzdkb2JWUml3eE1qTnZ3VHcxc3AvbXhVR1cvRmdxYUZ3TE56WmFMbmVWdG14QlJTTk9ZaEtUTko2a21lVDRhaFlXV3BGVEhpK2M2T1liMmc2TXQ5dHZrTExzNW9rM2dJb0tqRmozeVYweWJHVUFxUXYxUHhQU2IwR0pndlRUZFUvajNpbG1GSkNTVUZnUitveEtURURrdE4rOTA3enZnVTJMS2pYa1dlcjJ1YVA1N3ViQmx1eTVwOG0ySXl0QWNEbDdINXVYQW44OFdXeWNuZi9wMkZyUkFkK1VLMUN6eVNhYnBLR2o1a3hFRCtkVnBQNnBvQkRXQlNJeWd2TVV4cWZIc2lJMVRSUkFRSndSekJBc0VhMWEyWnFWZGRxeE5DZDlCb2lWYVg2UGpTQU5pZzJBYkV4dkJhQkFHTHdDSHdDRUx4Qmw3Z29uTG41bkU5YVVyeHVvaGZnKzNybFVMQWFreHBjbk5kS21aMzBPUjJ4WUJHWUJEWW9teitVQ29XNi9KNjErUmVRUmxVM2NPclBaUDcvVGllYlFGSG1pR0R6VkRoeGlMd0NBS0NpQUNXMFAxcjlPZzFxbjZOUmwvRG1sbEM3L2FySkpkaGFKWU95aDdLTU52TDd2VVdtdFZWc2JYYXYvYVN3U3NZbHZXNDVRamNYVThaQU4vWGM0V0ExVmdFRGdFTTVYcUJWaGU2Wm1pTWdxaDhnY29YcUh5Qnl0Zm8zUm83c1FadEd3d3FteHBHZlFON2UxUGVCY3BpRTJ6VE1nRExhelBIMkxPWis1b2hWRDhQckM1QTN6ZnppQUNXMkFhMzJlWU9IYnpESHQ1WjBMMWlQcTFVelJEYXhhMjYyVy9Wclp3Wk16UkJRWWZBSTRqWUNQY29JYzBRZVA5cUJZS3Zpb2RQYUlJSTF3OGhKZ212ZFpWMzNuSVpURDFnbUg3WWI4REx1cWtxdWtEZ0Fkb3huY3h4M2RSN3BRMGNUVTBOa1ltQUJxbjUzc0dtWHNDMFpLUVkwZ3c1aGdKRGthRUVwaGRNaVdibU5ET251YVJoaUJuUUNaRmhPZzNUWWd5NkVoQkVCSjNDaXd5YTYvMUdPODk0QTYvSlBjRktZQzJ3RWRnSzdBVDJBZ2VCbzhDSnV5Zk03d2Q1VjIwNHRCdzZEajJIZ2NQSVllS0c1MktjNXNLeHVSaVh1UmdYaU5nTkppOU52Y1FGdklJWlhvMFZRN2dGTnFoaHcycGdXVXpnTFhBQ0dnSDRVN2J2MlJaVVdJV05IbEQzZzBYQTFIa0VBVUZFQUZNOWVTaUQ4bklIVnlXb05BMldZY0Nha2k4MWJRbFBUYnVDTjhnejBnd1pSR01tT1dhU1l5YlpNTW1HU1Y2enVtdGV4N1JjTzBSVDFtN0syazFadTZsbGlHbHBtWmFXYVdtWmxwWnBhYmtXejFCQU5HY1c1c3pDbkZtWU13dXZZQW5lN00vR0xSZ2pLRDlSYUc0c2xCMlVQWlRCdVp2cE5TeVBHWmJiSWpWYjN2K3VMRGhDSzhVaHJQVFpxdnp3bzdrdHhmSldjN09BVmJnQUorOUtSa2hsT00zdWl5djNaWFdlYjlIK25OM0JNWk9GWGJkc2J3R3Nia296QXZDcmxDM1VLTG9vS3NsNnpkQ1lvUWFSWnBLYVNlcVNwVFFyZUkyNlliR3E3S0VNRmpBNzZ6bVV5eVdReXBBR041dFZHZEhyTVlST0FtVjRyN3ZMNFdVOVZjbnpCeHhUYlZXQUwrRlA2MmtKTitkYk1CZXd6TzYwdVN2bHFZSXl5RXdObEV0Z25MYXFwQ1RUMXN5THc5TnlCWjR1WnVYU09DMjluVzZXTjN1K0xWdW54UU9reGR0dmk3ZGZBaGFCUStBUmxLU21IY1BRdDVpaUV6QUlMSUt5L3R2cmNqMXFwNURJRXdCbDdYNGdxV2RsZzdYbElVRzdnQjhudFl2N1VvWWJhM3V2b1d5Z1hHSlF1NndoTTg1SU0yUVk0dTJnWDh2eUU2WVdmaXJWd28yOVhkMUJ1ZHdZMjY0ajUxUmM0aEp1TnlzR3dMUE5xbDRXYmEvTGo2T29YTVJ1Smh0WWtqZVRCNmg1Z0pDZFVSbittMllDaldiRmhadTcrcmEwSVZEVzY4MnF0RmlWT2I1WjE2QnEzVUs1TE9UWkJITDVHWmJoNXo5VW5nTllZTTFDSXlnalBtdUw4UmtLTFdDUUNaUnV6TUNySlZ4aFpxdnl0T1diR2g0YmZBT09mUFBQNDNxdjZiYXNZQmltMi9LemxWdGJpcTRVZlNtR1VveWxXRzRBdHpVKzk3a3RicDF0UWNVUXI5TU1HWVpLbHB4UnFOR0NSbUFRV0FRT2dVY1FFRVMwRytkWVZXTE83UXhpOGUxc1ZmcThnMlczM2Q2VjQ0aktrSS9kMHIyYm9URW95YWppMEtIc2hMV2NhSVlNUXhiVlRPSVNLN2s3MTZnbTFCeVZKWHdMd1luS2F3VTEwSEZJNjI4MyswVjhXYy96ZTNNSnZ4UWxSam4ySGsxbVVzTG56c1RrRjk3Z2lYWm0rSmMyVDR5UXNmd2xpSGxsbEZHT3RUTEtzeThZTXhPcUtHUkM0UDZZL0swV2I2V2pTbHpHT0oyNEhtdlp0eFFkdy9TNEt2QmVHR2ZaVjgxYnhnczl4Q2htM1NhdkUydEZqTEdTY1Z3UE1leTNEcGtKSVFubTZac2UvbFBreXcvdjN6eisrTzM3NzM3NE1EQURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekFETXpBRE16QURNekQvTDVuUjZGZmJmMWRtTkRxaXovbnU3NHZkMzVQZDMyUEJIMEg5OGU3emN2ZjNCY2pqNTFUb2VMSGpqb1VOMVBrQzlDR1BPazkyTXRuM00vcGNRTHNUc05YcE9Odjl2ZGlWMFI5WmZyR1RrWFpSUDdiclpNNUVYM0ZjVHFHTWY3UGNsN3QrU04wWHUvRjlDZlU0VitqYm9iRi83dWZRM0hWamNHZ3VuL3M1RVg4LzlYbDV3TVp6MnY2U3Z2K3Q4Zmg3MlR1azYyUHRYM3lpN2FGNnVRNy8wZVAxM0hIN2xOekg1dnB2dGZtVXpVL1ZmKzZhL3BoKzFQWGNOZi8zR00vbnpPK25Zc1gvMVJicSs1aU81Nnk1ei9YaDdCTzZ6ejlUM3o5Nm5qNVg3NkVZL1RteDZwZWVFWWYwUG1mdVAyY05mVXhYbHpkMGZ6Rlg2R1R3L016MWVGNUsyWXRST1plbG5SUEJaMTFkbm5FODZ1OTdQTk1QZlhDY3V2eEQrbm9NdHJHL01tODRnZmJkNXdMcWM3dXZSaVhYd2o0ZUg3QXR6eEVjZzQrdE8rbmZrYkNESDV3YjFIVUN0bkJOZFdOME51TDd1TXVCT3JzNFYvbHpLZnFKZlQ4Rk9Zd2RhQi9udHNNeVg1VHI5dEE2eFBwek1XZTRCcnA4Vkk2ZnpDOWxUbnM4NG11aHE4dDk2ZklvSEU5c2R5NTBudTY0MHdPNnU3WVh1N0Y5Y2NCMi91UWM5aXRoRi9NL3VYYmxmc0gxY0FFY3p2M1ppSTlETjU0NGRvZGkvcUU1azc3aHZlRlFMT25HRE5jOHlwNEpIVjIrM3ZrczQ1dmNoNWo3U2l6NzI5azdIdlhYenFFNDE0MHBqaWV1MjI0OHVuV0E4M0kyNHYxRi9iaG11anNXNmo0Q3UzSlBYWXhLN01MN0VZNzdtZUJPUlJzWlgvSCtkVG9xc1JySEMrZFk3Z3ZVaFhMZEhSTnRQL2RjK3lYbkl0azZlV3IvZElNLy9mYjdOei8rT05yK283Qjc4dklQYno2OHVmcmoremQvZml2RVg3NS85KzlYajhUbkpyL0t2dndYL2Uvbm4vLzNuNlRlVHVnMzI4N3RXdi9wN2VQYjMrZWFIZkhsSDkrKytmRFQrN2UvLy9DZlAzVGMwU1A5N3k5QzNmbTdIejU4OSs2UkZMNzQ3VzRtME9Hajk0TDQ5VStQMmN3ZnZ2NzJYMzk2L0xldjdhaEU4ZEhPY3lvdi9ydVU4d3hra3ljLzcxU2Q3bFNkdlgzODAzZVBuWGVuMzcvNWw3ZmY3OEJYTkJyYmZsNzk4UDY3eC8xREVXSi92UHJ3N3NPYlR1N3kyM2ZmZDh5MmI2Ty8vQlVBRmR4Y1I1UUFBQT09In0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"A1BG","2":"transcript","3":"1"},{"1":"A1CF","2":"transcript","3":"7"},{"1":"A2M","2":"transcript","3":"1"},{"1":"A2ML1","2":"transcript","3":"2"},{"1":"A3GALT2","2":"transcript","3":"1"},{"1":"A4GALT","2":"transcript","3":"4"},{"1":"A4GNT","2":"transcript","3":"1"},{"1":"AAAS","2":"transcript","3":"3"},{"1":"AACS","2":"transcript","3":"1"},{"1":"AADAC","2":"transcript","3":"2"},{"1":"AADACL2","2":"transcript","3":"1"},{"1":"AADACL3","2":"transcript","3":"1"},{"1":"AADACL4","2":"transcript","3":"1"},{"1":"AADAT","2":"transcript","3":"4"},{"1":"AAGAB","2":"transcript","3":"3"},{"1":"AAK1","2":"transcript","3":"3"},{"1":"AAMDC","2":"transcript","3":"9"},{"1":"AAMP","2":"transcript","3":"3"},{"1":"AANAT","2":"transcript","3":"2"},{"1":"AAR2","2":"transcript","3":"3"},{"1":"AARD","2":"transcript","3":"1"},{"1":"AARS","2":"transcript","3":"1"},{"1":"AARS2","2":"transcript","3":"1"},{"1":"AARSD1","2":"transcript","3":"1"},{"1":"AASDH","2":"transcript","3":"5"},{"1":"AASDHPPT","2":"transcript","3":"1"},{"1":"AASS","2":"transcript","3":"2"},{"1":"AATF","2":"transcript","3":"1"},{"1":"AATK","2":"transcript","3":"2"},{"1":"ABAT","2":"transcript","3":"5"},{"1":"ABCA1","2":"transcript","3":"3"},{"1":"ABCA10","2":"transcript","3":"1"},{"1":"ABCA12","2":"transcript","3":"3"},{"1":"ABCA13","2":"transcript","3":"1"},{"1":"ABCA2","2":"transcript","3":"4"},{"1":"ABCA3","2":"transcript","3":"3"},{"1":"ABCA4","2":"transcript","3":"3"},{"1":"ABCA5","2":"transcript","3":"2"},{"1":"ABCA6","2":"transcript","3":"2"},{"1":"ABCA7","2":"transcript","3":"3"},{"1":"ABCA8","2":"transcript","3":"4"},{"1":"ABCA9","2":"transcript","3":"3"},{"1":"ABCB1","2":"transcript","3":"3"},{"1":"ABCB10","2":"transcript","3":"1"},{"1":"ABCB11","2":"transcript","3":"1"},{"1":"ABCB4","2":"transcript","3":"5"},{"1":"ABCB5","2":"transcript","3":"4"},{"1":"ABCB6","2":"transcript","3":"2"},{"1":"ABCB7","2":"transcript","3":"7"},{"1":"ABCB8","2":"transcript","3":"6"},{"1":"ABCB9","2":"transcript","3":"8"},{"1":"ABCC1","2":"transcript","3":"2"},{"1":"ABCC10","2":"transcript","3":"2"},{"1":"ABCC11","2":"transcript","3":"4"},{"1":"ABCC12","2":"transcript","3":"1"},{"1":"ABCC2","2":"transcript","3":"2"},{"1":"ABCC3","2":"transcript","3":"3"},{"1":"ABCC4","2":"transcript","3":"4"},{"1":"ABCC5","2":"transcript","3":"6"},{"1":"ABCC6","2":"transcript","3":"4"},{"1":"ABCC8","2":"transcript","3":"8"},{"1":"ABCC9","2":"transcript","3":"6"},{"1":"ABCD1","2":"transcript","3":"2"},{"1":"ABCD2","2":"transcript","3":"1"},{"1":"ABCD3","2":"transcript","3":"2"},{"1":"ABCD4","2":"transcript","3":"2"},{"1":"ABCE1","2":"transcript","3":"1"},{"1":"ABCF1","2":"transcript","3":"2"},{"1":"ABCF2","2":"transcript","3":"2"},{"1":"ABCF3","2":"transcript","3":"2"},{"1":"ABCG1","2":"transcript","3":"6"},{"1":"ABCG2","2":"transcript","3":"3"},{"1":"ABCG4","2":"transcript","3":"3"},{"1":"ABCG5","2":"transcript","3":"1"},{"1":"ABCG8","2":"transcript","3":"1"},{"1":"ABHD1","2":"transcript","3":"2"},{"1":"ABHD10","2":"transcript","3":"2"},{"1":"ABHD11","2":"transcript","3":"4"},{"1":"ABHD12","2":"transcript","3":"2"},{"1":"ABHD12B","2":"transcript","3":"2"},{"1":"ABHD13","2":"transcript","3":"1"},{"1":"ABHD14A","2":"transcript","3":"3"},{"1":"ABHD14A-ACY1","2":"transcript","3":"1"},{"1":"ABHD14B","2":"transcript","3":"6"},{"1":"ABHD15","2":"transcript","3":"1"},{"1":"ABHD16A","2":"transcript","3":"2"},{"1":"ABHD16B","2":"transcript","3":"1"},{"1":"ABHD17A","2":"transcript","3":"3"},{"1":"ABHD17B","2":"transcript","3":"2"},{"1":"ABHD17C","2":"transcript","3":"3"},{"1":"ABHD18","2":"transcript","3":"5"},{"1":"ABHD2","2":"transcript","3":"2"},{"1":"ABHD3","2":"transcript","3":"3"},{"1":"ABHD4","2":"transcript","3":"2"},{"1":"ABHD5","2":"transcript","3":"3"},{"1":"ABHD6","2":"transcript","3":"2"},{"1":"ABHD8","2":"transcript","3":"1"},{"1":"ABI1","2":"transcript","3":"12"},{"1":"ABI2","2":"transcript","3":"7"},{"1":"ABI3","2":"transcript","3":"2"},{"1":"ABI3BP","2":"transcript","3":"4"},{"1":"ABITRAM","2":"transcript","3":"2"},{"1":"ABL1","2":"transcript","3":"2"},{"1":"ABL2","2":"transcript","3":"8"},{"1":"ABLIM1","2":"transcript","3":"9"},{"1":"ABLIM2","2":"transcript","3":"9"},{"1":"ABLIM3","2":"transcript","3":"7"},{"1":"ABO","2":"transcript","3":"2"},{"1":"ABR","2":"transcript","3":"7"},{"1":"ABRA","2":"transcript","3":"1"},{"1":"ABRACL","2":"transcript","3":"2"},{"1":"ABRAXAS1","2":"transcript","3":"3"},{"1":"ABRAXAS2","2":"transcript","3":"1"},{"1":"ABT1","2":"transcript","3":"1"},{"1":"ABTB1","2":"transcript","3":"3"},{"1":"ABTB2","2":"transcript","3":"1"},{"1":"AC000093.1","2":"transcript","3":"1"},{"1":"AC000120.2","2":"transcript","3":"1"},{"1":"AC000120.3","2":"transcript","3":"1"},{"1":"AC001226.2","2":"transcript","3":"1"},{"1":"AC002094.3","2":"transcript","3":"1"},{"1":"AC002310.4","2":"transcript","3":"1"},{"1":"AC002985.1","2":"transcript","3":"1"},{"1":"AC002996.1","2":"transcript","3":"1"},{"1":"AC003002.1","2":"transcript","3":"1"},{"1":"AC003002.2","2":"transcript","3":"1"},{"1":"AC003002.3","2":"transcript","3":"1"},{"1":"AC003005.1","2":"transcript","3":"1"},{"1":"AC003006.1","2":"transcript","3":"1"},{"1":"AC003112.1","2":"transcript","3":"2"},{"1":"AC003665.1","2":"transcript","3":"3"},{"1":"AC003688.1","2":"transcript","3":"1"},{"1":"AC004076.1","2":"transcript","3":"1"},{"1":"AC004080.3","2":"transcript","3":"1"},{"1":"AC004086.1","2":"transcript","3":"2"},{"1":"AC004151.1","2":"transcript","3":"6"},{"1":"AC004156.1","2":"transcript","3":"1"},{"1":"AC004223.3","2":"transcript","3":"1"},{"1":"AC004475.1","2":"transcript","3":"1"},{"1":"AC004551.1","2":"transcript","3":"5"},{"1":"AC004593.3","2":"transcript","3":"1"},{"1":"AC004687.2","2":"transcript","3":"1"},{"1":"AC004691.2","2":"transcript","3":"1"},{"1":"AC004706.3","2":"transcript","3":"1"},{"1":"AC004805.1","2":"transcript","3":"1"},{"1":"AC004832.3","2":"transcript","3":"1"},{"1":"AC004922.1","2":"transcript","3":"1"},{"1":"AC004997.1","2":"transcript","3":"1"},{"1":"AC005020.2","2":"transcript","3":"1"},{"1":"AC005041.1","2":"transcript","3":"1"},{"1":"AC005154.5","2":"transcript","3":"1"},{"1":"AC005255.1","2":"transcript","3":"4"},{"1":"AC005258.1","2":"transcript","3":"1"},{"1":"AC005261.1","2":"transcript","3":"4"},{"1":"AC005324.3","2":"transcript","3":"1"},{"1":"AC005324.4","2":"transcript","3":"1"},{"1":"AC005520.1","2":"transcript","3":"1"},{"1":"AC005551.1","2":"transcript","3":"1"},{"1":"AC005666.1","2":"transcript","3":"1"},{"1":"AC005670.2","2":"transcript","3":"1"},{"1":"AC005697.1","2":"transcript","3":"1"},{"1":"AC005702.1","2":"transcript","3":"1"},{"1":"AC005726.1","2":"transcript","3":"1"},{"1":"AC005747.1","2":"transcript","3":"9"},{"1":"AC005759.1","2":"transcript","3":"5"},{"1":"AC005832.4","2":"transcript","3":"1"},{"1":"AC005833.1","2":"transcript","3":"1"},{"1":"AC005837.2","2":"transcript","3":"1"},{"1":"AC005840.1","2":"transcript","3":"4"},{"1":"AC005943.1","2":"transcript","3":"1"},{"1":"AC005954.1","2":"transcript","3":"1"},{"1":"AC006030.1","2":"transcript","3":"1"},{"1":"AC006059.2","2":"transcript","3":"1"},{"1":"AC006064.6","2":"transcript","3":"1"},{"1":"AC006254.1","2":"transcript","3":"1"},{"1":"AC006486.1","2":"transcript","3":"1"},{"1":"AC006518.7","2":"transcript","3":"1"},{"1":"AC006538.1","2":"transcript","3":"3"},{"1":"AC006538.3","2":"transcript","3":"1"},{"1":"AC006978.2","2":"transcript","3":"1"},{"1":"AC007040.2","2":"transcript","3":"1"},{"1":"AC007192.1","2":"transcript","3":"1"},{"1":"AC007244.1","2":"transcript","3":"1"},{"1":"AC007326.4","2":"transcript","3":"1"},{"1":"AC007375.1","2":"transcript","3":"1"},{"1":"AC007731.4","2":"transcript","3":"1"},{"1":"AC007846.2","2":"transcript","3":"1"},{"1":"AC007906.2","2":"transcript","3":"1"},{"1":"AC007998.2","2":"transcript","3":"1"},{"1":"AC008012.1","2":"transcript","3":"1"},{"1":"AC008073.3","2":"transcript","3":"1"},{"1":"AC008162.2","2":"transcript","3":"1"},{"1":"AC008397.1","2":"transcript","3":"1"},{"1":"AC008397.2","2":"transcript","3":"1"},{"1":"AC008403.1","2":"transcript","3":"1"},{"1":"AC008481.3","2":"transcript","3":"1"},{"1":"AC008537.1","2":"transcript","3":"1"},{"1":"AC008554.1","2":"transcript","3":"2"},{"1":"AC008575.1","2":"transcript","3":"1"},{"1":"AC008581.2","2":"transcript","3":"1"},{"1":"AC008676.3","2":"transcript","3":"1"},{"1":"AC008687.1","2":"transcript","3":"1"},{"1":"AC008687.4","2":"transcript","3":"1"},{"1":"AC008687.8","2":"transcript","3":"1"},{"1":"AC008695.1","2":"transcript","3":"1"},{"1":"AC008736.1","2":"transcript","3":"3"},{"1":"AC008750.8","2":"transcript","3":"1"},{"1":"AC008755.1","2":"transcript","3":"1"},{"1":"AC008758.1","2":"transcript","3":"1"},{"1":"AC008758.5","2":"transcript","3":"1"},{"1":"AC008758.6","2":"transcript","3":"1"},{"1":"AC008763.2","2":"transcript","3":"1"},{"1":"AC008763.3","2":"transcript","3":"1"},{"1":"AC008764.1","2":"transcript","3":"1"},{"1":"AC008764.4","2":"transcript","3":"1"},{"1":"AC008770.1","2":"transcript","3":"1"},{"1":"AC008770.2","2":"transcript","3":"2"},{"1":"AC008770.4","2":"transcript","3":"1"},{"1":"AC008878.1","2":"transcript","3":"1"},{"1":"AC008878.2","2":"transcript","3":"1"},{"1":"AC008878.3","2":"transcript","3":"1"},{"1":"AC008977.1","2":"transcript","3":"3"},{"1":"AC008982.1","2":"transcript","3":"1"},{"1":"AC009070.1","2":"transcript","3":"1"},{"1":"AC009119.2","2":"transcript","3":"1"},{"1":"AC009133.6","2":"transcript","3":"1"},{"1":"AC009163.2","2":"transcript","3":"1"},{"1":"AC009163.4","2":"transcript","3":"1"},{"1":"AC009412.1","2":"transcript","3":"2"},{"1":"AC009690.1","2":"transcript","3":"1"},{"1":"AC009690.3","2":"transcript","3":"1"},{"1":"AC009779.3","2":"transcript","3":"2"},{"1":"AC009879.2","2":"transcript","3":"1"},{"1":"AC009879.3","2":"transcript","3":"1"},{"1":"AC010132.3","2":"transcript","3":"1"},{"1":"AC010197.2","2":"transcript","3":"1"},{"1":"AC010255.3","2":"transcript","3":"2"},{"1":"AC010319.2","2":"transcript","3":"1"},{"1":"AC010323.1","2":"transcript","3":"1"},{"1":"AC010325.1","2":"transcript","3":"2"},{"1":"AC010326.2","2":"transcript","3":"1"},{"1":"AC010327.1","2":"transcript","3":"2"},{"1":"AC010327.2","2":"transcript","3":"1"},{"1":"AC010330.1","2":"transcript","3":"3"},{"1":"AC010422.3","2":"transcript","3":"1"},{"1":"AC010422.5","2":"transcript","3":"1"},{"1":"AC010422.6","2":"transcript","3":"1"},{"1":"AC010422.8","2":"transcript","3":"1"},{"1":"AC010463.1","2":"transcript","3":"1"},{"1":"AC010522.1","2":"transcript","3":"1"},{"1":"AC010531.1","2":"transcript","3":"1"},{"1":"AC010542.3","2":"transcript","3":"1"},{"1":"AC010547.4","2":"transcript","3":"1"},{"1":"AC010605.1","2":"transcript","3":"3"},{"1":"AC010615.4","2":"transcript","3":"1"},{"1":"AC010616.1","2":"transcript","3":"1"},{"1":"AC010616.2","2":"transcript","3":"1"},{"1":"AC010618.1","2":"transcript","3":"1"},{"1":"AC010619.1","2":"transcript","3":"1"},{"1":"AC010646.1","2":"transcript","3":"1"},{"1":"AC010761.1","2":"transcript","3":"1"},{"1":"AC011005.1","2":"transcript","3":"1"},{"1":"AC011195.2","2":"transcript","3":"1"},{"1":"AC011330.3","2":"transcript","3":"1"},{"1":"AC011448.1","2":"transcript","3":"1"},{"1":"AC011452.1","2":"transcript","3":"1"},{"1":"AC011455.2","2":"transcript","3":"1"},{"1":"AC011462.1","2":"transcript","3":"1"},{"1":"AC011473.4","2":"transcript","3":"1"},{"1":"AC011479.1","2":"transcript","3":"1"},{"1":"AC011498.4","2":"transcript","3":"1"},{"1":"AC011499.1","2":"transcript","3":"1"},{"1":"AC011511.1","2":"transcript","3":"1"},{"1":"AC011511.4","2":"transcript","3":"1"},{"1":"AC011530.1","2":"transcript","3":"1"},{"1":"AC011604.2","2":"transcript","3":"2"},{"1":"AC012184.2","2":"transcript","3":"1"},{"1":"AC012213.5","2":"transcript","3":"1"},{"1":"AC012254.2","2":"transcript","3":"1"},{"1":"AC012309.1","2":"transcript","3":"1"},{"1":"AC012488.2","2":"transcript","3":"1"},{"1":"AC012531.3","2":"transcript","3":"1"},{"1":"AC012651.1","2":"transcript","3":"1"},{"1":"AC013271.1","2":"transcript","3":"1"},{"1":"AC013394.1","2":"transcript","3":"1"},{"1":"AC013470.2","2":"transcript","3":"2"},{"1":"AC013489.1","2":"transcript","3":"1"},{"1":"AC013717.1","2":"transcript","3":"1"},{"1":"AC015688.4","2":"transcript","3":"1"},{"1":"AC015802.6","2":"transcript","3":"1"},{"1":"AC015813.2","2":"transcript","3":"1"},{"1":"AC016586.1","2":"transcript","3":"4"},{"1":"AC017083.3","2":"transcript","3":"1"},{"1":"AC018362.3","2":"transcript","3":"1"},{"1":"AC018512.1","2":"transcript","3":"1"},{"1":"AC018523.2","2":"transcript","3":"1"},{"1":"AC018630.2","2":"transcript","3":"1"},{"1":"AC018709.1","2":"transcript","3":"1"},{"1":"AC018755.2","2":"transcript","3":"2"},{"1":"AC019117.3","2":"transcript","3":"1"},{"1":"AC019257.8","2":"transcript","3":"1"},{"1":"AC020613.1","2":"transcript","3":"1"},{"1":"AC020636.2","2":"transcript","3":"1"},{"1":"AC020907.6","2":"transcript","3":"1"},{"1":"AC020909.1","2":"transcript","3":"2"},{"1":"AC020909.2","2":"transcript","3":"1"},{"1":"AC020915.5","2":"transcript","3":"1"},{"1":"AC020922.1","2":"transcript","3":"1"},{"1":"AC021072.1","2":"transcript","3":"1"},{"1":"AC021087.5","2":"transcript","3":"1"},{"1":"AC021097.2","2":"transcript","3":"1"},{"1":"AC021660.3","2":"transcript","3":"1"},{"1":"AC022137.3","2":"transcript","3":"2"},{"1":"AC022335.1","2":"transcript","3":"1"},{"1":"AC022384.1","2":"transcript","3":"1"},{"1":"AC022400.7","2":"transcript","3":"1"},{"1":"AC022414.1","2":"transcript","3":"1"},{"1":"AC022415.2","2":"transcript","3":"1"},{"1":"AC022506.1","2":"transcript","3":"1"},{"1":"AC022826.2","2":"transcript","3":"1"},{"1":"AC022966.1","2":"transcript","3":"5"},{"1":"AC023055.1","2":"transcript","3":"1"},{"1":"AC023490.4","2":"transcript","3":"1"},{"1":"AC024592.3","2":"transcript","3":"1"},{"1":"AC025165.3","2":"transcript","3":"1"},{"1":"AC025165.6","2":"transcript","3":"1"},{"1":"AC025263.2","2":"transcript","3":"1"},{"1":"AC025283.2","2":"transcript","3":"1"},{"1":"AC025283.3","2":"transcript","3":"1"},{"1":"AC025287.4","2":"transcript","3":"1"},{"1":"AC026316.4","2":"transcript","3":"1"},{"1":"AC026464.1","2":"transcript","3":"1"},{"1":"AC026464.3","2":"transcript","3":"1"},{"1":"AC026464.4","2":"transcript","3":"1"},{"1":"AC026464.6","2":"transcript","3":"1"},{"1":"AC026470.1","2":"transcript","3":"1"},{"1":"AC026740.3","2":"transcript","3":"2"},{"1":"AC026786.1","2":"transcript","3":"1"},{"1":"AC026954.2","2":"transcript","3":"1"},{"1":"AC027237.1","2":"transcript","3":"5"},{"1":"AC027644.4","2":"transcript","3":"1"},{"1":"AC027796.3","2":"transcript","3":"1"},{"1":"AC034102.1","2":"transcript","3":"4"},{"1":"AC034102.3","2":"transcript","3":"1"},{"1":"AC034228.4","2":"transcript","3":"1"},{"1":"AC036214.3","2":"transcript","3":"1"},{"1":"AC037459.1","2":"transcript","3":"1"},{"1":"AC040162.1","2":"transcript","3":"1"},{"1":"AC046185.1","2":"transcript","3":"1"},{"1":"AC048338.1","2":"transcript","3":"1"},{"1":"AC053503.7","2":"transcript","3":"1"},{"1":"AC055811.2","2":"transcript","3":"1"},{"1":"AC055839.2","2":"transcript","3":"1"},{"1":"AC058822.1","2":"transcript","3":"1"},{"1":"AC067752.1","2":"transcript","3":"1"},{"1":"AC067968.1","2":"transcript","3":"1"},{"1":"AC068234.1","2":"transcript","3":"1"},{"1":"AC068533.4","2":"transcript","3":"1"},{"1":"AC068547.1","2":"transcript","3":"1"},{"1":"AC068580.4","2":"transcript","3":"2"},{"1":"AC068631.2","2":"transcript","3":"1"},{"1":"AC068775.1","2":"transcript","3":"1"},{"1":"AC068831.7","2":"transcript","3":"1"},{"1":"AC068896.1","2":"transcript","3":"1"},{"1":"AC068946.1","2":"transcript","3":"1"},{"1":"AC068946.2","2":"transcript","3":"1"},{"1":"AC069257.3","2":"transcript","3":"1"},{"1":"AC069288.1","2":"transcript","3":"1"},{"1":"AC069368.1","2":"transcript","3":"1"},{"1":"AC069444.2","2":"transcript","3":"1"},{"1":"AC069503.2","2":"transcript","3":"1"},{"1":"AC072022.2","2":"transcript","3":"1"},{"1":"AC073082.1","2":"transcript","3":"1"},{"1":"AC073111.4","2":"transcript","3":"3"},{"1":"AC073283.3","2":"transcript","3":"1"},{"1":"AC073508.2","2":"transcript","3":"1"},{"1":"AC073585.2","2":"transcript","3":"1"},{"1":"AC073610.2","2":"transcript","3":"1"},{"1":"AC073611.1","2":"transcript","3":"3"},{"1":"AC073612.1","2":"transcript","3":"1"},{"1":"AC073896.1","2":"transcript","3":"1"},{"1":"AC074143.1","2":"transcript","3":"3"},{"1":"AC074143.2","2":"transcript","3":"1"},{"1":"AC078927.1","2":"transcript","3":"1"},{"1":"AC079447.1","2":"transcript","3":"1"},{"1":"AC079594.2","2":"transcript","3":"1"},{"1":"AC080038.1","2":"transcript","3":"2"},{"1":"AC083800.1","2":"transcript","3":"1"},{"1":"AC083977.1","2":"transcript","3":"1"},{"1":"AC084121.11","2":"transcript","3":"1"},{"1":"AC084121.12","2":"transcript","3":"1"},{"1":"AC084121.13","2":"transcript","3":"1"},{"1":"AC084121.5","2":"transcript","3":"1"},{"1":"AC084121.6","2":"transcript","3":"1"},{"1":"AC084121.7","2":"transcript","3":"1"},{"1":"AC084121.8","2":"transcript","3":"1"},{"1":"AC084121.9","2":"transcript","3":"1"},{"1":"AC084337.2","2":"transcript","3":"1"},{"1":"AC087289.1","2":"transcript","3":"1"},{"1":"AC087289.4","2":"transcript","3":"1"},{"1":"AC087498.1","2":"transcript","3":"1"},{"1":"AC087498.2","2":"transcript","3":"3"},{"1":"AC087632.1","2":"transcript","3":"1"},{"1":"AC087651.1","2":"transcript","3":"2"},{"1":"AC087721.2","2":"transcript","3":"1"},{"1":"AC090004.1","2":"transcript","3":"1"},{"1":"AC090227.1","2":"transcript","3":"1"},{"1":"AC090360.1","2":"transcript","3":"1"},{"1":"AC090517.4","2":"transcript","3":"1"},{"1":"AC090527.2","2":"transcript","3":"1"},{"1":"AC091021.1","2":"transcript","3":"3"},{"1":"AC091057.6","2":"transcript","3":"1"},{"1":"AC091167.2","2":"transcript","3":"1"},{"1":"AC091167.6","2":"transcript","3":"1"},{"1":"AC091167.7","2":"transcript","3":"1"},{"1":"AC091551.1","2":"transcript","3":"1"},{"1":"AC091959.3","2":"transcript","3":"1"},{"1":"AC092017.3","2":"transcript","3":"1"},{"1":"AC092042.3","2":"transcript","3":"1"},{"1":"AC092072.1","2":"transcript","3":"2"},{"1":"AC092073.1","2":"transcript","3":"1"},{"1":"AC092111.3","2":"transcript","3":"1"},{"1":"AC092143.1","2":"transcript","3":"1"},{"1":"AC092161.1","2":"transcript","3":"1"},{"1":"AC092329.3","2":"transcript","3":"1"},{"1":"AC092338.1","2":"transcript","3":"1"},{"1":"AC092442.1","2":"transcript","3":"1"},{"1":"AC092587.1","2":"transcript","3":"1"},{"1":"AC092647.5","2":"transcript","3":"1"},{"1":"AC092718.3","2":"transcript","3":"1"},{"1":"AC092718.8","2":"transcript","3":"1"},{"1":"AC092724.1","2":"transcript","3":"6"},{"1":"AC092835.1","2":"transcript","3":"1"},{"1":"AC092881.1","2":"transcript","3":"3"},{"1":"AC093155.3","2":"transcript","3":"1"},{"1":"AC093227.2","2":"transcript","3":"1"},{"1":"AC093323.1","2":"transcript","3":"1"},{"1":"AC093423.3","2":"transcript","3":"1"},{"1":"AC093503.1","2":"transcript","3":"1"},{"1":"AC093512.2","2":"transcript","3":"7"},{"1":"AC093525.1","2":"transcript","3":"1"},{"1":"AC093525.2","2":"transcript","3":"1"},{"1":"AC093668.1","2":"transcript","3":"1"},{"1":"AC093668.2","2":"transcript","3":"1"},{"1":"AC093827.5","2":"transcript","3":"1"},{"1":"AC093884.1","2":"transcript","3":"1"},{"1":"AC093899.2","2":"transcript","3":"1"},{"1":"AC096887.1","2":"transcript","3":"1"},{"1":"AC097104.1","2":"transcript","3":"1"},{"1":"AC097625.2","2":"transcript","3":"1"},{"1":"AC097634.4","2":"transcript","3":"1"},{"1":"AC097636.2","2":"transcript","3":"1"},{"1":"AC097637.1","2":"transcript","3":"1"},{"1":"AC098484.3","2":"transcript","3":"1"},{"1":"AC098582.1","2":"transcript","3":"1"},{"1":"AC098588.1","2":"transcript","3":"1"},{"1":"AC098650.1","2":"transcript","3":"1"},{"1":"AC098850.3","2":"transcript","3":"1"},{"1":"AC099489.1","2":"transcript","3":"1"},{"1":"AC099811.2","2":"transcript","3":"1"},{"1":"AC099850.2","2":"transcript","3":"1"},{"1":"AC100868.1","2":"transcript","3":"1"},{"1":"AC104109.3","2":"transcript","3":"1"},{"1":"AC104304.1","2":"transcript","3":"1"},{"1":"AC104389.4","2":"transcript","3":"1"},{"1":"AC104389.5","2":"transcript","3":"2"},{"1":"AC104452.1","2":"transcript","3":"1"},{"1":"AC104472.3","2":"transcript","3":"1"},{"1":"AC104532.1","2":"transcript","3":"1"},{"1":"AC104581.2","2":"transcript","3":"1"},{"1":"AC105052.1","2":"transcript","3":"1"},{"1":"AC105052.3","2":"transcript","3":"1"},{"1":"AC106741.1","2":"transcript","3":"1"},{"1":"AC106774.4","2":"transcript","3":"1"},{"1":"AC106886.5","2":"transcript","3":"1"},{"1":"AC107871.1","2":"transcript","3":"1"},{"1":"AC107959.5","2":"transcript","3":"1"},{"1":"AC108488.2","2":"transcript","3":"1"},{"1":"AC108941.2","2":"transcript","3":"1"},{"1":"AC110275.1","2":"transcript","3":"1"},{"1":"AC112128.1","2":"transcript","3":"1"},{"1":"AC112229.3","2":"transcript","3":"1"},{"1":"AC112504.2","2":"transcript","3":"1"},{"1":"AC113189.9","2":"transcript","3":"1"},{"1":"AC113348.1","2":"transcript","3":"1"},{"1":"AC113348.2","2":"transcript","3":"1"},{"1":"AC113554.1","2":"transcript","3":"1"},{"1":"AC114267.1","2":"transcript","3":"2"},{"1":"AC114490.2","2":"transcript","3":"1"},{"1":"AC114490.3","2":"transcript","3":"1"},{"1":"AC115220.1","2":"transcript","3":"1"},{"1":"AC116366.3","2":"transcript","3":"3"},{"1":"AC117378.1","2":"transcript","3":"1"},{"1":"AC117457.1","2":"transcript","3":"1"},{"1":"AC118470.1","2":"transcript","3":"1"},{"1":"AC118549.1","2":"transcript","3":"2"},{"1":"AC118553.2","2":"transcript","3":"2"},{"1":"AC118754.1","2":"transcript","3":"1"},{"1":"AC119396.1","2":"transcript","3":"1"},{"1":"AC119674.2","2":"transcript","3":"1"},{"1":"AC119676.1","2":"transcript","3":"1"},{"1":"AC120057.2","2":"transcript","3":"1"},{"1":"AC120114.4","2":"transcript","3":"1"},{"1":"AC124312.1","2":"transcript","3":"1"},{"1":"AC124319.1","2":"transcript","3":"3"},{"1":"AC126283.2","2":"transcript","3":"1"},{"1":"AC127029.3","2":"transcript","3":"1"},{"1":"AC129492.1","2":"transcript","3":"3"},{"1":"AC129492.4","2":"transcript","3":"1"},{"1":"AC131160.1","2":"transcript","3":"1"},{"1":"AC132217.2","2":"transcript","3":"1"},{"1":"AC134669.1","2":"transcript","3":"1"},{"1":"AC134684.11","2":"transcript","3":"1"},{"1":"AC134684.8","2":"transcript","3":"1"},{"1":"AC134684.9","2":"transcript","3":"1"},{"1":"AC134980.3","2":"transcript","3":"1"},{"1":"AC135050.2","2":"transcript","3":"1"},{"1":"AC135068.1","2":"transcript","3":"1"},{"1":"AC135068.3","2":"transcript","3":"1"},{"1":"AC135178.2","2":"transcript","3":"1"},{"1":"AC136428.1","2":"transcript","3":"1"},{"1":"AC137834.1","2":"transcript","3":"1"},{"1":"AC138647.1","2":"transcript","3":"2"},{"1":"AC138696.1","2":"transcript","3":"1"},{"1":"AC138811.2","2":"transcript","3":"1"},{"1":"AC138894.1","2":"transcript","3":"1"},{"1":"AC138969.1","2":"transcript","3":"3"},{"1":"AC139491.7","2":"transcript","3":"1"},{"1":"AC139530.1","2":"transcript","3":"3"},{"1":"AC139530.3","2":"transcript","3":"1"},{"1":"AC139768.1","2":"transcript","3":"2"},{"1":"AC140504.1","2":"transcript","3":"1"},{"1":"AC142391.1","2":"transcript","3":"1"},{"1":"AC144573.1","2":"transcript","3":"1"},{"1":"AC187653.1","2":"transcript","3":"1"},{"1":"AC211486.6","2":"transcript","3":"1"},{"1":"AC211486.7","2":"transcript","3":"1"},{"1":"AC211486.8","2":"transcript","3":"1"},{"1":"AC231656.1","2":"transcript","3":"1"},{"1":"AC231657.3","2":"transcript","3":"1"},{"1":"AC233723.1","2":"transcript","3":"1"},{"1":"AC233992.2","2":"transcript","3":"1"},{"1":"AC235565.2","2":"transcript","3":"1"},{"1":"AC236972.4","2":"transcript","3":"1"},{"1":"AC239811.1","2":"transcript","3":"1"},{"1":"AC242842.3","2":"transcript","3":"1"},{"1":"AC242843.1","2":"transcript","3":"3"},{"1":"AC243547.3","2":"transcript","3":"1"},{"1":"AC243967.1","2":"transcript","3":"1"},{"1":"AC244197.3","2":"transcript","3":"2"},{"1":"AC244517.10","2":"transcript","3":"1"},{"1":"AC245033.1","2":"transcript","3":"1"},{"1":"AC245748.1","2":"transcript","3":"1"},{"1":"AC253536.7","2":"transcript","3":"1"},{"1":"AC253572.1","2":"transcript","3":"1"},{"1":"ACAA1","2":"transcript","3":"5"},{"1":"ACAA2","2":"transcript","3":"3"},{"1":"ACACA","2":"transcript","3":"5"},{"1":"ACACB","2":"transcript","3":"3"},{"1":"ACAD10","2":"transcript","3":"3"},{"1":"ACAD11","2":"transcript","3":"2"},{"1":"ACAD8","2":"transcript","3":"2"},{"1":"ACAD9","2":"transcript","3":"1"},{"1":"ACADL","2":"transcript","3":"1"},{"1":"ACADM","2":"transcript","3":"4"},{"1":"ACADS","2":"transcript","3":"2"},{"1":"ACADSB","2":"transcript","3":"2"},{"1":"ACADVL","2":"transcript","3":"3"},{"1":"ACAN","2":"transcript","3":"7"},{"1":"ACAP1","2":"transcript","3":"1"},{"1":"ACAP2","2":"transcript","3":"2"},{"1":"ACAP3","2":"transcript","3":"2"},{"1":"ACAT1","2":"transcript","3":"2"},{"1":"ACAT2","2":"transcript","3":"1"},{"1":"ACBD3","2":"transcript","3":"1"},{"1":"ACBD4","2":"transcript","3":"8"},{"1":"ACBD5","2":"transcript","3":"5"},{"1":"ACBD6","2":"transcript","3":"2"},{"1":"ACBD7","2":"transcript","3":"1"},{"1":"ACCS","2":"transcript","3":"1"},{"1":"ACCSL","2":"transcript","3":"1"},{"1":"ACD","2":"transcript","3":"5"},{"1":"ACE","2":"transcript","3":"4"},{"1":"ACE2","2":"transcript","3":"2"},{"1":"ACER1","2":"transcript","3":"1"},{"1":"ACER2","2":"transcript","3":"1"},{"1":"ACER3","2":"transcript","3":"3"},{"1":"ACHE","2":"transcript","3":"6"},{"1":"ACIN1","2":"transcript","3":"8"},{"1":"ACKR1","2":"transcript","3":"3"},{"1":"ACKR2","2":"transcript","3":"2"},{"1":"ACKR3","2":"transcript","3":"1"},{"1":"ACKR4","2":"transcript","3":"1"},{"1":"ACLY","2":"transcript","3":"5"},{"1":"ACMSD","2":"transcript","3":"2"},{"1":"ACO1","2":"transcript","3":"3"},{"1":"ACO2","2":"transcript","3":"2"},{"1":"ACOD1","2":"transcript","3":"2"},{"1":"ACOT1","2":"transcript","3":"2"},{"1":"ACOT11","2":"transcript","3":"2"},{"1":"ACOT12","2":"transcript","3":"2"},{"1":"ACOT13","2":"transcript","3":"2"},{"1":"ACOT2","2":"transcript","3":"3"},{"1":"ACOT4","2":"transcript","3":"1"},{"1":"ACOT6","2":"transcript","3":"2"},{"1":"ACOT7","2":"transcript","3":"6"},{"1":"ACOT8","2":"transcript","3":"2"},{"1":"ACOT9","2":"transcript","3":"4"},{"1":"ACOX1","2":"transcript","3":"2"},{"1":"ACOX2","2":"transcript","3":"2"},{"1":"ACOX3","2":"transcript","3":"3"},{"1":"ACOXL","2":"transcript","3":"3"},{"1":"ACP1","2":"transcript","3":"5"},{"1":"ACP2","2":"transcript","3":"4"},{"1":"ACP4","2":"transcript","3":"1"},{"1":"ACP5","2":"transcript","3":"5"},{"1":"ACP6","2":"transcript","3":"3"},{"1":"ACP7","2":"transcript","3":"2"},{"1":"ACPP","2":"transcript","3":"3"},{"1":"ACR","2":"transcript","3":"2"},{"1":"ACRBP","2":"transcript","3":"3"},{"1":"ACRV1","2":"transcript","3":"4"},{"1":"ACSBG1","2":"transcript","3":"2"},{"1":"ACSBG2","2":"transcript","3":"4"},{"1":"ACSF2","2":"transcript","3":"4"},{"1":"ACSF3","2":"transcript","3":"4"},{"1":"ACSL1","2":"transcript","3":"8"},{"1":"ACSL3","2":"transcript","3":"2"},{"1":"ACSL4","2":"transcript","3":"3"},{"1":"ACSL5","2":"transcript","3":"5"},{"1":"ACSL6","2":"transcript","3":"14"},{"1":"ACSM1","2":"transcript","3":"2"},{"1":"ACSM2A","2":"transcript","3":"5"},{"1":"ACSM2B","2":"transcript","3":"5"},{"1":"ACSM3","2":"transcript","3":"2"},{"1":"ACSM4","2":"transcript","3":"1"},{"1":"ACSM5","2":"transcript","3":"3"},{"1":"ACSM6","2":"transcript","3":"2"},{"1":"ACSS1","2":"transcript","3":"4"},{"1":"ACSS2","2":"transcript","3":"2"},{"1":"ACSS3","2":"transcript","3":"2"},{"1":"ACTA1","2":"transcript","3":"2"},{"1":"ACTA2","2":"transcript","3":"1"},{"1":"ACTB","2":"transcript","3":"2"},{"1":"ACTBL2","2":"transcript","3":"1"},{"1":"ACTC1","2":"transcript","3":"1"},{"1":"ACTG1","2":"transcript","3":"5"},{"1":"ACTG2","2":"transcript","3":"4"},{"1":"ACTL10","2":"transcript","3":"1"},{"1":"ACTL6A","2":"transcript","3":"3"},{"1":"ACTL6B","2":"transcript","3":"1"},{"1":"ACTL7A","2":"transcript","3":"1"},{"1":"ACTL7B","2":"transcript","3":"1"},{"1":"ACTL8","2":"transcript","3":"2"},{"1":"ACTL9","2":"transcript","3":"2"},{"1":"ACTN1","2":"transcript","3":"5"},{"1":"ACTN2","2":"transcript","3":"3"},{"1":"ACTN3","2":"transcript","3":"2"},{"1":"ACTN4","2":"transcript","3":"3"},{"1":"ACTR10","2":"transcript","3":"1"},{"1":"ACTR1A","2":"transcript","3":"2"},{"1":"ACTR1B","2":"transcript","3":"1"},{"1":"ACTR2","2":"transcript","3":"3"},{"1":"ACTR3","2":"transcript","3":"3"},{"1":"ACTR3B","2":"transcript","3":"3"},{"1":"ACTR3C","2":"transcript","3":"3"},{"1":"ACTR5","2":"transcript","3":"1"},{"1":"ACTR6","2":"transcript","3":"4"},{"1":"ACTR8","2":"transcript","3":"2"},{"1":"ACTRT1","2":"transcript","3":"1"},{"1":"ACTRT2","2":"transcript","3":"1"},{"1":"ACTRT3","2":"transcript","3":"1"},{"1":"ACVR1","2":"transcript","3":"4"},{"1":"ACVR1B","2":"transcript","3":"5"},{"1":"ACVR1C","2":"transcript","3":"4"},{"1":"ACVR2A","2":"transcript","3":"3"},{"1":"ACVR2B","2":"transcript","3":"1"},{"1":"ACVRL1","2":"transcript","3":"3"},{"1":"ACY1","2":"transcript","3":"6"},{"1":"ACY3","2":"transcript","3":"2"},{"1":"ACYP1","2":"transcript","3":"6"},{"1":"ACYP2","2":"transcript","3":"7"},{"1":"AD000671.1","2":"transcript","3":"1"},{"1":"AD000671.2","2":"transcript","3":"1"},{"1":"ADA","2":"transcript","3":"2"},{"1":"ADA2","2":"transcript","3":"7"},{"1":"ADAD1","2":"transcript","3":"3"},{"1":"ADAD2","2":"transcript","3":"2"},{"1":"ADAL","2":"transcript","3":"5"},{"1":"ADAM10","2":"transcript","3":"4"},{"1":"ADAM11","2":"transcript","3":"2"},{"1":"ADAM12","2":"transcript","3":"2"},{"1":"ADAM15","2":"transcript","3":"10"},{"1":"ADAM17","2":"transcript","3":"1"},{"1":"ADAM18","2":"transcript","3":"3"},{"1":"ADAM19","2":"transcript","3":"2"},{"1":"ADAM2","2":"transcript","3":"5"},{"1":"ADAM20","2":"transcript","3":"2"},{"1":"ADAM21","2":"transcript","3":"1"},{"1":"ADAM22","2":"transcript","3":"5"},{"1":"ADAM23","2":"transcript","3":"2"},{"1":"ADAM28","2":"transcript","3":"2"},{"1":"ADAM29","2":"transcript","3":"6"},{"1":"ADAM30","2":"transcript","3":"1"},{"1":"ADAM32","2":"transcript","3":"3"},{"1":"ADAM33","2":"transcript","3":"5"},{"1":"ADAM7","2":"transcript","3":"4"},{"1":"ADAM8","2":"transcript","3":"3"},{"1":"ADAM9","2":"transcript","3":"3"},{"1":"ADAMDEC1","2":"transcript","3":"2"},{"1":"ADAMTS1","2":"transcript","3":"1"},{"1":"ADAMTS10","2":"transcript","3":"3"},{"1":"ADAMTS12","2":"transcript","3":"3"},{"1":"ADAMTS13","2":"transcript","3":"6"},{"1":"ADAMTS14","2":"transcript","3":"2"},{"1":"ADAMTS15","2":"transcript","3":"1"},{"1":"ADAMTS16","2":"transcript","3":"2"},{"1":"ADAMTS17","2":"transcript","3":"1"},{"1":"ADAMTS18","2":"transcript","3":"1"},{"1":"ADAMTS19","2":"transcript","3":"1"},{"1":"ADAMTS2","2":"transcript","3":"3"},{"1":"ADAMTS20","2":"transcript","3":"3"},{"1":"ADAMTS3","2":"transcript","3":"2"},{"1":"ADAMTS4","2":"transcript","3":"2"},{"1":"ADAMTS5","2":"transcript","3":"1"},{"1":"ADAMTS6","2":"transcript","3":"1"},{"1":"ADAMTS7","2":"transcript","3":"1"},{"1":"ADAMTS8","2":"transcript","3":"1"},{"1":"ADAMTS9","2":"transcript","3":"3"},{"1":"ADAMTSL1","2":"transcript","3":"7"},{"1":"ADAMTSL2","2":"transcript","3":"4"},{"1":"ADAMTSL3","2":"transcript","3":"2"},{"1":"ADAMTSL4","2":"transcript","3":"4"},{"1":"ADAMTSL5","2":"transcript","3":"2"},{"1":"ADAP1","2":"transcript","3":"6"},{"1":"ADAP2","2":"transcript","3":"2"},{"1":"ADAR","2":"transcript","3":"8"},{"1":"ADARB1","2":"transcript","3":"6"},{"1":"ADARB2","2":"transcript","3":"3"},{"1":"ADAT1","2":"transcript","3":"1"},{"1":"ADAT2","2":"transcript","3":"2"},{"1":"ADAT3","2":"transcript","3":"1"},{"1":"ADCK1","2":"transcript","3":"2"},{"1":"ADCK2","2":"transcript","3":"2"},{"1":"ADCK5","2":"transcript","3":"1"},{"1":"ADCY1","2":"transcript","3":"3"},{"1":"ADCY10","2":"transcript","3":"4"},{"1":"ADCY2","2":"transcript","3":"1"},{"1":"ADCY3","2":"transcript","3":"2"},{"1":"ADCY4","2":"transcript","3":"3"},{"1":"ADCY5","2":"transcript","3":"3"},{"1":"ADCY6","2":"transcript","3":"3"},{"1":"ADCY7","2":"transcript","3":"4"},{"1":"ADCY8","2":"transcript","3":"2"},{"1":"ADCY9","2":"transcript","3":"1"},{"1":"ADCYAP1","2":"transcript","3":"2"},{"1":"ADCYAP1R1","2":"transcript","3":"5"},{"1":"ADD1","2":"transcript","3":"9"},{"1":"ADD2","2":"transcript","3":"5"},{"1":"ADD3","2":"transcript","3":"3"},{"1":"ADGB","2":"transcript","3":"1"},{"1":"ADGRA1","2":"transcript","3":"3"},{"1":"ADGRA2","2":"transcript","3":"2"},{"1":"ADGRA3","2":"transcript","3":"3"},{"1":"ADGRB1","2":"transcript","3":"3"},{"1":"ADGRB2","2":"transcript","3":"7"},{"1":"ADGRB3","2":"transcript","3":"3"},{"1":"ADGRD1","2":"transcript","3":"4"},{"1":"ADGRD2","2":"transcript","3":"1"},{"1":"ADGRE1","2":"transcript","3":"5"},{"1":"ADGRE2","2":"transcript","3":"7"},{"1":"ADGRE3","2":"transcript","3":"5"},{"1":"ADGRE5","2":"transcript","3":"3"},{"1":"ADGRF1","2":"transcript","3":"3"},{"1":"ADGRF2","2":"transcript","3":"3"},{"1":"ADGRF3","2":"transcript","3":"4"},{"1":"ADGRF4","2":"transcript","3":"3"},{"1":"ADGRF5","2":"transcript","3":"2"},{"1":"ADGRG1","2":"transcript","3":"8"},{"1":"ADGRG2","2":"transcript","3":"10"},{"1":"ADGRG3","2":"transcript","3":"2"},{"1":"ADGRG4","2":"transcript","3":"3"},{"1":"ADGRG5","2":"transcript","3":"3"},{"1":"ADGRG6","2":"transcript","3":"4"},{"1":"ADGRG7","2":"transcript","3":"2"},{"1":"ADGRL1","2":"transcript","3":"2"},{"1":"ADGRL2","2":"transcript","3":"12"},{"1":"ADGRL3","2":"transcript","3":"14"},{"1":"ADGRL4","2":"transcript","3":"5"},{"1":"ADGRV1","2":"transcript","3":"3"},{"1":"ADH1A","2":"transcript","3":"1"},{"1":"ADH1B","2":"transcript","3":"4"},{"1":"ADH1C","2":"transcript","3":"1"},{"1":"ADH4","2":"transcript","3":"4"},{"1":"ADH5","2":"transcript","3":"2"},{"1":"ADH6","2":"transcript","3":"3"},{"1":"ADH7","2":"transcript","3":"4"},{"1":"ADHFE1","2":"transcript","3":"2"},{"1":"ADI1","2":"transcript","3":"2"},{"1":"ADIG","2":"transcript","3":"3"},{"1":"ADIPOQ","2":"transcript","3":"2"},{"1":"ADIPOR1","2":"transcript","3":"2"},{"1":"ADIPOR2","2":"transcript","3":"1"},{"1":"ADIRF","2":"transcript","3":"1"},{"1":"ADK","2":"transcript","3":"4"},{"1":"ADM","2":"transcript","3":"8"},{"1":"ADM2","2":"transcript","3":"2"},{"1":"ADM5","2":"transcript","3":"1"},{"1":"ADNP","2":"transcript","3":"7"},{"1":"ADNP2","2":"transcript","3":"1"},{"1":"ADO","2":"transcript","3":"1"},{"1":"ADORA1","2":"transcript","3":"6"},{"1":"ADORA2A","2":"transcript","3":"4"},{"1":"ADORA2B","2":"transcript","3":"1"},{"1":"ADORA3","2":"transcript","3":"2"},{"1":"ADPGK","2":"transcript","3":"2"},{"1":"ADPRH","2":"transcript","3":"4"},{"1":"ADPRHL1","2":"transcript","3":"3"},{"1":"ADPRHL2","2":"transcript","3":"1"},{"1":"ADPRM","2":"transcript","3":"2"},{"1":"ADRA1A","2":"transcript","3":"7"},{"1":"ADRA1B","2":"transcript","3":"1"},{"1":"ADRA1D","2":"transcript","3":"1"},{"1":"ADRA2A","2":"transcript","3":"1"},{"1":"ADRA2B","2":"transcript","3":"1"},{"1":"ADRA2C","2":"transcript","3":"2"},{"1":"ADRB1","2":"transcript","3":"1"},{"1":"ADRB2","2":"transcript","3":"1"},{"1":"ADRB3","2":"transcript","3":"1"},{"1":"ADRM1","2":"transcript","3":"3"},{"1":"ADSL","2":"transcript","3":"4"},{"1":"ADSS","2":"transcript","3":"1"},{"1":"ADSSL1","2":"transcript","3":"4"},{"1":"ADTRP","2":"transcript","3":"2"},{"1":"AEBP1","2":"transcript","3":"2"},{"1":"AEBP2","2":"transcript","3":"4"},{"1":"AEN","2":"transcript","3":"1"},{"1":"AF196969.1","2":"transcript","3":"1"},{"1":"AF241726.2","2":"transcript","3":"1"},{"1":"AFAP1","2":"transcript","3":"4"},{"1":"AFAP1L1","2":"transcript","3":"2"},{"1":"AFAP1L2","2":"transcript","3":"2"},{"1":"AFDN","2":"transcript","3":"7"},{"1":"AFF1","2":"transcript","3":"3"},{"1":"AFF2","2":"transcript","3":"5"},{"1":"AFF3","2":"transcript","3":"3"},{"1":"AFF4","2":"transcript","3":"2"},{"1":"AFG1L","2":"transcript","3":"1"},{"1":"AFG3L2","2":"transcript","3":"1"},{"1":"AFM","2":"transcript","3":"1"},{"1":"AFMID","2":"transcript","3":"6"},{"1":"AFP","2":"transcript","3":"2"},{"1":"AFTPH","2":"transcript","3":"4"},{"1":"AGA","2":"transcript","3":"1"},{"1":"AGAP1","2":"transcript","3":"7"},{"1":"AGAP2","2":"transcript","3":"2"},{"1":"AGAP3","2":"transcript","3":"6"},{"1":"AGAP4","2":"transcript","3":"4"},{"1":"AGAP5","2":"transcript","3":"3"},{"1":"AGAP6","2":"transcript","3":"1"},{"1":"AGAP9","2":"transcript","3":"1"},{"1":"AGBL1","2":"transcript","3":"2"},{"1":"AGBL2","2":"transcript","3":"3"},{"1":"AGBL3","2":"transcript","3":"2"},{"1":"AGBL4","2":"transcript","3":"4"},{"1":"AGBL5","2":"transcript","3":"2"},{"1":"AGER","2":"transcript","3":"9"},{"1":"AGFG1","2":"transcript","3":"5"},{"1":"AGFG2","2":"transcript","3":"1"},{"1":"AGGF1","2":"transcript","3":"2"},{"1":"AGK","2":"transcript","3":"9"},{"1":"AGL","2":"transcript","3":"5"},{"1":"AGMAT","2":"transcript","3":"1"},{"1":"AGMO","2":"transcript","3":"1"},{"1":"AGO1","2":"transcript","3":"2"},{"1":"AGO2","2":"transcript","3":"2"},{"1":"AGO3","2":"transcript","3":"4"},{"1":"AGO4","2":"transcript","3":"1"},{"1":"AGPAT1","2":"transcript","3":"6"},{"1":"AGPAT2","2":"transcript","3":"3"},{"1":"AGPAT3","2":"transcript","3":"6"},{"1":"AGPAT4","2":"transcript","3":"3"},{"1":"AGPAT5","2":"transcript","3":"1"},{"1":"AGPS","2":"transcript","3":"2"},{"1":"AGR2","2":"transcript","3":"2"},{"1":"AGR3","2":"transcript","3":"2"},{"1":"AGRN","2":"transcript","3":"2"},{"1":"AGRP","2":"transcript","3":"1"},{"1":"AGT","2":"transcript","3":"1"},{"1":"AGTPBP1","2":"transcript","3":"4"},{"1":"AGTR1","2":"transcript","3":"8"},{"1":"AGTR2","2":"transcript","3":"1"},{"1":"AGTRAP","2":"transcript","3":"7"},{"1":"AGXT","2":"transcript","3":"1"},{"1":"AGXT2","2":"transcript","3":"3"},{"1":"AHCTF1","2":"transcript","3":"3"},{"1":"AHCY","2":"transcript","3":"2"},{"1":"AHCYL1","2":"transcript","3":"3"},{"1":"AHCYL2","2":"transcript","3":"4"},{"1":"AHDC1","2":"transcript","3":"5"},{"1":"AHI1","2":"transcript","3":"7"},{"1":"AHNAK","2":"transcript","3":"3"},{"1":"AHNAK2","2":"transcript","3":"2"},{"1":"AHR","2":"transcript","3":"2"},{"1":"AHRR","2":"transcript","3":"6"},{"1":"AHSA1","2":"transcript","3":"3"},{"1":"AHSG","2":"transcript","3":"2"},{"1":"AHSP","2":"transcript","3":"1"},{"1":"AICDA","2":"transcript","3":"2"},{"1":"AIDA","2":"transcript","3":"2"},{"1":"AIF1","2":"transcript","3":"2"},{"1":"AIF1L","2":"transcript","3":"7"},{"1":"AIFM1","2":"transcript","3":"5"},{"1":"AIFM2","2":"transcript","3":"3"},{"1":"AIFM3","2":"transcript","3":"4"},{"1":"AIG1","2":"transcript","3":"6"},{"1":"AIM2","2":"transcript","3":"1"},{"1":"AIMP1","2":"transcript","3":"3"},{"1":"AIMP2","2":"transcript","3":"3"},{"1":"AIP","2":"transcript","3":"1"},{"1":"AIPL1","2":"transcript","3":"8"},{"1":"AIRE","2":"transcript","3":"1"},{"1":"AJAP1","2":"transcript","3":"2"},{"1":"AJM1","2":"transcript","3":"1"},{"1":"AJUBA","2":"transcript","3":"3"},{"1":"AK1","2":"transcript","3":"3"},{"1":"AK2","2":"transcript","3":"8"},{"1":"AK3","2":"transcript","3":"4"},{"1":"AK4","2":"transcript","3":"4"},{"1":"AK5","2":"transcript","3":"3"},{"1":"AK6","2":"transcript","3":"5"},{"1":"AK7","2":"transcript","3":"2"},{"1":"AK8","2":"transcript","3":"1"},{"1":"AK9","2":"transcript","3":"4"},{"1":"AKAIN1","2":"transcript","3":"2"},{"1":"AKAP1","2":"transcript","3":"6"},{"1":"AKAP10","2":"transcript","3":"2"},{"1":"AKAP11","2":"transcript","3":"1"},{"1":"AKAP12","2":"transcript","3":"4"},{"1":"AKAP13","2":"transcript","3":"5"},{"1":"AKAP14","2":"transcript","3":"4"},{"1":"AKAP17A","2":"transcript","3":"4"},{"1":"AKAP2","2":"transcript","3":"3"},{"1":"AKAP3","2":"transcript","3":"2"},{"1":"AKAP4","2":"transcript","3":"2"},{"1":"AKAP5","2":"transcript","3":"2"},{"1":"AKAP6","2":"transcript","3":"3"},{"1":"AKAP7","2":"transcript","3":"6"},{"1":"AKAP8","2":"transcript","3":"1"},{"1":"AKAP8L","2":"transcript","3":"2"},{"1":"AKAP9","2":"transcript","3":"5"},{"1":"AKIP1","2":"transcript","3":"8"},{"1":"AKIRIN1","2":"transcript","3":"3"},{"1":"AKIRIN2","2":"transcript","3":"1"},{"1":"AKNA","2":"transcript","3":"6"},{"1":"AKNAD1","2":"transcript","3":"3"},{"1":"AKR1A1","2":"transcript","3":"4"},{"1":"AKR1B1","2":"transcript","3":"1"},{"1":"AKR1B10","2":"transcript","3":"1"},{"1":"AKR1B15","2":"transcript","3":"3"},{"1":"AKR1C1","2":"transcript","3":"2"},{"1":"AKR1C2","2":"transcript","3":"3"},{"1":"AKR1C3","2":"transcript","3":"3"},{"1":"AKR1C4","2":"transcript","3":"2"},{"1":"AKR1C8P","2":"transcript","3":"2"},{"1":"AKR1D1","2":"transcript","3":"3"},{"1":"AKR1E2","2":"transcript","3":"4"},{"1":"AKR7A2","2":"transcript","3":"1"},{"1":"AKR7A3","2":"transcript","3":"1"},{"1":"AKT1","2":"transcript","3":"7"},{"1":"AKT1S1","2":"transcript","3":"6"},{"1":"AKT2","2":"transcript","3":"4"},{"1":"AKT3","2":"transcript","3":"4"},{"1":"AKTIP","2":"transcript","3":"3"},{"1":"AL020996.2","2":"transcript","3":"1"},{"1":"AL021546.1","2":"transcript","3":"1"},{"1":"AL021997.3","2":"transcript","3":"1"},{"1":"AL022238.4","2":"transcript","3":"1"},{"1":"AL022312.1","2":"transcript","3":"1"},{"1":"AL022318.4","2":"transcript","3":"1"},{"1":"AL024498.2","2":"transcript","3":"1"},{"1":"AL031315.1","2":"transcript","3":"1"},{"1":"AL031681.2","2":"transcript","3":"1"},{"1":"AL031708.1","2":"transcript","3":"1"},{"1":"AL031777.3","2":"transcript","3":"2"},{"1":"AL031847.2","2":"transcript","3":"1"},{"1":"AL032819.3","2":"transcript","3":"1"},{"1":"AL033529.1","2":"transcript","3":"1"},{"1":"AL034430.1","2":"transcript","3":"1"},{"1":"AL034430.2","2":"transcript","3":"1"},{"1":"AL035078.4","2":"transcript","3":"1"},{"1":"AL035425.2","2":"transcript","3":"1"},{"1":"AL035460.1","2":"transcript","3":"1"},{"1":"AL035461.3","2":"transcript","3":"1"},{"1":"AL049629.2","2":"transcript","3":"1"},{"1":"AL049634.2","2":"transcript","3":"1"},{"1":"AL049650.1","2":"transcript","3":"1"},{"1":"AL049697.1","2":"transcript","3":"1"},{"1":"AL049779.1","2":"transcript","3":"1"},{"1":"AL049834.1","2":"transcript","3":"4"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[19942]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcihnZW5lX3R5cGUgPT0gJ2xuY1JOQScpICU+JSBncm91cF9ieShnZW5lX25hbWUsIGZlYXR1cmVfdHlwZSkgJT4lIGNvdW50KClcbmBgYCJ9 -->

```r
d2 %>% filter(gene_type == 'lncRNA') %>% group_by(gene_name, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjE2ODI1LCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjE2LDgyNSB4IDMiXSwiR3JvdXBzIjpbImdlbmVfbmFtZSwgZmVhdHVyZV90eXBlIFsxNiw4MjVdIl19fSwicmRmIjoiSDRzSUFBQUFBQUFBQnUyYzNXNWN0eEhIVjdia3J6UnBnTnowSlN3c3Z3OHZWekxRWHJnZmFIdVJ1MEIxbE5Tb0t4dXlqTFIzZmEyK1VSK2lpRHRrVm9uTzc3K1NWMTdsb2dBTnlOYitUQTdKNFhBNEhKNnpmM3oyWlhqeTVaUEZZbkYvc2IrM3Q3aC9ZTDh1RG83L1VHTmRMUGJ2MlllOXhmN2ljZnYzSDFib2kxNXlzZmpjL3ZuUCtqOGVyZHpScjUrdS91VFdueCt1L0crdmZIeHNINSs3VGNDdndTZXIxYlBWOFhOL3BjekJhdlhubytQMWh3ZXJvOVh6My85dS9lbXoxZEhSYXVtV3dlV2NEMzhTZW5TOHFyTldqbzZQMHhYd1pIWDBtMmR1VG82WDlpZjRRNUlrSkpPa3BaQ0pKRHNoSXRtSUJ5bFNSaVZQVW1zNkRITlNaRnlURXVsUHJTRE9CUkxQc1R1dlpTSklrUDdFSldvNTc2Rm52NHlSSk9YNTJJMFVLVk9rVEdXWkxKS05vRmFXL21TUlU1WkN2QkJwcTBoYlJkcXFiZ05CclNxU3EwZzJFbWZFTFNuWnVTU0UvV2xrTHRuNWlXVThMTk9ISmVVRVI0MDFnbHFCUFF5UnM5d0lhaVdweGJYalErRklReEhKUldwTlFZaU1xMHF0U20xRTBYd2pXaVlJaVNEc2M1UzVpRElYMFZjUzBWaE10S2lZT05KSVgrZGo1cHcyZ3JZeWRSaGxEVVo2eUU0b1I4WXVhN0FSMUpKVkdTZXBOYkZXY3RSUEl5eERuNUM4a09oWksxS3JpWHRLSjZnbDg1VVN0ZG9JYTlFU0dwbmJXT0xzaENWdDFRam13a2hsR2U2ZW5hQldsTFppa0RLUlpUakxuYUFXMTNKWVRpS0hLOWVJMUtwK0EwRmJWVVpCRHhERTAzYmlRZGhXNW00Vk1uY2ljMnlVbkxtL0crSFlNM2Q4SXh4N25tQXRvZEl5amJBL2xUWVdLdGR5SnloVHFNTksvMnhPbE9PcU1xNHE0Nm95eTQxNElRR0VNMWk1QzNjQ09iU1dhT0dOa0VyaXBKYUxKSXlzTEdpU01rRWtjNitNeXlSdDBXOFlFY24wQ2JGRlJCNUUybUtNRkZ0c2cxcjB2WjFvbVNna0NjbENDZ2hINFVUenpqeDJtSlBBSGpyUnFtTUUwa2tRRW9Va2tFa2tUNVRNdU5lSTFPSXUzTW04bG5jY2hmZE9TR0F0SXhFa3NneGp0azRDQ0RWdm9kNEdRc21CY3FLMEZSMXJSV2s5T281Q1ZvSG43bWxFTkNicm9oSFVZbXdUQXoxMk5OTWtZUndlbzloR0ZOdG9KQWlKSUZ5Vk1WTmpFbzhaeVJzSWEyVzJ6cDNhaUl5OVNPdmN1enZSTW1pTG50OEdLcTJMZjQ0OE44WGtzYjkzRW9SRUVNNU9ZdFJ0QVRYNzB3akxzSWNTTVJxaDFTV1oweFlmb2xiaU9rM2NQVHZCU0VXclNiU2FHUDkwZ3JaVXo1VVdsU3JuUFFjaGpONDc4U0JzUzJLYlR1WWp6WlZ6a2JuakcyRmJoVEdiRWJaZWVQSTF3djRVeGtobTRHeTl5RXBwSkFpSlFoS0l0Q1dXVUdRdUpwNUdPNGxDa3BBTVFtdVpITFU2TWVmUUNjdElEeDI5UkNOQlNCU0NQdnRFT1VZZ1I5YjdKT3Q5a3NoaGtsMnZrU2drQ1lFT0pmYWJlTm95SXRvSW9vMGcycEFZY3VMNXRCTklsb2hvU2pLbjRuK21MTnFRL1hUaVNjR0k5REJYbHVGNXNKTWdCR09YZFRISlRqUVZhWjJua2s2OEVMUStpVFo0NGpBaWJWWEdQMU5sbjZ1Y09LcjRueXJ4UmlOZXlMeXRLaXV1T3BIakdHOVVab21OMExNMWd0WWx6cXl5bWlxejhVWmtGQklmVnRrOUszTU9uV0RzekVJWWtkWmxYVlJaRjFYT0JWWGl1aXE3ZVdXMnB4UG9KM085VjFrcGNzbzJJdU1xWEhGeTd1NkVaV1FVRWlkVXhnQ3A1WkdDa0FpU3BSWm1PY21adWhPVTRmMUZhcWRzbG9uc2o0dnNqeFBKdE9mVXp1WmVTQkFTaFNRaEdhU3dMY2FRUmh6YjRubW5FN1RGT05PSWFKNzVuMDY4RUxUT3ZTQXQ2Zm1UM01La0pUMjJFZEV6bzhFa3R5ZHB5ZE5Xa3Z1dlR0QkQ1Z3FTWkF1TmlOWFI1cFBjM1JpaEhFZVAzWWtIb1g0YW1ldkgwVWNseDFPdEVhbVZhSWVTUFRCQ2pUWGloVUF5WTlwT1VJYjVuMDRnV1RUdkdKMG16eDNOQ0dmSGN5ZEtucmVUU2JJWm5RUVFqc3N6WXV5RXRTcjAzTWg4RFVwV3hJajBVSHlMRjUvZ281UkpucTJMYlVqR3d3ZzE3NW5kVFo3WmcwNkNFTFF1OWlONWt1UVpmWFdDV21JL1h1eW5FZlJIZklMbi9wVzhyRnpQZks4UjZqQXd6OStKQjlGYUhIc2pyRVU3bEJ0Vkk5UllZTjY0RTBpV1ZSQmtGVFNTUUxpK0FtOFZPMEdmR2NVWm9jMDM0b1ZRRHUwNThOelVTUlpTUUdRVXpNQjBndjR3aHV3RS9SRnZIQkw5Um1EbXRoTzB4WXhpQ2p5REpNa29Kcm1uTmlLMk1YRTFCYkg1d0l4Wko2d2xzNlByUW1LL3dPY1RVcFI5TVBKNUFDT1VFM21uWUlUNmFRUmx4RzlFMFdHVU9DSEtUaFJGcXhhYXN5M2VCbmFpWmVZNnRJMFpaUkpQVzBsdW5KTnRlNnpsNmYwYVNTQWNhUksva2NSdnlPMTJTcktXayt4ZlNYWXJNOFFOQkgxbTd0U0lTSlpkVCs3V2pVaGJQRzBsdVg5UGN2OXVSRVlxTVZJU0cwdGlQNGwza1NuSmlrdXk0aVNibWlSM2FvVGp5ckpmTk9KQnRCYlhUaU5CU0JReXQ3RXNLemZMK1NzejcyZUU4OVVJYTlGYU1yTVFOam4wdlZsMm1TeDdTcFk5SmZOR294TzB6ak4xSjJoTFRoeFo1bDJ5eloyZ0xlWi9PbUV0eHFKWnppQ05zRmJlVUF2NnFkeEJHbUV0V2xUTG1TY1FlcHRHdkpBZ0pBcVo3KytGdHptZFFJN1lSdkhVUmlPc1JXMDBnbkVGK28xR0lJZFpyRTVRSzBxdHVHVHJrVjY5aVBjcnpGQjFBc2x5TGloeUxtaUV0ZWgvU3FMZktISmFMN3k3TWNLMUk3Y1ZSbVFVdktVeW9tVmtwSEorTDNKK2w5c0tJL1JJaytSL0p1WXpqWWdjMlUvbFRpSEpuVUluUVVnVU1yY0V1WGN3SXEzTHZpejNCVWFrUDJLWms5amh4QnlzRWVsUDVBeE9jdmFVMjRFMFNZWmhrZ3pESkZtamlmZG9hZUw5VjVMOGZKTDhmSnJFc3pVU2hFUWhTVWdXVW9STU0xTGxwRmxsTjI4a2dIQUdKYzl2aE9PcUVnTlVQZzlwaEd1NXlvNHZkd0dkZUNGQlNBVGhERGFDTWhJZlZ0NUFkWUsySklxcmtxbW9oVHRJbGZOT2xSMjJ5aW1naXYwME1tc3JMNWs5TUlLMmpFUWhSY2hFd255NEVhbkY4MFdXN0hjbllVNjRWeG9SeWZRL1dUTGJlY21ZUHkvcFNUcUJIUHFXTEpudFRpS0lhSU9uOVN4dmRuVGloV0RzZklhekV5MFRoYVE1b1dWbWVTcXZFeThrQ0VGYnpMeGxlUU9pRThxSkcrU3d6MGxxSmRaaXhqNDcrcFpPdkpBQVF2MjRLeWZvVDliRTJsb3FZajFFZHAwa0lWbklKS1RPQ2JOZm5XQWNZdU9PNTFvanRFM0hXQy9MczRSWm5pWE1qaWZkTERjR1JyZ3VuVmkwWTZ5WDVRNGhPNTRWc2w5eUZKN1BsUnZodUR4M09TUHNvUmZmMW9nWEVvUkVJVWxJbVJPWlU4bkhHK0ZjZUVaUzJUTVBrVDF6RE5tTEQ1QThlaWNZRjgrc1JrUU9iNGF6Wk1TejU1N1dpUmVDMXJuTDVTRHpIbVRlQTIrR3MrU2JjK0FUbzUxQWpxeUNsb1ZGR2JId0lCNHg4QzY5RThnUnJRYWVienJCdVBpT1VaWk1iWlkzaW5MMFhFMlJUd0Ixb21YbXJVZnhOcEczeDUxRUVKVkQvVFRpaFdpdEpHVHVWNlA0cU1oc1hDZVFMR3NueW43ZVNCU1NoRXdnTWhmaUQrVXRxQ3h2UWVVbzg5NEl5dkF1SzB0TzJnaHRYbkxTbmN6TEpJa1pFMitjT2tsQzV0NnZ2UVVWaFNRaEdVVDZ3eHhNVHVJaEc0bEMwQmFmdU9uRUM1bGJTeElQMEFqSzhBbm9UaUNaZHdpZHpNdFVpVS9sdWFGT0FnaDdXTVdpR2tFdFBoUFVDY293ZjVtcitERjVTcWdUU09aOVRtbnZ2Z1FRNktmSWFhYklzenhGM29ZcDh1Uk9XWEozS1BJK2Q1RzN0enZ4UW9LUUNES0puSWx5ZUhZcEVsTVhlWWVtRTdUT0c3a2liNlFWZWR1c3lOdmJuY3pia3FkT2lqeDFZb1FhYzR5K2lwUFpjVEk3anU5ekYzbGp1TWdidzBXaVpTTWN1OFNpeGZIcEF5TzBNY2Nid2s3UU9qMXRjY3pIR3hISmZQSzlrM2wvUEc4NU92RkN0RllFb1E0OTcwWTZnUnptWURwQkxXWTBqVkFibmhuTjRzWG1QZmR1STRtRUVhd1J6bzVuRHE5SUJGdmthUkVqb2gvdXNFWTQ3NTQzNTBha3oxTmlMVm1WWHF6RjgvYkdpUFNRTjhNbE1ITlRncXk0d014Tko1RERYRTRKdk9Yb0pJR0lIRWJkUmFKdUk3VHdJSjVXbnJQb0pBcEJmNWluS2ZLY1JaRnZDREFpWTJkMDJra1FndjZJUFFmeFA0Rm50Q0tuQ1NPMEtQbCtnazdRUTU3ak9tRVpXbWJnYlVtUkUwZVI1MGM2UVMzR21aMUFZOHlqRjNuR3BCUFdhanFjZjVuTWs0dnprN08zTDg1ZnZya1laSkJCQmhsa2tFRUdHV1NRUVFZWlpKQkJCaGxra0VFR0dXU1FRUVlaWkpCQkJobGtrRUVHR1dTUVFRWVpaSkJCQmhsa2tFRUdHV1NRUVFZWlpKQkJCaGxra0VFR0dXU1FRUVlaWkpCQkJobGtrRUVHR2VUL2tpd1duL2J2bFZrczdxMS85cTc4M0x2bTkrdCt0aW16eTgvOWRSdjM3MGdlKzd1cjNDYnZJV1JmNnZYQno2eWJiWFYvMHh6ZnhWeHluSnZrYkZObTF6bTViZjgvZG80K3RuOTNaUS9YamZQUlI4N2hiZXA4ek5nZmJtaHIvd050Yjh1MzZjKzlHMlRlLzBCN20rYnZ1ckszMGMzOWErVGNwZTN2NHB1M3JYdXdReHUzSFN2YjJtYnU3dHJmWDhyWVJjN2wzUC9jZStjdU5yQ3RmOTZraDIzbTQ3YSs4TkdXT3QrL29jM2I2R1JYWDcxci9VMTl2bzJkMytXY2Iyc0x1OXIwWjNmUVYvWnRrODNjWkVlYjdPY3VkYmxMM1VjMzlQbW05cTZyOTZIMmIyc1BCMXVPZTVOdFh6Y24xL1hsdHA4L1Z1L2Irdmx0ZGZ4d3kzSjNZUys3ekRuWDljZnFlcGZ4M3ZVNVpkdDJQaVIvR3o5OHFiK2I0aVBHTDFmbDJxbDkvNGZQUDV6Z0QxNjhPbm43ZHRHL0ZQWkgrT1RyazR1VHcyL09ULzUraXVLUHoxOS9kM2htdkZYNXRNbitsLzMxL3YxL2YwVzVsNFUrN3gxYTEvNzI5T3owcS9ZL2EvQ0xiMDVQTHQ2ZG4zNTE4YzgzbDJ6dnpQNzZIdUlldm41ejhmTDFtUW04OThWNjlGYzd2SGNPOE10M1o2MlpyNSsrK091N3M3ODlUVzFRYXcwczFqMjMzMWYvL3VuM3ByWFc1UDc3dGFpRHRhZ0hwMmZmdmp5NzdOM0JxNU8vbkw1YWYvak10TkhIZWZqbS9PWFpqMGtSbzI4UEwxNWZuRnlXZS9MaTlhdEwwc2UyK1A1L2oxYjhiQW1lQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"A1BG-AS1","2":"transcript","3":"2"},{"1":"A2M-AS1","2":"transcript","3":"2"},{"1":"A2ML1-AS1","2":"transcript","3":"1"},{"1":"A2ML1-AS2","2":"transcript","3":"1"},{"1":"AADACL2-AS1","2":"transcript","3":"1"},{"1":"AATBC","2":"transcript","3":"2"},{"1":"ABALON","2":"transcript","3":"1"},{"1":"ABBA01031666.1","2":"transcript","3":"1"},{"1":"ABCA9-AS1","2":"transcript","3":"2"},{"1":"ABCC5-AS1","2":"transcript","3":"1"},{"1":"ABHD15-AS1","2":"transcript","3":"1"},{"1":"AC000032.1","2":"transcript","3":"1"},{"1":"AC000035.1","2":"transcript","3":"1"},{"1":"AC000036.1","2":"transcript","3":"1"},{"1":"AC000050.1","2":"transcript","3":"1"},{"1":"AC000058.1","2":"transcript","3":"1"},{"1":"AC000061.1","2":"transcript","3":"1"},{"1":"AC000065.1","2":"transcript","3":"2"},{"1":"AC000065.2","2":"transcript","3":"1"},{"1":"AC000067.1","2":"transcript","3":"1"},{"1":"AC000068.1","2":"transcript","3":"1"},{"1":"AC000068.2","2":"transcript","3":"1"},{"1":"AC000068.3","2":"transcript","3":"1"},{"1":"AC000072.1","2":"transcript","3":"1"},{"1":"AC000082.1","2":"transcript","3":"1"},{"1":"AC000085.1","2":"transcript","3":"1"},{"1":"AC000099.1","2":"transcript","3":"1"},{"1":"AC000113.1","2":"transcript","3":"1"},{"1":"AC000120.1","2":"transcript","3":"1"},{"1":"AC000123.1","2":"transcript","3":"1"},{"1":"AC000124.1","2":"transcript","3":"1"},{"1":"AC000372.1","2":"transcript","3":"1"},{"1":"AC000403.1","2":"transcript","3":"1"},{"1":"AC001226.1","2":"transcript","3":"1"},{"1":"AC002044.1","2":"transcript","3":"1"},{"1":"AC002056.2","2":"transcript","3":"1"},{"1":"AC002057.1","2":"transcript","3":"1"},{"1":"AC002057.2","2":"transcript","3":"1"},{"1":"AC002059.1","2":"transcript","3":"1"},{"1":"AC002064.1","2":"transcript","3":"1"},{"1":"AC002064.2","2":"transcript","3":"1"},{"1":"AC002066.1","2":"transcript","3":"3"},{"1":"AC002069.1","2":"transcript","3":"2"},{"1":"AC002070.1","2":"transcript","3":"3"},{"1":"AC002072.1","2":"transcript","3":"1"},{"1":"AC002074.1","2":"transcript","3":"1"},{"1":"AC002074.2","2":"transcript","3":"1"},{"1":"AC002076.1","2":"transcript","3":"1"},{"1":"AC002091.1","2":"transcript","3":"1"},{"1":"AC002091.2","2":"transcript","3":"1"},{"1":"AC002094.1","2":"transcript","3":"1"},{"1":"AC002094.2","2":"transcript","3":"1"},{"1":"AC002094.4","2":"transcript","3":"1"},{"1":"AC002101.1","2":"transcript","3":"1"},{"1":"AC002115.1","2":"transcript","3":"1"},{"1":"AC002116.1","2":"transcript","3":"1"},{"1":"AC002116.2","2":"transcript","3":"1"},{"1":"AC002128.1","2":"transcript","3":"1"},{"1":"AC002128.2","2":"transcript","3":"1"},{"1":"AC002306.1","2":"transcript","3":"1"},{"1":"AC002310.1","2":"transcript","3":"1"},{"1":"AC002310.2","2":"transcript","3":"2"},{"1":"AC002331.1","2":"transcript","3":"1"},{"1":"AC002347.1","2":"transcript","3":"1"},{"1":"AC002347.2","2":"transcript","3":"1"},{"1":"AC002351.1","2":"transcript","3":"3"},{"1":"AC002368.1","2":"transcript","3":"1"},{"1":"AC002375.1","2":"transcript","3":"1"},{"1":"AC002377.1","2":"transcript","3":"1"},{"1":"AC002378.1","2":"transcript","3":"1"},{"1":"AC002383.1","2":"transcript","3":"1"},{"1":"AC002386.1","2":"transcript","3":"1"},{"1":"AC002398.1","2":"transcript","3":"1"},{"1":"AC002398.2","2":"transcript","3":"1"},{"1":"AC002401.1","2":"transcript","3":"1"},{"1":"AC002401.2","2":"transcript","3":"1"},{"1":"AC002401.3","2":"transcript","3":"1"},{"1":"AC002401.4","2":"transcript","3":"1"},{"1":"AC002407.1","2":"transcript","3":"1"},{"1":"AC002428.1","2":"transcript","3":"1"},{"1":"AC002428.2","2":"transcript","3":"1"},{"1":"AC002429.2","2":"transcript","3":"2"},{"1":"AC002451.1","2":"transcript","3":"7"},{"1":"AC002454.1","2":"transcript","3":"1"},{"1":"AC002456.1","2":"transcript","3":"2"},{"1":"AC002458.1","2":"transcript","3":"1"},{"1":"AC002460.1","2":"transcript","3":"2"},{"1":"AC002460.2","2":"transcript","3":"2"},{"1":"AC002463.1","2":"transcript","3":"6"},{"1":"AC002464.1","2":"transcript","3":"1"},{"1":"AC002465.1","2":"transcript","3":"1"},{"1":"AC002465.2","2":"transcript","3":"1"},{"1":"AC002467.1","2":"transcript","3":"1"},{"1":"AC002470.1","2":"transcript","3":"1"},{"1":"AC002470.2","2":"transcript","3":"1"},{"1":"AC002472.1","2":"transcript","3":"1"},{"1":"AC002480.1","2":"transcript","3":"1"},{"1":"AC002480.2","2":"transcript","3":"1"},{"1":"AC002511.1","2":"transcript","3":"1"},{"1":"AC002511.2","2":"transcript","3":"1"},{"1":"AC002519.1","2":"transcript","3":"1"},{"1":"AC002529.1","2":"transcript","3":"1"},{"1":"AC002542.2","2":"transcript","3":"1"},{"1":"AC002546.1","2":"transcript","3":"1"},{"1":"AC002550.1","2":"transcript","3":"1"},{"1":"AC002550.2","2":"transcript","3":"1"},{"1":"AC002551.1","2":"transcript","3":"1"},{"1":"AC002553.1","2":"transcript","3":"1"},{"1":"AC002553.2","2":"transcript","3":"1"},{"1":"AC002558.2","2":"transcript","3":"1"},{"1":"AC002558.3","2":"transcript","3":"1"},{"1":"AC002563.1","2":"transcript","3":"1"},{"1":"AC003001.1","2":"transcript","3":"2"},{"1":"AC003005.2","2":"transcript","3":"1"},{"1":"AC003009.1","2":"transcript","3":"1"},{"1":"AC003035.1","2":"transcript","3":"1"},{"1":"AC003035.2","2":"transcript","3":"1"},{"1":"AC003043.1","2":"transcript","3":"1"},{"1":"AC003043.2","2":"transcript","3":"1"},{"1":"AC003044.1","2":"transcript","3":"1"},{"1":"AC003070.1","2":"transcript","3":"1"},{"1":"AC003070.2","2":"transcript","3":"1"},{"1":"AC003077.1","2":"transcript","3":"2"},{"1":"AC003084.1","2":"transcript","3":"1"},{"1":"AC003086.1","2":"transcript","3":"1"},{"1":"AC003087.1","2":"transcript","3":"1"},{"1":"AC003092.1","2":"transcript","3":"2"},{"1":"AC003092.2","2":"transcript","3":"1"},{"1":"AC003093.1","2":"transcript","3":"1"},{"1":"AC003098.1","2":"transcript","3":"1"},{"1":"AC003101.1","2":"transcript","3":"1"},{"1":"AC003101.2","2":"transcript","3":"1"},{"1":"AC003102.1","2":"transcript","3":"2"},{"1":"AC003659.1","2":"transcript","3":"1"},{"1":"AC003666.1","2":"transcript","3":"1"},{"1":"AC003681.1","2":"transcript","3":"1"},{"1":"AC003682.1","2":"transcript","3":"1"},{"1":"AC003684.1","2":"transcript","3":"1"},{"1":"AC003685.1","2":"transcript","3":"1"},{"1":"AC003687.1","2":"transcript","3":"1"},{"1":"AC003688.2","2":"transcript","3":"1"},{"1":"AC003950.1","2":"transcript","3":"1"},{"1":"AC003956.1","2":"transcript","3":"1"},{"1":"AC003958.2","2":"transcript","3":"1"},{"1":"AC003965.1","2":"transcript","3":"1"},{"1":"AC003965.2","2":"transcript","3":"1"},{"1":"AC003973.1","2":"transcript","3":"6"},{"1":"AC003975.1","2":"transcript","3":"1"},{"1":"AC003982.1","2":"transcript","3":"1"},{"1":"AC003984.1","2":"transcript","3":"1"},{"1":"AC003985.1","2":"transcript","3":"2"},{"1":"AC003986.1","2":"transcript","3":"1"},{"1":"AC003986.2","2":"transcript","3":"1"},{"1":"AC003986.3","2":"transcript","3":"1"},{"1":"AC003988.1","2":"transcript","3":"1"},{"1":"AC003991.1","2":"transcript","3":"6"},{"1":"AC003991.2","2":"transcript","3":"1"},{"1":"AC003992.1","2":"transcript","3":"1"},{"1":"AC004000.1","2":"transcript","3":"1"},{"1":"AC004009.1","2":"transcript","3":"2"},{"1":"AC004012.1","2":"transcript","3":"1"},{"1":"AC004014.1","2":"transcript","3":"1"},{"1":"AC004023.1","2":"transcript","3":"1"},{"1":"AC004034.1","2":"transcript","3":"1"},{"1":"AC004039.1","2":"transcript","3":"1"},{"1":"AC004047.1","2":"transcript","3":"1"},{"1":"AC004052.1","2":"transcript","3":"3"},{"1":"AC004053.1","2":"transcript","3":"1"},{"1":"AC004054.1","2":"transcript","3":"1"},{"1":"AC004063.1","2":"transcript","3":"1"},{"1":"AC004066.2","2":"transcript","3":"1"},{"1":"AC004067.1","2":"transcript","3":"1"},{"1":"AC004069.1","2":"transcript","3":"1"},{"1":"AC004076.2","2":"transcript","3":"1"},{"1":"AC004080.1","2":"transcript","3":"1"},{"1":"AC004080.2","2":"transcript","3":"1"},{"1":"AC004080.4","2":"transcript","3":"1"},{"1":"AC004080.5","2":"transcript","3":"1"},{"1":"AC004080.6","2":"transcript","3":"1"},{"1":"AC004080.7","2":"transcript","3":"1"},{"1":"AC004083.1","2":"transcript","3":"2"},{"1":"AC004112.1","2":"transcript","3":"1"},{"1":"AC004129.3","2":"transcript","3":"1"},{"1":"AC004130.1","2":"transcript","3":"1"},{"1":"AC004147.1","2":"transcript","3":"1"},{"1":"AC004147.2","2":"transcript","3":"1"},{"1":"AC004147.3","2":"transcript","3":"1"},{"1":"AC004147.4","2":"transcript","3":"1"},{"1":"AC004147.5","2":"transcript","3":"1"},{"1":"AC004148.1","2":"transcript","3":"1"},{"1":"AC004148.2","2":"transcript","3":"1"},{"1":"AC004156.2","2":"transcript","3":"1"},{"1":"AC004158.1","2":"transcript","3":"2"},{"1":"AC004160.1","2":"transcript","3":"6"},{"1":"AC004160.2","2":"transcript","3":"1"},{"1":"AC004217.1","2":"transcript","3":"1"},{"1":"AC004221.1","2":"transcript","3":"1"},{"1":"AC004223.2","2":"transcript","3":"1"},{"1":"AC004223.4","2":"transcript","3":"1"},{"1":"AC004224.2","2":"transcript","3":"1"},{"1":"AC004231.1","2":"transcript","3":"1"},{"1":"AC004231.3","2":"transcript","3":"1"},{"1":"AC004232.1","2":"transcript","3":"1"},{"1":"AC004233.1","2":"transcript","3":"1"},{"1":"AC004233.2","2":"transcript","3":"1"},{"1":"AC004233.3","2":"transcript","3":"1"},{"1":"AC004241.1","2":"transcript","3":"3"},{"1":"AC004241.2","2":"transcript","3":"1"},{"1":"AC004241.3","2":"transcript","3":"1"},{"1":"AC004241.4","2":"transcript","3":"1"},{"1":"AC004253.1","2":"transcript","3":"1"},{"1":"AC004253.2","2":"transcript","3":"1"},{"1":"AC004257.1","2":"transcript","3":"1"},{"1":"AC004263.1","2":"transcript","3":"1"},{"1":"AC004263.2","2":"transcript","3":"1"},{"1":"AC004264.1","2":"transcript","3":"1"},{"1":"AC004381.1","2":"transcript","3":"1"},{"1":"AC004408.1","2":"transcript","3":"1"},{"1":"AC004415.1","2":"transcript","3":"1"},{"1":"AC004448.1","2":"transcript","3":"1"},{"1":"AC004448.2","2":"transcript","3":"6"},{"1":"AC004448.3","2":"transcript","3":"1"},{"1":"AC004448.4","2":"transcript","3":"1"},{"1":"AC004449.1","2":"transcript","3":"1"},{"1":"AC004461.2","2":"transcript","3":"1"},{"1":"AC004464.1","2":"transcript","3":"1"},{"1":"AC004466.1","2":"transcript","3":"1"},{"1":"AC004466.2","2":"transcript","3":"1"},{"1":"AC004466.3","2":"transcript","3":"1"},{"1":"AC004470.1","2":"transcript","3":"1"},{"1":"AC004471.1","2":"transcript","3":"1"},{"1":"AC004471.2","2":"transcript","3":"1"},{"1":"AC004477.1","2":"transcript","3":"1"},{"1":"AC004477.2","2":"transcript","3":"1"},{"1":"AC004477.3","2":"transcript","3":"1"},{"1":"AC004485.1","2":"transcript","3":"1"},{"1":"AC004490.1","2":"transcript","3":"1"},{"1":"AC004492.1","2":"transcript","3":"1"},{"1":"AC004494.1","2":"transcript","3":"2"},{"1":"AC004522.2","2":"transcript","3":"1"},{"1":"AC004522.3","2":"transcript","3":"1"},{"1":"AC004522.4","2":"transcript","3":"1"},{"1":"AC004528.1","2":"transcript","3":"1"},{"1":"AC004528.2","2":"transcript","3":"1"},{"1":"AC004540.1","2":"transcript","3":"8"},{"1":"AC004540.2","2":"transcript","3":"2"},{"1":"AC004542.1","2":"transcript","3":"1"},{"1":"AC004542.2","2":"transcript","3":"1"},{"1":"AC004543.1","2":"transcript","3":"1"},{"1":"AC004549.1","2":"transcript","3":"1"},{"1":"AC004551.2","2":"transcript","3":"1"},{"1":"AC004554.2","2":"transcript","3":"1"},{"1":"AC004584.1","2":"transcript","3":"1"},{"1":"AC004584.3","2":"transcript","3":"1"},{"1":"AC004585.1","2":"transcript","3":"1"},{"1":"AC004590.1","2":"transcript","3":"1"},{"1":"AC004593.1","2":"transcript","3":"1"},{"1":"AC004593.2","2":"transcript","3":"1"},{"1":"AC004594.1","2":"transcript","3":"2"},{"1":"AC004596.1","2":"transcript","3":"1"},{"1":"AC004597.1","2":"transcript","3":"1"},{"1":"AC004637.1","2":"transcript","3":"1"},{"1":"AC004672.1","2":"transcript","3":"1"},{"1":"AC004672.2","2":"transcript","3":"1"},{"1":"AC004674.1","2":"transcript","3":"1"},{"1":"AC004687.1","2":"transcript","3":"1"},{"1":"AC004687.3","2":"transcript","3":"1"},{"1":"AC004690.2","2":"transcript","3":"1"},{"1":"AC004691.1","2":"transcript","3":"1"},{"1":"AC004692.2","2":"transcript","3":"3"},{"1":"AC004702.1","2":"transcript","3":"1"},{"1":"AC004704.1","2":"transcript","3":"1"},{"1":"AC004706.1","2":"transcript","3":"1"},{"1":"AC004707.1","2":"transcript","3":"1"},{"1":"AC004765.1","2":"transcript","3":"1"},{"1":"AC004771.1","2":"transcript","3":"1"},{"1":"AC004771.2","2":"transcript","3":"1"},{"1":"AC004771.3","2":"transcript","3":"1"},{"1":"AC004771.4","2":"transcript","3":"1"},{"1":"AC004771.5","2":"transcript","3":"1"},{"1":"AC004775.1","2":"transcript","3":"1"},{"1":"AC004784.1","2":"transcript","3":"1"},{"1":"AC004797.1","2":"transcript","3":"7"},{"1":"AC004801.3","2":"transcript","3":"1"},{"1":"AC004801.4","2":"transcript","3":"1"},{"1":"AC004801.5","2":"transcript","3":"2"},{"1":"AC004801.6","2":"transcript","3":"1"},{"1":"AC004803.1","2":"transcript","3":"4"},{"1":"AC004812.2","2":"transcript","3":"1"},{"1":"AC004816.1","2":"transcript","3":"2"},{"1":"AC004816.2","2":"transcript","3":"1"},{"1":"AC004817.1","2":"transcript","3":"1"},{"1":"AC004817.2","2":"transcript","3":"1"},{"1":"AC004817.3","2":"transcript","3":"1"},{"1":"AC004817.4","2":"transcript","3":"1"},{"1":"AC004817.5","2":"transcript","3":"2"},{"1":"AC004825.2","2":"transcript","3":"1"},{"1":"AC004825.3","2":"transcript","3":"1"},{"1":"AC004828.1","2":"transcript","3":"1"},{"1":"AC004828.2","2":"transcript","3":"1"},{"1":"AC004830.1","2":"transcript","3":"1"},{"1":"AC004832.1","2":"transcript","3":"2"},{"1":"AC004832.4","2":"transcript","3":"1"},{"1":"AC004832.5","2":"transcript","3":"1"},{"1":"AC004832.6","2":"transcript","3":"1"},{"1":"AC004834.1","2":"transcript","3":"3"},{"1":"AC004835.1","2":"transcript","3":"1"},{"1":"AC004837.2","2":"transcript","3":"1"},{"1":"AC004837.3","2":"transcript","3":"1"},{"1":"AC004837.4","2":"transcript","3":"1"},{"1":"AC004839.1","2":"transcript","3":"1"},{"1":"AC004846.1","2":"transcript","3":"1"},{"1":"AC004846.2","2":"transcript","3":"1"},{"1":"AC004847.1","2":"transcript","3":"1"},{"1":"AC004852.2","2":"transcript","3":"2"},{"1":"AC004854.2","2":"transcript","3":"1"},{"1":"AC004862.1","2":"transcript","3":"2"},{"1":"AC004863.1","2":"transcript","3":"1"},{"1":"AC004865.2","2":"transcript","3":"1"},{"1":"AC004869.1","2":"transcript","3":"1"},{"1":"AC004869.2","2":"transcript","3":"1"},{"1":"AC004870.2","2":"transcript","3":"3"},{"1":"AC004870.3","2":"transcript","3":"1"},{"1":"AC004870.4","2":"transcript","3":"2"},{"1":"AC004875.1","2":"transcript","3":"1"},{"1":"AC004877.1","2":"transcript","3":"1"},{"1":"AC004879.1","2":"transcript","3":"1"},{"1":"AC004882.1","2":"transcript","3":"1"},{"1":"AC004882.2","2":"transcript","3":"1"},{"1":"AC004882.3","2":"transcript","3":"1"},{"1":"AC004884.2","2":"transcript","3":"1"},{"1":"AC004888.1","2":"transcript","3":"1"},{"1":"AC004889.1","2":"transcript","3":"6"},{"1":"AC004893.3","2":"transcript","3":"1"},{"1":"AC004895.1","2":"transcript","3":"2"},{"1":"AC004900.1","2":"transcript","3":"1"},{"1":"AC004906.1","2":"transcript","3":"1"},{"1":"AC004908.1","2":"transcript","3":"1"},{"1":"AC004908.2","2":"transcript","3":"1"},{"1":"AC004908.3","2":"transcript","3":"1"},{"1":"AC004917.1","2":"transcript","3":"3"},{"1":"AC004918.1","2":"transcript","3":"1"},{"1":"AC004918.4","2":"transcript","3":"1"},{"1":"AC004920.1","2":"transcript","3":"1"},{"1":"AC004921.1","2":"transcript","3":"1"},{"1":"AC004921.2","2":"transcript","3":"1"},{"1":"AC004923.4","2":"transcript","3":"1"},{"1":"AC004930.1","2":"transcript","3":"1"},{"1":"AC004936.1","2":"transcript","3":"1"},{"1":"AC004938.2","2":"transcript","3":"1"},{"1":"AC004941.1","2":"transcript","3":"1"},{"1":"AC004943.1","2":"transcript","3":"3"},{"1":"AC004943.2","2":"transcript","3":"2"},{"1":"AC004943.3","2":"transcript","3":"1"},{"1":"AC004944.1","2":"transcript","3":"1"},{"1":"AC004946.1","2":"transcript","3":"1"},{"1":"AC004946.2","2":"transcript","3":"1"},{"1":"AC004947.1","2":"transcript","3":"3"},{"1":"AC004947.3","2":"transcript","3":"1"},{"1":"AC004948.1","2":"transcript","3":"1"},{"1":"AC004949.1","2":"transcript","3":"1"},{"1":"AC004951.1","2":"transcript","3":"1"},{"1":"AC004951.4","2":"transcript","3":"1"},{"1":"AC004967.2","2":"transcript","3":"1"},{"1":"AC004969.1","2":"transcript","3":"1"},{"1":"AC004973.1","2":"transcript","3":"1"},{"1":"AC004974.1","2":"transcript","3":"1"},{"1":"AC004975.2","2":"transcript","3":"1"},{"1":"AC004982.1","2":"transcript","3":"1"},{"1":"AC004982.2","2":"transcript","3":"1"},{"1":"AC004988.1","2":"transcript","3":"1"},{"1":"AC004990.1","2":"transcript","3":"2"},{"1":"AC004994.1","2":"transcript","3":"1"},{"1":"AC005005.3","2":"transcript","3":"1"},{"1":"AC005005.4","2":"transcript","3":"1"},{"1":"AC005006.1","2":"transcript","3":"1"},{"1":"AC005008.2","2":"transcript","3":"2"},{"1":"AC005009.1","2":"transcript","3":"1"},{"1":"AC005009.2","2":"transcript","3":"1"},{"1":"AC005013.1","2":"transcript","3":"1"},{"1":"AC005014.2","2":"transcript","3":"1"},{"1":"AC005014.3","2":"transcript","3":"1"},{"1":"AC005014.4","2":"transcript","3":"1"},{"1":"AC005019.2","2":"transcript","3":"1"},{"1":"AC005021.1","2":"transcript","3":"1"},{"1":"AC005034.2","2":"transcript","3":"1"},{"1":"AC005034.3","2":"transcript","3":"1"},{"1":"AC005034.4","2":"transcript","3":"1"},{"1":"AC005034.5","2":"transcript","3":"1"},{"1":"AC005034.6","2":"transcript","3":"1"},{"1":"AC005037.1","2":"transcript","3":"1"},{"1":"AC005040.2","2":"transcript","3":"2"},{"1":"AC005041.3","2":"transcript","3":"1"},{"1":"AC005041.4","2":"transcript","3":"1"},{"1":"AC005041.5","2":"transcript","3":"1"},{"1":"AC005042.2","2":"transcript","3":"1"},{"1":"AC005046.1","2":"transcript","3":"1"},{"1":"AC005050.1","2":"transcript","3":"1"},{"1":"AC005050.2","2":"transcript","3":"1"},{"1":"AC005050.3","2":"transcript","3":"1"},{"1":"AC005052.2","2":"transcript","3":"1"},{"1":"AC005062.1","2":"transcript","3":"5"},{"1":"AC005064.1","2":"transcript","3":"1"},{"1":"AC005070.3","2":"transcript","3":"1"},{"1":"AC005071.1","2":"transcript","3":"1"},{"1":"AC005072.1","2":"transcript","3":"1"},{"1":"AC005076.1","2":"transcript","3":"1"},{"1":"AC005081.1","2":"transcript","3":"1"},{"1":"AC005082.1","2":"transcript","3":"1"},{"1":"AC005082.2","2":"transcript","3":"1"},{"1":"AC005083.1","2":"transcript","3":"1"},{"1":"AC005086.1","2":"transcript","3":"1"},{"1":"AC005089.1","2":"transcript","3":"1"},{"1":"AC005090.1","2":"transcript","3":"1"},{"1":"AC005091.1","2":"transcript","3":"1"},{"1":"AC005096.1","2":"transcript","3":"1"},{"1":"AC005100.1","2":"transcript","3":"3"},{"1":"AC005100.2","2":"transcript","3":"1"},{"1":"AC005104.1","2":"transcript","3":"1"},{"1":"AC005104.3","2":"transcript","3":"1"},{"1":"AC005144.1","2":"transcript","3":"1"},{"1":"AC005153.1","2":"transcript","3":"1"},{"1":"AC005154.3","2":"transcript","3":"1"},{"1":"AC005154.4","2":"transcript","3":"1"},{"1":"AC005160.1","2":"transcript","3":"1"},{"1":"AC005162.1","2":"transcript","3":"1"},{"1":"AC005162.2","2":"transcript","3":"1"},{"1":"AC005162.3","2":"transcript","3":"1"},{"1":"AC005165.1","2":"transcript","3":"5"},{"1":"AC005165.3","2":"transcript","3":"1"},{"1":"AC005180.1","2":"transcript","3":"1"},{"1":"AC005180.2","2":"transcript","3":"1"},{"1":"AC005186.1","2":"transcript","3":"3"},{"1":"AC005197.1","2":"transcript","3":"1"},{"1":"AC005208.1","2":"transcript","3":"2"},{"1":"AC005209.1","2":"transcript","3":"1"},{"1":"AC005220.1","2":"transcript","3":"1"},{"1":"AC005224.1","2":"transcript","3":"1"},{"1":"AC005224.2","2":"transcript","3":"1"},{"1":"AC005224.3","2":"transcript","3":"1"},{"1":"AC005225.1","2":"transcript","3":"1"},{"1":"AC005225.2","2":"transcript","3":"2"},{"1":"AC005225.3","2":"transcript","3":"1"},{"1":"AC005229.4","2":"transcript","3":"1"},{"1":"AC005229.5","2":"transcript","3":"1"},{"1":"AC005232.1","2":"transcript","3":"3"},{"1":"AC005234.1","2":"transcript","3":"1"},{"1":"AC005234.2","2":"transcript","3":"1"},{"1":"AC005237.1","2":"transcript","3":"1"},{"1":"AC005244.2","2":"transcript","3":"1"},{"1":"AC005252.4","2":"transcript","3":"1"},{"1":"AC005253.1","2":"transcript","3":"1"},{"1":"AC005253.2","2":"transcript","3":"1"},{"1":"AC005256.1","2":"transcript","3":"1"},{"1":"AC005258.2","2":"transcript","3":"1"},{"1":"AC005261.2","2":"transcript","3":"1"},{"1":"AC005261.3","2":"transcript","3":"1"},{"1":"AC005261.4","2":"transcript","3":"1"},{"1":"AC005262.2","2":"transcript","3":"1"},{"1":"AC005264.1","2":"transcript","3":"1"},{"1":"AC005277.1","2":"transcript","3":"1"},{"1":"AC005277.2","2":"transcript","3":"1"},{"1":"AC005280.1","2":"transcript","3":"3"},{"1":"AC005280.2","2":"transcript","3":"3"},{"1":"AC005280.3","2":"transcript","3":"1"},{"1":"AC005281.1","2":"transcript","3":"1"},{"1":"AC005288.1","2":"transcript","3":"1"},{"1":"AC005291.1","2":"transcript","3":"1"},{"1":"AC005291.2","2":"transcript","3":"1"},{"1":"AC005293.1","2":"transcript","3":"1"},{"1":"AC005301.1","2":"transcript","3":"1"},{"1":"AC005301.2","2":"transcript","3":"1"},{"1":"AC005303.1","2":"transcript","3":"1"},{"1":"AC005304.1","2":"transcript","3":"1"},{"1":"AC005304.2","2":"transcript","3":"1"},{"1":"AC005304.3","2":"transcript","3":"1"},{"1":"AC005306.1","2":"transcript","3":"1"},{"1":"AC005307.1","2":"transcript","3":"1"},{"1":"AC005323.1","2":"transcript","3":"3"},{"1":"AC005323.2","2":"transcript","3":"2"},{"1":"AC005324.1","2":"transcript","3":"2"},{"1":"AC005324.2","2":"transcript","3":"1"},{"1":"AC005324.5","2":"transcript","3":"1"},{"1":"AC005329.1","2":"transcript","3":"1"},{"1":"AC005329.2","2":"transcript","3":"1"},{"1":"AC005329.3","2":"transcript","3":"1"},{"1":"AC005330.1","2":"transcript","3":"1"},{"1":"AC005332.1","2":"transcript","3":"1"},{"1":"AC005332.2","2":"transcript","3":"1"},{"1":"AC005332.3","2":"transcript","3":"1"},{"1":"AC005332.4","2":"transcript","3":"1"},{"1":"AC005332.5","2":"transcript","3":"1"},{"1":"AC005332.6","2":"transcript","3":"1"},{"1":"AC005332.7","2":"transcript","3":"1"},{"1":"AC005339.1","2":"transcript","3":"1"},{"1":"AC005342.1","2":"transcript","3":"1"},{"1":"AC005342.2","2":"transcript","3":"1"},{"1":"AC005343.1","2":"transcript","3":"1"},{"1":"AC005343.4","2":"transcript","3":"1"},{"1":"AC005344.1","2":"transcript","3":"1"},{"1":"AC005355.1","2":"transcript","3":"1"},{"1":"AC005358.1","2":"transcript","3":"1"},{"1":"AC005358.2","2":"transcript","3":"1"},{"1":"AC005363.2","2":"transcript","3":"1"},{"1":"AC005379.1","2":"transcript","3":"1"},{"1":"AC005381.1","2":"transcript","3":"2"},{"1":"AC005383.1","2":"transcript","3":"1"},{"1":"AC005387.1","2":"transcript","3":"1"},{"1":"AC005387.2","2":"transcript","3":"1"},{"1":"AC005391.1","2":"transcript","3":"1"},{"1":"AC005392.1","2":"transcript","3":"1"},{"1":"AC005392.2","2":"transcript","3":"1"},{"1":"AC005392.3","2":"transcript","3":"1"},{"1":"AC005393.1","2":"transcript","3":"1"},{"1":"AC005394.1","2":"transcript","3":"1"},{"1":"AC005394.2","2":"transcript","3":"1"},{"1":"AC005400.1","2":"transcript","3":"1"},{"1":"AC005410.2","2":"transcript","3":"1"},{"1":"AC005414.1","2":"transcript","3":"1"},{"1":"AC005476.2","2":"transcript","3":"2"},{"1":"AC005479.1","2":"transcript","3":"1"},{"1":"AC005479.2","2":"transcript","3":"1"},{"1":"AC005480.1","2":"transcript","3":"1"},{"1":"AC005481.1","2":"transcript","3":"1"},{"1":"AC005482.1","2":"transcript","3":"1"},{"1":"AC005486.1","2":"transcript","3":"1"},{"1":"AC005487.1","2":"transcript","3":"3"},{"1":"AC005495.2","2":"transcript","3":"1"},{"1":"AC005498.1","2":"transcript","3":"1"},{"1":"AC005498.2","2":"transcript","3":"3"},{"1":"AC005498.3","2":"transcript","3":"1"},{"1":"AC005515.2","2":"transcript","3":"1"},{"1":"AC005518.1","2":"transcript","3":"1"},{"1":"AC005519.1","2":"transcript","3":"1"},{"1":"AC005520.2","2":"transcript","3":"2"},{"1":"AC005520.3","2":"transcript","3":"1"},{"1":"AC005520.5","2":"transcript","3":"1"},{"1":"AC005522.1","2":"transcript","3":"1"},{"1":"AC005523.1","2":"transcript","3":"1"},{"1":"AC005523.2","2":"transcript","3":"1"},{"1":"AC005529.1","2":"transcript","3":"1"},{"1":"AC005532.1","2":"transcript","3":"2"},{"1":"AC005534.1","2":"transcript","3":"1"},{"1":"AC005537.1","2":"transcript","3":"6"},{"1":"AC005538.1","2":"transcript","3":"1"},{"1":"AC005538.3","2":"transcript","3":"1"},{"1":"AC005540.1","2":"transcript","3":"1"},{"1":"AC005544.1","2":"transcript","3":"1"},{"1":"AC005544.2","2":"transcript","3":"1"},{"1":"AC005546.1","2":"transcript","3":"1"},{"1":"AC005548.1","2":"transcript","3":"1"},{"1":"AC005549.1","2":"transcript","3":"1"},{"1":"AC005550.1","2":"transcript","3":"1"},{"1":"AC005550.2","2":"transcript","3":"1"},{"1":"AC005552.1","2":"transcript","3":"1"},{"1":"AC005562.1","2":"transcript","3":"8"},{"1":"AC005580.1","2":"transcript","3":"3"},{"1":"AC005586.1","2":"transcript","3":"1"},{"1":"AC005586.2","2":"transcript","3":"1"},{"1":"AC005592.1","2":"transcript","3":"1"},{"1":"AC005592.2","2":"transcript","3":"1"},{"1":"AC005594.1","2":"transcript","3":"1"},{"1":"AC005597.1","2":"transcript","3":"1"},{"1":"AC005599.1","2":"transcript","3":"1"},{"1":"AC005606.1","2":"transcript","3":"1"},{"1":"AC005606.2","2":"transcript","3":"4"},{"1":"AC005609.1","2":"transcript","3":"1"},{"1":"AC005609.2","2":"transcript","3":"1"},{"1":"AC005609.3","2":"transcript","3":"1"},{"1":"AC005609.4","2":"transcript","3":"1"},{"1":"AC005609.5","2":"transcript","3":"2"},{"1":"AC005614.1","2":"transcript","3":"1"},{"1":"AC005614.2","2":"transcript","3":"1"},{"1":"AC005616.1","2":"transcript","3":"1"},{"1":"AC005618.1","2":"transcript","3":"2"},{"1":"AC005618.2","2":"transcript","3":"1"},{"1":"AC005618.3","2":"transcript","3":"1"},{"1":"AC005618.4","2":"transcript","3":"1"},{"1":"AC005625.1","2":"transcript","3":"1"},{"1":"AC005632.2","2":"transcript","3":"1"},{"1":"AC005632.4","2":"transcript","3":"1"},{"1":"AC005632.5","2":"transcript","3":"1"},{"1":"AC005670.1","2":"transcript","3":"1"},{"1":"AC005670.3","2":"transcript","3":"6"},{"1":"AC005674.1","2":"transcript","3":"1"},{"1":"AC005674.2","2":"transcript","3":"1"},{"1":"AC005682.2","2":"transcript","3":"1"},{"1":"AC005692.1","2":"transcript","3":"1"},{"1":"AC005692.2","2":"transcript","3":"1"},{"1":"AC005692.3","2":"transcript","3":"1"},{"1":"AC005695.1","2":"transcript","3":"1"},{"1":"AC005695.2","2":"transcript","3":"1"},{"1":"AC005695.3","2":"transcript","3":"1"},{"1":"AC005696.1","2":"transcript","3":"1"},{"1":"AC005696.2","2":"transcript","3":"1"},{"1":"AC005696.3","2":"transcript","3":"1"},{"1":"AC005696.4","2":"transcript","3":"1"},{"1":"AC005697.2","2":"transcript","3":"1"},{"1":"AC005697.3","2":"transcript","3":"1"},{"1":"AC005699.1","2":"transcript","3":"6"},{"1":"AC005702.5","2":"transcript","3":"1"},{"1":"AC005703.1","2":"transcript","3":"1"},{"1":"AC005703.2","2":"transcript","3":"1"},{"1":"AC005703.3","2":"transcript","3":"1"},{"1":"AC005703.4","2":"transcript","3":"1"},{"1":"AC005703.7","2":"transcript","3":"1"},{"1":"AC005722.2","2":"transcript","3":"1"},{"1":"AC005722.3","2":"transcript","3":"1"},{"1":"AC005725.1","2":"transcript","3":"1"},{"1":"AC005726.2","2":"transcript","3":"1"},{"1":"AC005726.3","2":"transcript","3":"1"},{"1":"AC005726.4","2":"transcript","3":"1"},{"1":"AC005726.5","2":"transcript","3":"1"},{"1":"AC005730.2","2":"transcript","3":"1"},{"1":"AC005730.3","2":"transcript","3":"1"},{"1":"AC005736.1","2":"transcript","3":"2"},{"1":"AC005736.2","2":"transcript","3":"1"},{"1":"AC005740.3","2":"transcript","3":"1"},{"1":"AC005740.4","2":"transcript","3":"1"},{"1":"AC005740.5","2":"transcript","3":"2"},{"1":"AC005746.1","2":"transcript","3":"2"},{"1":"AC005746.2","2":"transcript","3":"1"},{"1":"AC005746.3","2":"transcript","3":"1"},{"1":"AC005753.1","2":"transcript","3":"1"},{"1":"AC005753.2","2":"transcript","3":"1"},{"1":"AC005753.3","2":"transcript","3":"1"},{"1":"AC005757.1","2":"transcript","3":"1"},{"1":"AC005759.2","2":"transcript","3":"2"},{"1":"AC005772.1","2":"transcript","3":"1"},{"1":"AC005772.2","2":"transcript","3":"1"},{"1":"AC005774.1","2":"transcript","3":"1"},{"1":"AC005775.1","2":"transcript","3":"1"},{"1":"AC005776.2","2":"transcript","3":"1"},{"1":"AC005785.1","2":"transcript","3":"1"},{"1":"AC005786.2","2":"transcript","3":"1"},{"1":"AC005786.3","2":"transcript","3":"1"},{"1":"AC005789.1","2":"transcript","3":"1"},{"1":"AC005790.1","2":"transcript","3":"1"},{"1":"AC005803.1","2":"transcript","3":"1"},{"1":"AC005808.1","2":"transcript","3":"1"},{"1":"AC005821.1","2":"transcript","3":"1"},{"1":"AC005821.2","2":"transcript","3":"1"},{"1":"AC005823.1","2":"transcript","3":"1"},{"1":"AC005823.2","2":"transcript","3":"1"},{"1":"AC005828.1","2":"transcript","3":"2"},{"1":"AC005828.2","2":"transcript","3":"1"},{"1":"AC005828.3","2":"transcript","3":"1"},{"1":"AC005828.4","2":"transcript","3":"1"},{"1":"AC005828.5","2":"transcript","3":"1"},{"1":"AC005832.1","2":"transcript","3":"1"},{"1":"AC005833.2","2":"transcript","3":"2"},{"1":"AC005837.1","2":"transcript","3":"1"},{"1":"AC005837.3","2":"transcript","3":"1"},{"1":"AC005838.2","2":"transcript","3":"1"},{"1":"AC005840.3","2":"transcript","3":"1"},{"1":"AC005840.5","2":"transcript","3":"1"},{"1":"AC005841.1","2":"transcript","3":"1"},{"1":"AC005842.1","2":"transcript","3":"3"},{"1":"AC005845.1","2":"transcript","3":"1"},{"1":"AC005856.1","2":"transcript","3":"1"},{"1":"AC005863.1","2":"transcript","3":"2"},{"1":"AC005865.1","2":"transcript","3":"2"},{"1":"AC005865.3","2":"transcript","3":"1"},{"1":"AC005871.1","2":"transcript","3":"1"},{"1":"AC005871.2","2":"transcript","3":"1"},{"1":"AC005884.1","2":"transcript","3":"1"},{"1":"AC005884.2","2":"transcript","3":"1"},{"1":"AC005888.1","2":"transcript","3":"1"},{"1":"AC005899.1","2":"transcript","3":"1"},{"1":"AC005899.3","2":"transcript","3":"1"},{"1":"AC005899.4","2":"transcript","3":"1"},{"1":"AC005899.5","2":"transcript","3":"1"},{"1":"AC005899.6","2":"transcript","3":"1"},{"1":"AC005899.7","2":"transcript","3":"1"},{"1":"AC005899.8","2":"transcript","3":"1"},{"1":"AC005901.1","2":"transcript","3":"1"},{"1":"AC005906.2","2":"transcript","3":"14"},{"1":"AC005906.3","2":"transcript","3":"1"},{"1":"AC005908.2","2":"transcript","3":"1"},{"1":"AC005908.3","2":"transcript","3":"1"},{"1":"AC005909.1","2":"transcript","3":"1"},{"1":"AC005909.2","2":"transcript","3":"1"},{"1":"AC005911.1","2":"transcript","3":"1"},{"1":"AC005912.2","2":"transcript","3":"1"},{"1":"AC005914.1","2":"transcript","3":"1"},{"1":"AC005920.1","2":"transcript","3":"1"},{"1":"AC005920.2","2":"transcript","3":"1"},{"1":"AC005920.3","2":"transcript","3":"2"},{"1":"AC005920.4","2":"transcript","3":"1"},{"1":"AC005921.2","2":"transcript","3":"1"},{"1":"AC005921.4","2":"transcript","3":"1"},{"1":"AC005944.1","2":"transcript","3":"1"},{"1":"AC005954.2","2":"transcript","3":"1"},{"1":"AC005954.3","2":"transcript","3":"1"},{"1":"AC005962.1","2":"transcript","3":"1"},{"1":"AC005962.2","2":"transcript","3":"1"},{"1":"AC005972.3","2":"transcript","3":"3"},{"1":"AC005993.1","2":"transcript","3":"3"},{"1":"AC005996.1","2":"transcript","3":"1"},{"1":"AC005998.1","2":"transcript","3":"1"},{"1":"AC005999.1","2":"transcript","3":"1"},{"1":"AC005999.2","2":"transcript","3":"1"},{"1":"AC006001.2","2":"transcript","3":"3"},{"1":"AC006003.1","2":"transcript","3":"1"},{"1":"AC006004.1","2":"transcript","3":"1"},{"1":"AC006007.1","2":"transcript","3":"1"},{"1":"AC006008.1","2":"transcript","3":"1"},{"1":"AC006013.1","2":"transcript","3":"1"},{"1":"AC006017.1","2":"transcript","3":"1"},{"1":"AC006019.1","2":"transcript","3":"4"},{"1":"AC006019.2","2":"transcript","3":"1"},{"1":"AC006019.3","2":"transcript","3":"1"},{"1":"AC006026.3","2":"transcript","3":"1"},{"1":"AC006027.1","2":"transcript","3":"1"},{"1":"AC006033.2","2":"transcript","3":"1"},{"1":"AC006037.1","2":"transcript","3":"1"},{"1":"AC006040.1","2":"transcript","3":"1"},{"1":"AC006041.1","2":"transcript","3":"1"},{"1":"AC006041.2","2":"transcript","3":"1"},{"1":"AC006042.1","2":"transcript","3":"1"},{"1":"AC006042.2","2":"transcript","3":"1"},{"1":"AC006042.4","2":"transcript","3":"1"},{"1":"AC006043.1","2":"transcript","3":"1"},{"1":"AC006055.1","2":"transcript","3":"1"},{"1":"AC006058.1","2":"transcript","3":"1"},{"1":"AC006058.2","2":"transcript","3":"1"},{"1":"AC006058.3","2":"transcript","3":"1"},{"1":"AC006059.1","2":"transcript","3":"2"},{"1":"AC006059.3","2":"transcript","3":"1"},{"1":"AC006059.4","2":"transcript","3":"1"},{"1":"AC006059.5","2":"transcript","3":"1"},{"1":"AC006062.1","2":"transcript","3":"1"},{"1":"AC006063.1","2":"transcript","3":"1"},{"1":"AC006063.2","2":"transcript","3":"1"},{"1":"AC006063.3","2":"transcript","3":"1"},{"1":"AC006063.4","2":"transcript","3":"1"},{"1":"AC006064.1","2":"transcript","3":"1"},{"1":"AC006064.2","2":"transcript","3":"1"},{"1":"AC006064.3","2":"transcript","3":"1"},{"1":"AC006064.4","2":"transcript","3":"1"},{"1":"AC006064.5","2":"transcript","3":"1"},{"1":"AC006065.3","2":"transcript","3":"1"},{"1":"AC006065.4","2":"transcript","3":"2"},{"1":"AC006076.1","2":"transcript","3":"1"},{"1":"AC006111.1","2":"transcript","3":"1"},{"1":"AC006111.2","2":"transcript","3":"1"},{"1":"AC006111.3","2":"transcript","3":"1"},{"1":"AC006112.1","2":"transcript","3":"1"},{"1":"AC006115.2","2":"transcript","3":"8"},{"1":"AC006116.10","2":"transcript","3":"1"},{"1":"AC006116.11","2":"transcript","3":"1"},{"1":"AC006116.4","2":"transcript","3":"1"},{"1":"AC006116.5","2":"transcript","3":"1"},{"1":"AC006116.6","2":"transcript","3":"1"},{"1":"AC006116.8","2":"transcript","3":"4"},{"1":"AC006116.9","2":"transcript","3":"2"},{"1":"AC006130.1","2":"transcript","3":"1"},{"1":"AC006130.3","2":"transcript","3":"1"},{"1":"AC006141.1","2":"transcript","3":"1"},{"1":"AC006144.2","2":"transcript","3":"1"},{"1":"AC006145.1","2":"transcript","3":"1"},{"1":"AC006146.1","2":"transcript","3":"1"},{"1":"AC006148.1","2":"transcript","3":"8"},{"1":"AC006148.2","2":"transcript","3":"1"},{"1":"AC006150.1","2":"transcript","3":"1"},{"1":"AC006153.1","2":"transcript","3":"1"},{"1":"AC006157.1","2":"transcript","3":"1"},{"1":"AC006159.1","2":"transcript","3":"1"},{"1":"AC006159.2","2":"transcript","3":"1"},{"1":"AC006160.1","2":"transcript","3":"1"},{"1":"AC006197.2","2":"transcript","3":"1"},{"1":"AC006205.1","2":"transcript","3":"1"},{"1":"AC006205.2","2":"transcript","3":"1"},{"1":"AC006206.1","2":"transcript","3":"1"},{"1":"AC006206.2","2":"transcript","3":"2"},{"1":"AC006207.1","2":"transcript","3":"1"},{"1":"AC006213.1","2":"transcript","3":"2"},{"1":"AC006213.2","2":"transcript","3":"1"},{"1":"AC006213.3","2":"transcript","3":"1"},{"1":"AC006213.4","2":"transcript","3":"1"},{"1":"AC006213.5","2":"transcript","3":"1"},{"1":"AC006213.7","2":"transcript","3":"1"},{"1":"AC006230.1","2":"transcript","3":"2"},{"1":"AC006237.1","2":"transcript","3":"1"},{"1":"AC006238.1","2":"transcript","3":"1"},{"1":"AC006238.2","2":"transcript","3":"1"},{"1":"AC006249.1","2":"transcript","3":"1"},{"1":"AC006252.1","2":"transcript","3":"1"},{"1":"AC006262.1","2":"transcript","3":"5"},{"1":"AC006262.2","2":"transcript","3":"2"},{"1":"AC006262.3","2":"transcript","3":"1"},{"1":"AC006270.1","2":"transcript","3":"1"},{"1":"AC006272.1","2":"transcript","3":"1"},{"1":"AC006273.1","2":"transcript","3":"1"},{"1":"AC006288.1","2":"transcript","3":"1"},{"1":"AC006296.1","2":"transcript","3":"1"},{"1":"AC006296.2","2":"transcript","3":"1"},{"1":"AC006296.3","2":"transcript","3":"1"},{"1":"AC006299.1","2":"transcript","3":"2"},{"1":"AC006305.1","2":"transcript","3":"1"},{"1":"AC006305.2","2":"transcript","3":"1"},{"1":"AC006305.3","2":"transcript","3":"1"},{"1":"AC006329.1","2":"transcript","3":"2"},{"1":"AC006333.1","2":"transcript","3":"3"},{"1":"AC006333.2","2":"transcript","3":"1"},{"1":"AC006348.1","2":"transcript","3":"1"},{"1":"AC006355.2","2":"transcript","3":"1"},{"1":"AC006357.1","2":"transcript","3":"1"},{"1":"AC006364.1","2":"transcript","3":"1"},{"1":"AC006369.1","2":"transcript","3":"2"},{"1":"AC006369.2","2":"transcript","3":"1"},{"1":"AC006372.1","2":"transcript","3":"2"},{"1":"AC006372.2","2":"transcript","3":"1"},{"1":"AC006372.3","2":"transcript","3":"1"},{"1":"AC006378.1","2":"transcript","3":"1"},{"1":"AC006387.1","2":"transcript","3":"2"},{"1":"AC006398.1","2":"transcript","3":"1"},{"1":"AC006427.2","2":"transcript","3":"1"},{"1":"AC006435.1","2":"transcript","3":"1"},{"1":"AC006435.2","2":"transcript","3":"2"},{"1":"AC006435.3","2":"transcript","3":"1"},{"1":"AC006441.1","2":"transcript","3":"1"},{"1":"AC006441.3","2":"transcript","3":"1"},{"1":"AC006441.4","2":"transcript","3":"2"},{"1":"AC006445.3","2":"transcript","3":"1"},{"1":"AC006449.1","2":"transcript","3":"1"},{"1":"AC006449.2","2":"transcript","3":"1"},{"1":"AC006449.3","2":"transcript","3":"1"},{"1":"AC006449.5","2":"transcript","3":"1"},{"1":"AC006449.6","2":"transcript","3":"1"},{"1":"AC006450.1","2":"transcript","3":"1"},{"1":"AC006450.2","2":"transcript","3":"1"},{"1":"AC006450.3","2":"transcript","3":"1"},{"1":"AC006452.1","2":"transcript","3":"1"},{"1":"AC006455.1","2":"transcript","3":"1"},{"1":"AC006455.4","2":"transcript","3":"1"},{"1":"AC006455.5","2":"transcript","3":"2"},{"1":"AC006455.8","2":"transcript","3":"1"},{"1":"AC006458.1","2":"transcript","3":"1"},{"1":"AC006459.1","2":"transcript","3":"1"},{"1":"AC006460.1","2":"transcript","3":"1"},{"1":"AC006460.2","2":"transcript","3":"3"},{"1":"AC006478.1","2":"transcript","3":"1"},{"1":"AC006478.2","2":"transcript","3":"1"},{"1":"AC006480.2","2":"transcript","3":"1"},{"1":"AC006482.1","2":"transcript","3":"1"},{"1":"AC006483.2","2":"transcript","3":"1"},{"1":"AC006487.1","2":"transcript","3":"1"},{"1":"AC006487.2","2":"transcript","3":"1"},{"1":"AC006504.1","2":"transcript","3":"1"},{"1":"AC006504.2","2":"transcript","3":"1"},{"1":"AC006504.5","2":"transcript","3":"8"},{"1":"AC006504.7","2":"transcript","3":"1"},{"1":"AC006511.4","2":"transcript","3":"1"},{"1":"AC006511.5","2":"transcript","3":"1"},{"1":"AC006511.6","2":"transcript","3":"1"},{"1":"AC006517.2","2":"transcript","3":"1"},{"1":"AC006525.1","2":"transcript","3":"1"},{"1":"AC006538.2","2":"transcript","3":"1"},{"1":"AC006538.4","2":"transcript","3":"1"},{"1":"AC006538.5","2":"transcript","3":"1"},{"1":"AC006547.1","2":"transcript","3":"7"},{"1":"AC006547.2","2":"transcript","3":"1"},{"1":"AC006547.3","2":"transcript","3":"1"},{"1":"AC006557.1","2":"transcript","3":"1"},{"1":"AC006557.3","2":"transcript","3":"1"},{"1":"AC006566.1","2":"transcript","3":"1"},{"1":"AC006566.2","2":"transcript","3":"1"},{"1":"AC006581.1","2":"transcript","3":"1"},{"1":"AC006581.2","2":"transcript","3":"1"},{"1":"AC006942.1","2":"transcript","3":"1"},{"1":"AC006946.2","2":"transcript","3":"1"},{"1":"AC006946.3","2":"transcript","3":"1"},{"1":"AC006947.1","2":"transcript","3":"1"},{"1":"AC006960.2","2":"transcript","3":"1"},{"1":"AC006960.3","2":"transcript","3":"1"},{"1":"AC006967.2","2":"transcript","3":"1"},{"1":"AC006967.3","2":"transcript","3":"1"},{"1":"AC006970.3","2":"transcript","3":"1"},{"1":"AC006972.1","2":"transcript","3":"1"},{"1":"AC006974.1","2":"transcript","3":"1"},{"1":"AC006974.2","2":"transcript","3":"1"},{"1":"AC006994.2","2":"transcript","3":"2"},{"1":"AC007000.3","2":"transcript","3":"1"},{"1":"AC007001.1","2":"transcript","3":"1"},{"1":"AC007003.1","2":"transcript","3":"1"},{"1":"AC007009.1","2":"transcript","3":"1"},{"1":"AC007014.1","2":"transcript","3":"1"},{"1":"AC007014.2","2":"transcript","3":"1"},{"1":"AC007029.1","2":"transcript","3":"1"},{"1":"AC007032.1","2":"transcript","3":"1"},{"1":"AC007036.1","2":"transcript","3":"1"},{"1":"AC007036.2","2":"transcript","3":"1"},{"1":"AC007036.3","2":"transcript","3":"1"},{"1":"AC007036.4","2":"transcript","3":"1"},{"1":"AC007038.1","2":"transcript","3":"1"},{"1":"AC007038.2","2":"transcript","3":"1"},{"1":"AC007040.1","2":"transcript","3":"1"},{"1":"AC007064.2","2":"transcript","3":"1"},{"1":"AC007066.2","2":"transcript","3":"2"},{"1":"AC007066.3","2":"transcript","3":"1"},{"1":"AC007091.1","2":"transcript","3":"2"},{"1":"AC007092.1","2":"transcript","3":"1"},{"1":"AC007098.1","2":"transcript","3":"3"},{"1":"AC007099.1","2":"transcript","3":"1"},{"1":"AC007099.2","2":"transcript","3":"1"},{"1":"AC007100.1","2":"transcript","3":"2"},{"1":"AC007100.2","2":"transcript","3":"1"},{"1":"AC007106.1","2":"transcript","3":"1"},{"1":"AC007106.2","2":"transcript","3":"1"},{"1":"AC007114.1","2":"transcript","3":"2"},{"1":"AC007114.2","2":"transcript","3":"1"},{"1":"AC007126.1","2":"transcript","3":"1"},{"1":"AC007128.1","2":"transcript","3":"1"},{"1":"AC007128.2","2":"transcript","3":"2"},{"1":"AC007130.1","2":"transcript","3":"1"},{"1":"AC007132.1","2":"transcript","3":"1"},{"1":"AC007159.1","2":"transcript","3":"1"},{"1":"AC007161.3","2":"transcript","3":"1"},{"1":"AC007163.1","2":"transcript","3":"1"},{"1":"AC007179.1","2":"transcript","3":"1"},{"1":"AC007179.2","2":"transcript","3":"7"},{"1":"AC007182.1","2":"transcript","3":"1"},{"1":"AC007192.2","2":"transcript","3":"1"},{"1":"AC007193.1","2":"transcript","3":"1"},{"1":"AC007193.2","2":"transcript","3":"1"},{"1":"AC007193.3","2":"transcript","3":"1"},{"1":"AC007216.1","2":"transcript","3":"1"},{"1":"AC007216.2","2":"transcript","3":"1"},{"1":"AC007216.3","2":"transcript","3":"1"},{"1":"AC007216.4","2":"transcript","3":"1"},{"1":"AC007218.1","2":"transcript","3":"1"},{"1":"AC007218.2","2":"transcript","3":"1"},{"1":"AC007218.3","2":"transcript","3":"1"},{"1":"AC007220.1","2":"transcript","3":"1"},{"1":"AC007220.2","2":"transcript","3":"1"},{"1":"AC007221.1","2":"transcript","3":"2"},{"1":"AC007222.1","2":"transcript","3":"1"},{"1":"AC007223.1","2":"transcript","3":"1"},{"1":"AC007240.1","2":"transcript","3":"1"},{"1":"AC007250.1","2":"transcript","3":"1"},{"1":"AC007255.1","2":"transcript","3":"1"},{"1":"AC007262.2","2":"transcript","3":"2"},{"1":"AC007269.1","2":"transcript","3":"1"},{"1":"AC007271.1","2":"transcript","3":"1"},{"1":"AC007272.1","2":"transcript","3":"1"},{"1":"AC007277.1","2":"transcript","3":"1"},{"1":"AC007278.1","2":"transcript","3":"1"},{"1":"AC007278.2","2":"transcript","3":"1"},{"1":"AC007279.2","2":"transcript","3":"1"},{"1":"AC007283.1","2":"transcript","3":"1"},{"1":"AC007285.1","2":"transcript","3":"1"},{"1":"AC007285.2","2":"transcript","3":"1"},{"1":"AC007292.1","2":"transcript","3":"1"},{"1":"AC007292.2","2":"transcript","3":"1"},{"1":"AC007292.3","2":"transcript","3":"1"},{"1":"AC007298.1","2":"transcript","3":"1"},{"1":"AC007298.2","2":"transcript","3":"1"},{"1":"AC007308.1","2":"transcript","3":"1"},{"1":"AC007314.1","2":"transcript","3":"1"},{"1":"AC007317.1","2":"transcript","3":"1"},{"1":"AC007317.2","2":"transcript","3":"1"},{"1":"AC007319.1","2":"transcript","3":"2"},{"1":"AC007326.2","2":"transcript","3":"1"},{"1":"AC007326.5","2":"transcript","3":"1"},{"1":"AC007327.2","2":"transcript","3":"1"},{"1":"AC007333.1","2":"transcript","3":"2"},{"1":"AC007333.2","2":"transcript","3":"1"},{"1":"AC007336.1","2":"transcript","3":"1"},{"1":"AC007336.2","2":"transcript","3":"1"},{"1":"AC007342.1","2":"transcript","3":"1"},{"1":"AC007342.4","2":"transcript","3":"1"},{"1":"AC007342.5","2":"transcript","3":"1"},{"1":"AC007343.1","2":"transcript","3":"1"},{"1":"AC007344.1","2":"transcript","3":"2"},{"1":"AC007347.1","2":"transcript","3":"1"},{"1":"AC007349.1","2":"transcript","3":"1"},{"1":"AC007349.2","2":"transcript","3":"1"},{"1":"AC007349.3","2":"transcript","3":"1"},{"1":"AC007349.4","2":"transcript","3":"1"},{"1":"AC007350.1","2":"transcript","3":"1"},{"1":"AC007359.1","2":"transcript","3":"2"},{"1":"AC007362.1","2":"transcript","3":"1"},{"1":"AC007364.1","2":"transcript","3":"3"},{"1":"AC007365.1","2":"transcript","3":"1"},{"1":"AC007368.1","2":"transcript","3":"5"},{"1":"AC007368.2","2":"transcript","3":"1"},{"1":"AC007370.1","2":"transcript","3":"1"},{"1":"AC007370.2","2":"transcript","3":"1"},{"1":"AC007375.2","2":"transcript","3":"1"},{"1":"AC007376.2","2":"transcript","3":"1"},{"1":"AC007378.1","2":"transcript","3":"1"},{"1":"AC007381.1","2":"transcript","3":"3"},{"1":"AC007381.2","2":"transcript","3":"1"},{"1":"AC007383.2","2":"transcript","3":"2"},{"1":"AC007383.3","2":"transcript","3":"1"},{"1":"AC007384.1","2":"transcript","3":"2"},{"1":"AC007387.2","2":"transcript","3":"1"},{"1":"AC007387.3","2":"transcript","3":"1"},{"1":"AC007389.1","2":"transcript","3":"9"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[16825]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcigncHNldWRvZ2VuZScgJWluJSBnZW5lX3R5cGUpICU+JSBncm91cF9ieShnZW5lX25hbWUsIGZlYXR1cmVfdHlwZSkgJT4lIGNvdW50KClcbmBgYCJ9 -->

```r
d2 %>% filter('pseudogene' %in% gene_type) %>% group_by(gene_name, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjU5MDUwLCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjU5LDA1MCB4IDMiXSwiR3JvdXBzIjpbImdlbmVfbmFtZSwgZmVhdHVyZV90eXBlIFs1OSwwNTBdIl19fSwicmRmIjoiSDRzSUFBQUFBQUFBQnUyY3kzSWN0eFdHaDFkSmpPMjR5cHU4aEZqVHVIUTNsalBEaUZaWnNsa2tGODdLeGNpMG80cE1xU2k2a3V6eVBIbXdWQjRpWlFjQTU0TCt2NkZrU2xTY1ZHRkJzZkgxd2NIQkFYQndhK3I0NEd1NzkvWGVhRFRhR20xdmJJeTJkdUxqYUdkMkZGd1lqYlkzWTJKanREMTZrSDcvTlFwOWxpVkhvMC9qcjMvTlgyeFBtdW5oL1BsK2VuNDRPV2xXNzJhUDVzOWJFL04wL25ndlBoWlNPekg1WkpGNGtCUEYyeVV3aGZoUnMxUmxEeWRQVGhmdmRpY3VKWmVTN3ZETDA2VXRrOG5KNm5sMnNzd1NuNDlXdGt3T0pyT2w4cFI0c2xEK20zbXlNRzR1WVlkSk4wZ09kYTlzbXh4T3BpdDd2aWlrbmg3TVZpK2VIcTFlZkRrcGFuTnNpdWVENHZsa2xlSDRaT1dabURob3lsUnAyTW5CNThzMlRJbWpvNktrazBMajZiU3c3ZlJSOGZ6RlV2ZDA4dVNyTDVkdnBrdWpQNWxNcDVOeE14NlBnL1g3Um1uYnRlMStJOVEyN1JycS9MaHpOOUJWUDVuT0pxc0twOFI0MlRBcDFSd05YcHBCeWc1RXU2TlM2YUFFV3laY21mQmxvaTBUWFpub3kwUlk5dnFVR0F5UzZXeGExbWE2ck0zOTY5U3lPUmRwSzJrM3lEMVFYTm85TGUyZWxuWlBTN3VucGQzVFVDUm1wWm16cFpuWHFlRTdNMGlWcnB5VlRwNE4zcFRHem56aHNKa1hoODBXMXQvTGlTSnE1T1NnaUxJNnM3STZCMld1ZzJhWmE1NjB3NlFiSmt0ZkhwVGxIWlJWT2lpcjlQdXlCbzhHaWJMb1IyWlFuWmdzMVQ4cTFSK1dTZzVMc2NPeTRNUFMyTVBDSDU4WGNTTWx4b1BVNHQzZWRhcG9ndXYzaGRFcE5SMjh0SU9YYmpKUGZqUlBQcHpNL3RBTVJZYjUvYUJ3UHdqTmliU1RZWEk2U0hhTHR3L215YVZMbDhBcWNBcThnbFpCcDZCWEVJWm1pWld6UVpVSERXUEtoQzBUcmt6NE10R1dpWVcyR0tjZkYrMzJ1Rm02SXIweHhiTXRwT3owYUdYcTQ5UGp5ZE5WelhLeTFQS2tmQzVHL3BQSFQ1dEJhdmh1VWQ3V1pQclY2dkY0cGV0NHNwSS9qdFB2S3V3ZFQ3NWVkWWhGdXFqTGFWSHVhVlBNaDlQVDZTQ3h5TE0zbWNYWmEyek5maVBFZzdSS1hLUEVqMEY2SlMxeXRTaXJYYzJtQzlKQmhwcDc1T3IzN1pCMHFHa0h6UjAwOTdDNWg1NGV0ZWlEa21CQmtDdWc3cEZJTFlKcWJocTFNQkVqUkV0dmpMWlhJZ2JFQ3FFZWkxd1d1Wnprc3EzNjBLS1ZMZHJMZHFySGpjV2V4cGgyRFJsWWFNWk85R1JDR1Rza3Z0VmNIcHA5QjVrT01nRXlZWTJNbE43QzVoWTJ0N0M1aGMwdFNtOVJlb3ZTdTdIbTB0WXhZMjBkVTY1Z0Y4UkR4a01HTm5md2M0QTlvVmxETkJjc0RMQXd3SWVST0JBL0lNMVlTMjhhclduVGFMMFNHWmJlR08wL2plblhrR0V1TzFiTnRsSC9KR0pBTElnREdkYlVXcTJwZFdweklsS1d6ZzZSUUEvNkttSkNKT29OaXg1bE5hcEhnbHdhMVNPeElMQW5RRS9RdG5CanJhbEQzMGhFY3pYU0ZvazRJVm92aDc3aDBEZWMwZkdlaU1pZ0xSSlJHUjA3RHZIUUlSNDZYUU1ZMThJL3JmWk0xMnBiT0VRL3B5dUhURlFQUElZNGxvamtRbVJMaERJV1JOcXJSMWs5eWtJZlMwUmx0Qlorck43d3VnYkl4SUJZSWRvM0VwRmNPbmRIb3A3MzZHUGVhZDI5VTY4bUltVTViWGZ2VUpaVGozbkVsa1JFTTNxNDl5akxXK1RTM3BzSVpjU3I2TDJoVng4R1hUR2FFS1JlZHF4eEl4TWp4RURHckpHeFFqeHllZVNpUFVHSjdoUXlFVDNhcHBtb2pJTU1OT3ZJemNTQWRFTGd3dzQrMVBGdXNTK3dZeDNMa2NCbUhjdVJRRTlBV1FIdHBUdUZTS1FmV3F3M01qRWdWb2lXM2pSS1dsMmR4bWxaMjZMVm1Ub1M1TklaTmhLVUJSKzJPaTR5TVVKUU92emM5dXF4UklaNmdzYU5TRlJ6MFBWOEpLbzVhRXl3QVI0TE9qZlowR2tySjZJeTBBTWZJclpZeEpaSVVDK2RaVEt4UWxCVDNkVm1JbnAwSFo2SnlrZ3Q0cUpwRE9KQVdwQ3doaGdRT3lUYTV5TkJXWTFUUFFhNWpJR01oUXcwV3hMVVFsZlVidXhSdXM1ZmtVQ3o5bDQzMW4xM0pOQ2pvenNUcWFtdXJCejJsUTc3U29jZG9zTmV6Mkd2NXhCN002R01CWEVnSHFRRjZZVEFQenFhSEtLb2EzUkZsSWtCR2RyY1dLMXBnOTdTb0c4MHV0dkt4SUk0RUMra2grWmVOZXM2S2hKNFEzY0JrVUN6N2dJeUdlWXlqZFkwRVpFeGFvL0JHRFI2UXBXSkJYRkMxUE9KaUI3ZEMyY2ltdlY4TlJQVkE1c3RiTGF3V2M5Z016RWd6T1ZBaGozQklMWVlYUnRIZ3RaQkpFbEVjdWsrTHBPaERQYm1tVkRHQ3RIU3NYL1BaS2duRG1hUlNVUms5Q1RIT1l3VWg1R1NpQVZ4UWpSS09IamV3Zk5PVDlFelVSbjFzME5VZDRqcWlZak5pUFBZUVVjQ2UzU05IUWw4cUd2c1REU1h4akduYXh1SEhYUjBLaXpVTlVra09pcWRuZzFtTXJUSFl3M2dzUVpJeElJNElkcC92SjdiT095Z00xRVoyS043YW9jOWRTVGE2N0E3emtUMFlBWGlzZDVJUkhMcDZqUVR5b2pIME1wZWR3SE9vNVc5N3BzeTBWd2FSVDNhM2V0T1BCTHRxeTFtbWRaQ1J2ZVZtUmdoV25xclorYVJhRzlKUkdSMEI1UUpaWVoxYitIRFJDUVh4azZyNi9sSVVGUGQxVWFpWGsxa0tOUHAvalFTOVUrSFhVQWlxa2ZqV0tkbnA2N1RQVm9NTmxyVERuRXNFUXZpUUx3UWxnVjdNRkk2dEU2SDF1bmcrUTU5dGRlemdrd01pQVZ4SUI2a0JlbUU2RWpwOWZRcEU3WEh3eDRkQlQxbTZoN3I4RVJVUnZ0UHI3Y3drY0NIV0hrbVlrRWNpSGhNejA0ek1TQ2lHZk5Gai9taXg5Nmh4OHF6eDhvekVRdmlRRHlJdER2MktiMmVEV2FpcGFNdEVFVVQwVnp3dklYbnNhZnVNWi8ybUU5N1BWdk9SR1ZnSVdiR1JBeUkySXlWWG8vNXRNZDgybU1uMVdOSDMyTWQzaVBXOVhvZUZRbnFqdlZoanoxK2p6MStqL1ZocitlMG1WZ1FhVUhFekI0eHM4ZUtzZTlnb1o2WVpXSkF4QjVFNHg3cmxsN1B4eUpCNllqaFBXSjRJbEk2VmpJOVZqSTlWaks5Zms4U0NTelVyMGN5R1dvT09Jc0xtSE1Eb25GQU5FNWthR0hRVytsSWRHNEtpTDJoUVZtTjdxU0NmczBTQ1RRYm5mVUMxdk1CNjdxQTA0T0EwNE9BMDRPQU9COFFud09pWDdDb3FZVlhzZXNQMlBVSHJQbUQzcmxrSXJYUVc1aElVQXRFeUlBSUdSQWhBMDZvQWs2b0F2YlVBZnVVZ0gxS3dENGxFUXNpcllONEdMRFBUVVEwSS9vRnJQQnhxaDhKdklySUZ2UWJHQmR3NWhsdzVobHc1aGx3NW9uN2dreFVEOW9VdTRtQTNVVEFiaUlSTFIxdGluZ1lzUDhLdW9MMXVCM0l4QWd4a0RHUThaRHhhMlFzaUJQU1FrKy9ocWptQUptZ01ob1BJN0VnYmcxUlBVNXIwVGl0aGQ3K1p5SjZOSzVtb2pLd1djOHpQVzQ5TWpFZ0ZzU0JlSkJXQ1BxUHJ2MHlrZEkxcm1aQ0dRdmlRTVJDUGFYSlJEVWJhRVlmMC92S1RBeUk2TkhWYVNhU1M5ZVFIbDhiZXR3S2VkekllOXpJWnlMMjZDN2I0LzdkNDFiSTQwWStFeTJyUTFtZHRvNmVMWHQ4elp1SmFOYXpaWS9iZjQ5Ym9VeFVUNnNXUnFJV0lwTG9xdExqNjBlUHJ4ODl2bldNQkJicWF0QTNpTFFOSWkyK0lvaEV5MHBFYytub2JoQ05HMTJCK0VaM1NaRlFCbVY1bE9VMWtpVGloYWgvY0VzVkNlcmVhbTlKUkVyWDlVWW1ta3ZublFham9ORzcwVWkwdHpTNmNzaEVjbUVVTk9qaGpaN0JSZ0o3MERNYjlNTUdzN25CWEdrd014ck1PN2lQODdpUHk4UUswVm9ZUFcvSmhMbWNFRnFvYzZYUis5eE1xR2ZZNjR6dUN6eHVEQ05CM1RGN0dzeDZSazlPdk1INE12cmR1emVZTHd6bUM2T25HWmxJVFRGeWNZY1lDVnJIbzNVd0tvMmVnV1FpdWZTT0xCUEtXQkFINGtGYUlmQVlZZ0x1UGIzUmZVRWthRUhNY1FhajIyQjBHOTBYUkFKdllKWXhHTGxHei9ralFadGlsakdZWlF3alFFQzlnczdkRmpPUjFYUHNUSXdRdGRCaWJyS1lteXptSm54Ukg0bld3dXBaUVNhaUdWSExJbXBaUksxRUhJZ0hhWVhBWmtRdGk2aGxFYlVzSXBKRlJMSjZrcHlKNnRHWVlQVWtPWk1XcEJPQ2VsbWRCeFBSMGxGM3JQQXRWdVlXSzNPclp5bVpTTDBRVjYyZW5Iajh2VU1rbE5Ib1ovVXJpMHhVUnNleVJUeTBlaWFjaWNnZy9sZzlUZlg0TnNQanU0dElvRWRQRDd4RnRMRjZrNVdKNWtJZlEwU3lpRWhXNy9FOS9rYkRPejJaekVSeTZlMVNKRnFXdzVyTlljM200TlZFUkFaeEhuODdFSW42MEdFdmc2OGpQUDZhd0RzOU44NUV5dEtUNUV3b00yd2RELzk0L1pZbUV5TkV5MHBFWmRTSEhpdEdqNU1Lbi8rZWNmaGZmdXhkWFo1ZHZINTIrZnpWVlNXVlZGSkpKWlZVVWtrbGxWUlNTU1dWVkZKSkpaVlVVa2tsbFZSU1NTV1ZWRkpKSlpWVVVra2xsVlJTU1NXVlZGSkpKWlZVVWtrbGxWUlNTU1dWVkZKSkpaVlVVa2tsbFZSU1NTV1ZWRkpKSlpWVVVra2wvNWRrTlBvNC83OHlvOUZHL05tTVAvZUs1ODM1ODBieHZEMy92WFdEM0VieGZuditPLzA4bVAvZUxQTHF6ODRhblp2eWJsMDVHNkpYeTk0czNtK1BmcGtkMjRVL2R1UFAvVUxQOW1obzUwTHZidUdqUlJuMzUzeWRqOWJWVlgyK1c5UkQ4MndYejR0OEM3bGRrZDhZM1Z4ZnRXZHI3b1BGYy9rN3lYdzAvMzFQN0Zqa3Z6OXY3d2VGekQzUnY2NE4zMmJYMjJUK2wzN1UzaTFodjZUT2QyM0RUV1BucnN1OE4xcHZ3NktmN242Z2NuOE5IOTZWemplVnMyNTgzOFhQdW5aNGt4MDN0ZHU3K0VqajJWMzU5VjExYWJ5OHF6NzZvY2I1aHhoRHQyM1gremZJM0RSWHYyOTV2N2JmeXpsc25jM2JiOG4vcGpYQWJmTzhULzNleFE0dDl6WnRWYTZMM3FmTjMxVG4yK3I1RUg1OVYzdmVSZjVkOC93M2ROOW0vTjlWSGU1eXZPL2NRcSsrZjVleC9MNzFXRGV1M2hhTDFwWDdJV0ptdWYrNmE5MjMvWG5mdG5tYi9HM1gyMXR2ZUNjNnQ2OS9YKy9nZDU2OU9IdjllcFQvVTlnbDNQdjI3T3BzLzd2THN4L09SZnpCNWN1LzdGOUVucko4bkhUK1BmN3o4OC8vL3AzcVhRaDltZzJhNS83Ky9PTDhtL1JtRGo3Njd2enM2c2ZMODIrdS92WnF3VFl1NGo4L2licDdMMTlkUFg5NUVSVnVmamE2SGxXbHdSdVhBbjc3NDBVcTV0dUh6LzcwNDhXZkg0WlVxYmtYUm5QTDQvTS8vN0Y2VGw1TFJXNy9QRmUxTTFlMWUzN3gvZk9MaFhVN0w4NytlUDVpbnZna2VpUFhjLy9WNWZPTDVhRklwSy8zcjE1ZW5TM2s5cDY5ZkxFZ3VXNmpuLzRESDVoaWVxK2JBQUE9In0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"A1BG","2":"transcript","3":"1"},{"1":"A1BG-AS1","2":"transcript","3":"2"},{"1":"A1CF","2":"transcript","3":"7"},{"1":"A2M","2":"transcript","3":"1"},{"1":"A2M-AS1","2":"transcript","3":"2"},{"1":"A2ML1","2":"transcript","3":"2"},{"1":"A2ML1-AS1","2":"transcript","3":"1"},{"1":"A2ML1-AS2","2":"transcript","3":"1"},{"1":"A2MP1","2":"transcript","3":"2"},{"1":"A3GALT2","2":"transcript","3":"1"},{"1":"A4GALT","2":"transcript","3":"4"},{"1":"A4GNT","2":"transcript","3":"1"},{"1":"AAAS","2":"transcript","3":"3"},{"1":"AACS","2":"transcript","3":"1"},{"1":"AACSP1","2":"transcript","3":"2"},{"1":"AADAC","2":"transcript","3":"2"},{"1":"AADACL2","2":"transcript","3":"1"},{"1":"AADACL2-AS1","2":"transcript","3":"1"},{"1":"AADACL3","2":"transcript","3":"1"},{"1":"AADACL4","2":"transcript","3":"1"},{"1":"AADACP1","2":"transcript","3":"3"},{"1":"AADAT","2":"transcript","3":"4"},{"1":"AAGAB","2":"transcript","3":"3"},{"1":"AAK1","2":"transcript","3":"3"},{"1":"AAMDC","2":"transcript","3":"9"},{"1":"AAMP","2":"transcript","3":"3"},{"1":"AANAT","2":"transcript","3":"2"},{"1":"AAR2","2":"transcript","3":"3"},{"1":"AARD","2":"transcript","3":"1"},{"1":"AARS","2":"transcript","3":"1"},{"1":"AARS2","2":"transcript","3":"1"},{"1":"AARSD1","2":"transcript","3":"1"},{"1":"AARSP1","2":"transcript","3":"1"},{"1":"AASDH","2":"transcript","3":"5"},{"1":"AASDHPPT","2":"transcript","3":"1"},{"1":"AASS","2":"transcript","3":"2"},{"1":"AATBC","2":"transcript","3":"2"},{"1":"AATF","2":"transcript","3":"1"},{"1":"AATK","2":"transcript","3":"2"},{"1":"ABALON","2":"transcript","3":"1"},{"1":"ABAT","2":"transcript","3":"5"},{"1":"ABBA01000935.2","2":"transcript","3":"1"},{"1":"ABBA01006766.1","2":"transcript","3":"1"},{"1":"ABBA01031666.1","2":"transcript","3":"1"},{"1":"ABBA01045074.1","2":"transcript","3":"1"},{"1":"ABBA01045074.2","2":"transcript","3":"1"},{"1":"ABCA1","2":"transcript","3":"3"},{"1":"ABCA10","2":"transcript","3":"1"},{"1":"ABCA11P","2":"transcript","3":"2"},{"1":"ABCA12","2":"transcript","3":"3"},{"1":"ABCA13","2":"transcript","3":"1"},{"1":"ABCA17P","2":"transcript","3":"3"},{"1":"ABCA2","2":"transcript","3":"4"},{"1":"ABCA3","2":"transcript","3":"3"},{"1":"ABCA4","2":"transcript","3":"3"},{"1":"ABCA5","2":"transcript","3":"2"},{"1":"ABCA6","2":"transcript","3":"2"},{"1":"ABCA7","2":"transcript","3":"3"},{"1":"ABCA8","2":"transcript","3":"4"},{"1":"ABCA9","2":"transcript","3":"3"},{"1":"ABCA9-AS1","2":"transcript","3":"2"},{"1":"ABCB1","2":"transcript","3":"3"},{"1":"ABCB10","2":"transcript","3":"1"},{"1":"ABCB10P1","2":"transcript","3":"1"},{"1":"ABCB10P3","2":"transcript","3":"1"},{"1":"ABCB10P4","2":"transcript","3":"1"},{"1":"ABCB11","2":"transcript","3":"1"},{"1":"ABCB4","2":"transcript","3":"5"},{"1":"ABCB5","2":"transcript","3":"4"},{"1":"ABCB6","2":"transcript","3":"2"},{"1":"ABCB7","2":"transcript","3":"7"},{"1":"ABCB8","2":"transcript","3":"6"},{"1":"ABCB9","2":"transcript","3":"8"},{"1":"ABCC1","2":"transcript","3":"2"},{"1":"ABCC10","2":"transcript","3":"2"},{"1":"ABCC11","2":"transcript","3":"4"},{"1":"ABCC12","2":"transcript","3":"1"},{"1":"ABCC13","2":"transcript","3":"2"},{"1":"ABCC2","2":"transcript","3":"2"},{"1":"ABCC3","2":"transcript","3":"3"},{"1":"ABCC4","2":"transcript","3":"4"},{"1":"ABCC5","2":"transcript","3":"6"},{"1":"ABCC5-AS1","2":"transcript","3":"1"},{"1":"ABCC6","2":"transcript","3":"4"},{"1":"ABCC6P1","2":"transcript","3":"3"},{"1":"ABCC6P2","2":"transcript","3":"2"},{"1":"ABCC8","2":"transcript","3":"8"},{"1":"ABCC9","2":"transcript","3":"6"},{"1":"ABCD1","2":"transcript","3":"2"},{"1":"ABCD1P2","2":"transcript","3":"1"},{"1":"ABCD1P3","2":"transcript","3":"1"},{"1":"ABCD1P4","2":"transcript","3":"1"},{"1":"ABCD1P5","2":"transcript","3":"1"},{"1":"ABCD2","2":"transcript","3":"1"},{"1":"ABCD3","2":"transcript","3":"2"},{"1":"ABCD4","2":"transcript","3":"2"},{"1":"ABCE1","2":"transcript","3":"1"},{"1":"ABCF1","2":"transcript","3":"2"},{"1":"ABCF2","2":"transcript","3":"2"},{"1":"ABCF2P1","2":"transcript","3":"1"},{"1":"ABCF2P2","2":"transcript","3":"1"},{"1":"ABCF3","2":"transcript","3":"2"},{"1":"ABCG1","2":"transcript","3":"6"},{"1":"ABCG2","2":"transcript","3":"3"},{"1":"ABCG4","2":"transcript","3":"3"},{"1":"ABCG5","2":"transcript","3":"1"},{"1":"ABCG8","2":"transcript","3":"1"},{"1":"ABHD1","2":"transcript","3":"2"},{"1":"ABHD10","2":"transcript","3":"2"},{"1":"ABHD11","2":"transcript","3":"4"},{"1":"ABHD11-AS1","2":"transcript","3":"2"},{"1":"ABHD12","2":"transcript","3":"2"},{"1":"ABHD12B","2":"transcript","3":"2"},{"1":"ABHD13","2":"transcript","3":"1"},{"1":"ABHD14A","2":"transcript","3":"3"},{"1":"ABHD14A-ACY1","2":"transcript","3":"1"},{"1":"ABHD14B","2":"transcript","3":"6"},{"1":"ABHD15","2":"transcript","3":"1"},{"1":"ABHD15-AS1","2":"transcript","3":"1"},{"1":"ABHD16A","2":"transcript","3":"2"},{"1":"ABHD16B","2":"transcript","3":"1"},{"1":"ABHD17A","2":"transcript","3":"3"},{"1":"ABHD17AP1","2":"transcript","3":"1"},{"1":"ABHD17AP3","2":"transcript","3":"1"},{"1":"ABHD17AP4","2":"transcript","3":"1"},{"1":"ABHD17AP5","2":"transcript","3":"1"},{"1":"ABHD17AP6","2":"transcript","3":"1"},{"1":"ABHD17AP7","2":"transcript","3":"1"},{"1":"ABHD17AP8","2":"transcript","3":"1"},{"1":"ABHD17AP9","2":"transcript","3":"1"},{"1":"ABHD17B","2":"transcript","3":"2"},{"1":"ABHD17C","2":"transcript","3":"3"},{"1":"ABHD18","2":"transcript","3":"5"},{"1":"ABHD2","2":"transcript","3":"2"},{"1":"ABHD3","2":"transcript","3":"3"},{"1":"ABHD4","2":"transcript","3":"2"},{"1":"ABHD5","2":"transcript","3":"3"},{"1":"ABHD6","2":"transcript","3":"2"},{"1":"ABHD8","2":"transcript","3":"1"},{"1":"ABI1","2":"transcript","3":"12"},{"1":"ABI1P1","2":"transcript","3":"1"},{"1":"ABI2","2":"transcript","3":"7"},{"1":"ABI3","2":"transcript","3":"2"},{"1":"ABI3BP","2":"transcript","3":"4"},{"1":"ABITRAM","2":"transcript","3":"2"},{"1":"ABITRAMP1","2":"transcript","3":"1"},{"1":"ABL1","2":"transcript","3":"2"},{"1":"ABL2","2":"transcript","3":"8"},{"1":"ABLIM1","2":"transcript","3":"9"},{"1":"ABLIM2","2":"transcript","3":"9"},{"1":"ABLIM3","2":"transcript","3":"7"},{"1":"ABO","2":"transcript","3":"2"},{"1":"ABR","2":"transcript","3":"7"},{"1":"ABRA","2":"transcript","3":"1"},{"1":"ABRACL","2":"transcript","3":"2"},{"1":"ABRAXAS1","2":"transcript","3":"3"},{"1":"ABRAXAS2","2":"transcript","3":"1"},{"1":"ABT1","2":"transcript","3":"1"},{"1":"ABT1P1","2":"transcript","3":"1"},{"1":"ABTB1","2":"transcript","3":"3"},{"1":"ABTB2","2":"transcript","3":"1"},{"1":"AC000032.1","2":"transcript","3":"1"},{"1":"AC000035.1","2":"transcript","3":"1"},{"1":"AC000036.1","2":"transcript","3":"1"},{"1":"AC000041.1","2":"transcript","3":"1"},{"1":"AC000050.1","2":"transcript","3":"1"},{"1":"AC000058.1","2":"transcript","3":"1"},{"1":"AC000061.1","2":"transcript","3":"1"},{"1":"AC000065.1","2":"transcript","3":"2"},{"1":"AC000065.2","2":"transcript","3":"1"},{"1":"AC000067.1","2":"transcript","3":"1"},{"1":"AC000068.1","2":"transcript","3":"1"},{"1":"AC000068.2","2":"transcript","3":"1"},{"1":"AC000068.3","2":"transcript","3":"1"},{"1":"AC000072.1","2":"transcript","3":"1"},{"1":"AC000077.1","2":"transcript","3":"1"},{"1":"AC000078.1","2":"transcript","3":"1"},{"1":"AC000081.1","2":"transcript","3":"1"},{"1":"AC000082.1","2":"transcript","3":"1"},{"1":"AC000085.1","2":"transcript","3":"1"},{"1":"AC000089.1","2":"transcript","3":"1"},{"1":"AC000093.1","2":"transcript","3":"1"},{"1":"AC000095.1","2":"transcript","3":"1"},{"1":"AC000095.2","2":"transcript","3":"1"},{"1":"AC000095.3","2":"transcript","3":"1"},{"1":"AC000099.1","2":"transcript","3":"1"},{"1":"AC000111.1","2":"transcript","3":"1"},{"1":"AC000111.2","2":"transcript","3":"1"},{"1":"AC000113.1","2":"transcript","3":"1"},{"1":"AC000120.1","2":"transcript","3":"1"},{"1":"AC000120.2","2":"transcript","3":"1"},{"1":"AC000120.3","2":"transcript","3":"1"},{"1":"AC000123.1","2":"transcript","3":"1"},{"1":"AC000123.2","2":"transcript","3":"1"},{"1":"AC000123.3","2":"transcript","3":"1"},{"1":"AC000124.1","2":"transcript","3":"1"},{"1":"AC000362.1","2":"transcript","3":"1"},{"1":"AC000367.1","2":"transcript","3":"1"},{"1":"AC000372.1","2":"transcript","3":"1"},{"1":"AC000374.1","2":"transcript","3":"1"},{"1":"AC000403.1","2":"transcript","3":"1"},{"1":"AC001226.1","2":"transcript","3":"1"},{"1":"AC001226.2","2":"transcript","3":"1"},{"1":"AC002044.1","2":"transcript","3":"1"},{"1":"AC002044.2","2":"transcript","3":"1"},{"1":"AC002044.3","2":"transcript","3":"1"},{"1":"AC002056.1","2":"transcript","3":"1"},{"1":"AC002056.2","2":"transcript","3":"1"},{"1":"AC002057.1","2":"transcript","3":"1"},{"1":"AC002057.2","2":"transcript","3":"1"},{"1":"AC002059.1","2":"transcript","3":"1"},{"1":"AC002059.2","2":"transcript","3":"1"},{"1":"AC002059.3","2":"transcript","3":"2"},{"1":"AC002064.1","2":"transcript","3":"1"},{"1":"AC002064.2","2":"transcript","3":"1"},{"1":"AC002064.3","2":"transcript","3":"1"},{"1":"AC002066.1","2":"transcript","3":"3"},{"1":"AC002069.1","2":"transcript","3":"2"},{"1":"AC002069.2","2":"transcript","3":"1"},{"1":"AC002069.3","2":"transcript","3":"1"},{"1":"AC002070.1","2":"transcript","3":"3"},{"1":"AC002072.1","2":"transcript","3":"1"},{"1":"AC002074.1","2":"transcript","3":"1"},{"1":"AC002074.2","2":"transcript","3":"1"},{"1":"AC002075.1","2":"transcript","3":"1"},{"1":"AC002075.2","2":"transcript","3":"1"},{"1":"AC002076.1","2":"transcript","3":"1"},{"1":"AC002076.2","2":"transcript","3":"1"},{"1":"AC002090.1","2":"transcript","3":"1"},{"1":"AC002091.1","2":"transcript","3":"1"},{"1":"AC002091.2","2":"transcript","3":"1"},{"1":"AC002094.1","2":"transcript","3":"1"},{"1":"AC002094.2","2":"transcript","3":"1"},{"1":"AC002094.3","2":"transcript","3":"1"},{"1":"AC002094.4","2":"transcript","3":"1"},{"1":"AC002094.5","2":"transcript","3":"1"},{"1":"AC002101.1","2":"transcript","3":"1"},{"1":"AC002115.1","2":"transcript","3":"1"},{"1":"AC002116.1","2":"transcript","3":"1"},{"1":"AC002116.2","2":"transcript","3":"1"},{"1":"AC002127.1","2":"transcript","3":"1"},{"1":"AC002128.1","2":"transcript","3":"1"},{"1":"AC002128.2","2":"transcript","3":"1"},{"1":"AC002306.1","2":"transcript","3":"1"},{"1":"AC002310.1","2":"transcript","3":"1"},{"1":"AC002310.2","2":"transcript","3":"2"},{"1":"AC002310.3","2":"transcript","3":"1"},{"1":"AC002310.4","2":"transcript","3":"1"},{"1":"AC002310.5","2":"transcript","3":"1"},{"1":"AC002331.1","2":"transcript","3":"1"},{"1":"AC002347.1","2":"transcript","3":"1"},{"1":"AC002347.2","2":"transcript","3":"1"},{"1":"AC002350.1","2":"transcript","3":"1"},{"1":"AC002351.1","2":"transcript","3":"3"},{"1":"AC002366.1","2":"transcript","3":"1"},{"1":"AC002367.1","2":"transcript","3":"1"},{"1":"AC002368.1","2":"transcript","3":"1"},{"1":"AC002375.1","2":"transcript","3":"1"},{"1":"AC002377.1","2":"transcript","3":"1"},{"1":"AC002378.1","2":"transcript","3":"1"},{"1":"AC002381.1","2":"transcript","3":"1"},{"1":"AC002383.1","2":"transcript","3":"1"},{"1":"AC002386.1","2":"transcript","3":"1"},{"1":"AC002398.1","2":"transcript","3":"1"},{"1":"AC002398.2","2":"transcript","3":"1"},{"1":"AC002400.1","2":"transcript","3":"1"},{"1":"AC002401.1","2":"transcript","3":"1"},{"1":"AC002401.2","2":"transcript","3":"1"},{"1":"AC002401.3","2":"transcript","3":"1"},{"1":"AC002401.4","2":"transcript","3":"1"},{"1":"AC002407.1","2":"transcript","3":"1"},{"1":"AC002428.1","2":"transcript","3":"1"},{"1":"AC002428.2","2":"transcript","3":"1"},{"1":"AC002429.1","2":"transcript","3":"1"},{"1":"AC002429.2","2":"transcript","3":"2"},{"1":"AC002451.1","2":"transcript","3":"7"},{"1":"AC002451.2","2":"transcript","3":"1"},{"1":"AC002454.1","2":"transcript","3":"1"},{"1":"AC002456.1","2":"transcript","3":"2"},{"1":"AC002456.2","2":"transcript","3":"1"},{"1":"AC002458.1","2":"transcript","3":"1"},{"1":"AC002460.1","2":"transcript","3":"2"},{"1":"AC002460.2","2":"transcript","3":"2"},{"1":"AC002463.1","2":"transcript","3":"6"},{"1":"AC002464.1","2":"transcript","3":"1"},{"1":"AC002465.1","2":"transcript","3":"1"},{"1":"AC002465.2","2":"transcript","3":"1"},{"1":"AC002467.1","2":"transcript","3":"1"},{"1":"AC002470.1","2":"transcript","3":"1"},{"1":"AC002470.2","2":"transcript","3":"1"},{"1":"AC002472.1","2":"transcript","3":"1"},{"1":"AC002472.2","2":"transcript","3":"1"},{"1":"AC002472.3","2":"transcript","3":"1"},{"1":"AC002472.4","2":"transcript","3":"1"},{"1":"AC002480.1","2":"transcript","3":"1"},{"1":"AC002480.2","2":"transcript","3":"1"},{"1":"AC002486.1","2":"transcript","3":"1"},{"1":"AC002486.2","2":"transcript","3":"1"},{"1":"AC002487.1","2":"transcript","3":"1"},{"1":"AC002504.1","2":"transcript","3":"1"},{"1":"AC002511.1","2":"transcript","3":"1"},{"1":"AC002511.2","2":"transcript","3":"1"},{"1":"AC002511.3","2":"transcript","3":"1"},{"1":"AC002519.1","2":"transcript","3":"1"},{"1":"AC002519.2","2":"transcript","3":"2"},{"1":"AC002524.1","2":"transcript","3":"1"},{"1":"AC002525.1","2":"transcript","3":"1"},{"1":"AC002529.1","2":"transcript","3":"1"},{"1":"AC002540.1","2":"transcript","3":"1"},{"1":"AC002542.1","2":"transcript","3":"1"},{"1":"AC002542.2","2":"transcript","3":"1"},{"1":"AC002543.1","2":"transcript","3":"1"},{"1":"AC002545.1","2":"transcript","3":"1"},{"1":"AC002546.1","2":"transcript","3":"1"},{"1":"AC002550.1","2":"transcript","3":"1"},{"1":"AC002550.2","2":"transcript","3":"1"},{"1":"AC002551.1","2":"transcript","3":"1"},{"1":"AC002553.1","2":"transcript","3":"1"},{"1":"AC002553.2","2":"transcript","3":"1"},{"1":"AC002558.1","2":"transcript","3":"1"},{"1":"AC002558.2","2":"transcript","3":"1"},{"1":"AC002558.3","2":"transcript","3":"1"},{"1":"AC002563.1","2":"transcript","3":"1"},{"1":"AC002984.1","2":"transcript","3":"1"},{"1":"AC002985.1","2":"transcript","3":"1"},{"1":"AC002996.1","2":"transcript","3":"1"},{"1":"AC003001.1","2":"transcript","3":"2"},{"1":"AC003001.2","2":"transcript","3":"1"},{"1":"AC003002.1","2":"transcript","3":"1"},{"1":"AC003002.2","2":"transcript","3":"1"},{"1":"AC003002.3","2":"transcript","3":"1"},{"1":"AC003005.1","2":"transcript","3":"1"},{"1":"AC003005.2","2":"transcript","3":"1"},{"1":"AC003006.1","2":"transcript","3":"1"},{"1":"AC003009.1","2":"transcript","3":"1"},{"1":"AC003035.1","2":"transcript","3":"1"},{"1":"AC003035.2","2":"transcript","3":"1"},{"1":"AC003043.1","2":"transcript","3":"1"},{"1":"AC003043.2","2":"transcript","3":"1"},{"1":"AC003044.1","2":"transcript","3":"1"},{"1":"AC003045.1","2":"transcript","3":"1"},{"1":"AC003070.1","2":"transcript","3":"1"},{"1":"AC003070.2","2":"transcript","3":"1"},{"1":"AC003070.7","2":"transcript","3":"1"},{"1":"AC003071.1","2":"transcript","3":"1"},{"1":"AC003071.2","2":"transcript","3":"1"},{"1":"AC003072.1","2":"transcript","3":"1"},{"1":"AC003077.1","2":"transcript","3":"2"},{"1":"AC003080.1","2":"transcript","3":"1"},{"1":"AC003084.1","2":"transcript","3":"1"},{"1":"AC003086.1","2":"transcript","3":"1"},{"1":"AC003087.1","2":"transcript","3":"1"},{"1":"AC003092.1","2":"transcript","3":"2"},{"1":"AC003092.2","2":"transcript","3":"1"},{"1":"AC003093.1","2":"transcript","3":"1"},{"1":"AC003098.1","2":"transcript","3":"1"},{"1":"AC003101.1","2":"transcript","3":"1"},{"1":"AC003101.2","2":"transcript","3":"1"},{"1":"AC003101.3","2":"transcript","3":"1"},{"1":"AC003102.1","2":"transcript","3":"2"},{"1":"AC003112.1","2":"transcript","3":"2"},{"1":"AC003659.1","2":"transcript","3":"1"},{"1":"AC003665.1","2":"transcript","3":"3"},{"1":"AC003666.1","2":"transcript","3":"1"},{"1":"AC003669.1","2":"transcript","3":"1"},{"1":"AC003681.1","2":"transcript","3":"1"},{"1":"AC003682.1","2":"transcript","3":"1"},{"1":"AC003684.1","2":"transcript","3":"1"},{"1":"AC003685.1","2":"transcript","3":"1"},{"1":"AC003685.2","2":"transcript","3":"1"},{"1":"AC003686.1","2":"transcript","3":"1"},{"1":"AC003687.1","2":"transcript","3":"1"},{"1":"AC003688.1","2":"transcript","3":"1"},{"1":"AC003688.2","2":"transcript","3":"1"},{"1":"AC003950.1","2":"transcript","3":"1"},{"1":"AC003956.1","2":"transcript","3":"1"},{"1":"AC003957.1","2":"transcript","3":"1"},{"1":"AC003958.1","2":"transcript","3":"1"},{"1":"AC003958.2","2":"transcript","3":"1"},{"1":"AC003965.1","2":"transcript","3":"1"},{"1":"AC003965.2","2":"transcript","3":"1"},{"1":"AC003973.1","2":"transcript","3":"6"},{"1":"AC003973.2","2":"transcript","3":"1"},{"1":"AC003975.1","2":"transcript","3":"1"},{"1":"AC003982.1","2":"transcript","3":"1"},{"1":"AC003984.1","2":"transcript","3":"1"},{"1":"AC003985.1","2":"transcript","3":"2"},{"1":"AC003986.1","2":"transcript","3":"1"},{"1":"AC003986.2","2":"transcript","3":"1"},{"1":"AC003986.3","2":"transcript","3":"1"},{"1":"AC003988.1","2":"transcript","3":"1"},{"1":"AC003989.1","2":"transcript","3":"1"},{"1":"AC003989.2","2":"transcript","3":"1"},{"1":"AC003991.1","2":"transcript","3":"6"},{"1":"AC003991.2","2":"transcript","3":"1"},{"1":"AC003992.1","2":"transcript","3":"1"},{"1":"AC004000.1","2":"transcript","3":"1"},{"1":"AC004004.1","2":"transcript","3":"1"},{"1":"AC004006.1","2":"transcript","3":"1"},{"1":"AC004009.1","2":"transcript","3":"2"},{"1":"AC004009.2","2":"transcript","3":"1"},{"1":"AC004009.3","2":"transcript","3":"1"},{"1":"AC004012.1","2":"transcript","3":"1"},{"1":"AC004014.1","2":"transcript","3":"1"},{"1":"AC004014.2","2":"transcript","3":"1"},{"1":"AC004022.1","2":"transcript","3":"1"},{"1":"AC004022.2","2":"transcript","3":"1"},{"1":"AC004023.1","2":"transcript","3":"1"},{"1":"AC004024.1","2":"transcript","3":"1"},{"1":"AC004034.1","2":"transcript","3":"1"},{"1":"AC004039.1","2":"transcript","3":"1"},{"1":"AC004047.1","2":"transcript","3":"1"},{"1":"AC004052.1","2":"transcript","3":"3"},{"1":"AC004053.1","2":"transcript","3":"1"},{"1":"AC004054.1","2":"transcript","3":"1"},{"1":"AC004057.1","2":"transcript","3":"2"},{"1":"AC004062.1","2":"transcript","3":"1"},{"1":"AC004063.1","2":"transcript","3":"1"},{"1":"AC004066.1","2":"transcript","3":"1"},{"1":"AC004066.2","2":"transcript","3":"1"},{"1":"AC004067.1","2":"transcript","3":"1"},{"1":"AC004069.1","2":"transcript","3":"1"},{"1":"AC004069.2","2":"transcript","3":"1"},{"1":"AC004074.1","2":"transcript","3":"1"},{"1":"AC004076.1","2":"transcript","3":"1"},{"1":"AC004076.2","2":"transcript","3":"1"},{"1":"AC004080.1","2":"transcript","3":"1"},{"1":"AC004080.2","2":"transcript","3":"1"},{"1":"AC004080.3","2":"transcript","3":"1"},{"1":"AC004080.4","2":"transcript","3":"1"},{"1":"AC004080.5","2":"transcript","3":"1"},{"1":"AC004080.6","2":"transcript","3":"1"},{"1":"AC004080.7","2":"transcript","3":"1"},{"1":"AC004083.1","2":"transcript","3":"2"},{"1":"AC004086.1","2":"transcript","3":"2"},{"1":"AC004112.1","2":"transcript","3":"1"},{"1":"AC004129.1","2":"transcript","3":"1"},{"1":"AC004129.2","2":"transcript","3":"1"},{"1":"AC004129.3","2":"transcript","3":"1"},{"1":"AC004130.1","2":"transcript","3":"1"},{"1":"AC004134.1","2":"transcript","3":"1"},{"1":"AC004147.1","2":"transcript","3":"1"},{"1":"AC004147.2","2":"transcript","3":"1"},{"1":"AC004147.3","2":"transcript","3":"1"},{"1":"AC004147.4","2":"transcript","3":"1"},{"1":"AC004147.5","2":"transcript","3":"1"},{"1":"AC004148.1","2":"transcript","3":"1"},{"1":"AC004148.2","2":"transcript","3":"1"},{"1":"AC004151.1","2":"transcript","3":"6"},{"1":"AC004156.1","2":"transcript","3":"1"},{"1":"AC004156.2","2":"transcript","3":"1"},{"1":"AC004158.1","2":"transcript","3":"2"},{"1":"AC004160.1","2":"transcript","3":"6"},{"1":"AC004160.2","2":"transcript","3":"1"},{"1":"AC004217.1","2":"transcript","3":"1"},{"1":"AC004217.2","2":"transcript","3":"1"},{"1":"AC004221.1","2":"transcript","3":"1"},{"1":"AC004223.1","2":"transcript","3":"1"},{"1":"AC004223.2","2":"transcript","3":"1"},{"1":"AC004223.3","2":"transcript","3":"1"},{"1":"AC004223.4","2":"transcript","3":"1"},{"1":"AC004224.1","2":"transcript","3":"1"},{"1":"AC004224.2","2":"transcript","3":"1"},{"1":"AC004231.1","2":"transcript","3":"1"},{"1":"AC004231.3","2":"transcript","3":"1"},{"1":"AC004232.1","2":"transcript","3":"1"},{"1":"AC004232.2","2":"transcript","3":"1"},{"1":"AC004233.1","2":"transcript","3":"1"},{"1":"AC004233.2","2":"transcript","3":"1"},{"1":"AC004233.3","2":"transcript","3":"1"},{"1":"AC004241.1","2":"transcript","3":"3"},{"1":"AC004241.2","2":"transcript","3":"1"},{"1":"AC004241.3","2":"transcript","3":"1"},{"1":"AC004241.4","2":"transcript","3":"1"},{"1":"AC004241.5","2":"transcript","3":"1"},{"1":"AC004253.1","2":"transcript","3":"1"},{"1":"AC004253.2","2":"transcript","3":"1"},{"1":"AC004257.1","2":"transcript","3":"1"},{"1":"AC004263.1","2":"transcript","3":"1"},{"1":"AC004263.2","2":"transcript","3":"1"},{"1":"AC004264.1","2":"transcript","3":"1"},{"1":"AC004264.2","2":"transcript","3":"1"},{"1":"AC004381.1","2":"transcript","3":"1"},{"1":"AC004381.2","2":"transcript","3":"1"},{"1":"AC004381.3","2":"transcript","3":"1"},{"1":"AC004383.1","2":"transcript","3":"1"},{"1":"AC004386.1","2":"transcript","3":"1"},{"1":"AC004386.2","2":"transcript","3":"1"},{"1":"AC004408.1","2":"transcript","3":"1"},{"1":"AC004408.2","2":"transcript","3":"1"},{"1":"AC004415.1","2":"transcript","3":"1"},{"1":"AC004448.1","2":"transcript","3":"1"},{"1":"AC004448.2","2":"transcript","3":"6"},{"1":"AC004448.3","2":"transcript","3":"1"},{"1":"AC004448.4","2":"transcript","3":"1"},{"1":"AC004449.1","2":"transcript","3":"1"},{"1":"AC004453.1","2":"transcript","3":"1"},{"1":"AC004453.2","2":"transcript","3":"1"},{"1":"AC004461.1","2":"transcript","3":"1"},{"1":"AC004461.2","2":"transcript","3":"1"},{"1":"AC004464.1","2":"transcript","3":"1"},{"1":"AC004466.1","2":"transcript","3":"1"},{"1":"AC004466.2","2":"transcript","3":"1"},{"1":"AC004466.3","2":"transcript","3":"1"},{"1":"AC004467.1","2":"transcript","3":"1"},{"1":"AC004470.1","2":"transcript","3":"1"},{"1":"AC004471.1","2":"transcript","3":"1"},{"1":"AC004471.2","2":"transcript","3":"1"},{"1":"AC004475.1","2":"transcript","3":"1"},{"1":"AC004477.1","2":"transcript","3":"1"},{"1":"AC004477.2","2":"transcript","3":"1"},{"1":"AC004477.3","2":"transcript","3":"1"},{"1":"AC004485.1","2":"transcript","3":"1"},{"1":"AC004486.1","2":"transcript","3":"1"},{"1":"AC004490.1","2":"transcript","3":"1"},{"1":"AC004491.1","2":"transcript","3":"1"},{"1":"AC004492.1","2":"transcript","3":"1"},{"1":"AC004494.1","2":"transcript","3":"2"},{"1":"AC004494.2","2":"transcript","3":"1"},{"1":"AC004522.1","2":"transcript","3":"1"},{"1":"AC004522.2","2":"transcript","3":"1"},{"1":"AC004522.3","2":"transcript","3":"1"},{"1":"AC004522.4","2":"transcript","3":"1"},{"1":"AC004528.1","2":"transcript","3":"1"},{"1":"AC004528.2","2":"transcript","3":"1"},{"1":"AC004540.1","2":"transcript","3":"8"},{"1":"AC004540.2","2":"transcript","3":"2"},{"1":"AC004542.1","2":"transcript","3":"1"},{"1":"AC004542.2","2":"transcript","3":"1"},{"1":"AC004543.1","2":"transcript","3":"1"},{"1":"AC004549.1","2":"transcript","3":"1"},{"1":"AC004551.1","2":"transcript","3":"5"},{"1":"AC004551.2","2":"transcript","3":"1"},{"1":"AC004552.1","2":"transcript","3":"1"},{"1":"AC004554.1","2":"transcript","3":"1"},{"1":"AC004554.2","2":"transcript","3":"1"},{"1":"AC004584.1","2":"transcript","3":"1"},{"1":"AC004584.2","2":"transcript","3":"1"},{"1":"AC004584.3","2":"transcript","3":"1"},{"1":"AC004585.1","2":"transcript","3":"1"},{"1":"AC004585.2","2":"transcript","3":"1"},{"1":"AC004590.1","2":"transcript","3":"1"},{"1":"AC004593.1","2":"transcript","3":"1"},{"1":"AC004593.2","2":"transcript","3":"1"},{"1":"AC004593.3","2":"transcript","3":"1"},{"1":"AC004594.1","2":"transcript","3":"2"},{"1":"AC004596.1","2":"transcript","3":"1"},{"1":"AC004597.1","2":"transcript","3":"1"},{"1":"AC004623.1","2":"transcript","3":"1"},{"1":"AC004637.1","2":"transcript","3":"1"},{"1":"AC004672.1","2":"transcript","3":"1"},{"1":"AC004672.2","2":"transcript","3":"1"},{"1":"AC004674.1","2":"transcript","3":"1"},{"1":"AC004674.2","2":"transcript","3":"1"},{"1":"AC004678.1","2":"transcript","3":"1"},{"1":"AC004678.2","2":"transcript","3":"1"},{"1":"AC004687.1","2":"transcript","3":"1"},{"1":"AC004687.2","2":"transcript","3":"1"},{"1":"AC004687.3","2":"transcript","3":"1"},{"1":"AC004690.1","2":"transcript","3":"1"},{"1":"AC004690.2","2":"transcript","3":"1"},{"1":"AC004691.1","2":"transcript","3":"1"},{"1":"AC004691.2","2":"transcript","3":"1"},{"1":"AC004692.1","2":"transcript","3":"1"},{"1":"AC004692.2","2":"transcript","3":"3"},{"1":"AC004696.1","2":"transcript","3":"1"},{"1":"AC004696.2","2":"transcript","3":"1"},{"1":"AC004702.1","2":"transcript","3":"1"},{"1":"AC004704.1","2":"transcript","3":"1"},{"1":"AC004706.1","2":"transcript","3":"1"},{"1":"AC004706.2","2":"transcript","3":"1"},{"1":"AC004706.3","2":"transcript","3":"1"},{"1":"AC004707.1","2":"transcript","3":"1"},{"1":"AC004765.1","2":"transcript","3":"1"},{"1":"AC004771.1","2":"transcript","3":"1"},{"1":"AC004771.2","2":"transcript","3":"1"},{"1":"AC004771.3","2":"transcript","3":"1"},{"1":"AC004771.4","2":"transcript","3":"1"},{"1":"AC004771.5","2":"transcript","3":"1"},{"1":"AC004775.1","2":"transcript","3":"1"},{"1":"AC004777.1","2":"transcript","3":"1"},{"1":"AC004784.1","2":"transcript","3":"1"},{"1":"AC004790.1","2":"transcript","3":"1"},{"1":"AC004790.2","2":"transcript","3":"1"},{"1":"AC004792.1","2":"transcript","3":"1"},{"1":"AC004797.1","2":"transcript","3":"7"},{"1":"AC004801.1","2":"transcript","3":"1"},{"1":"AC004801.2","2":"transcript","3":"2"},{"1":"AC004801.3","2":"transcript","3":"1"},{"1":"AC004801.4","2":"transcript","3":"1"},{"1":"AC004801.5","2":"transcript","3":"2"},{"1":"AC004801.6","2":"transcript","3":"1"},{"1":"AC004801.7","2":"transcript","3":"1"},{"1":"AC004803.1","2":"transcript","3":"4"},{"1":"AC004805.1","2":"transcript","3":"1"},{"1":"AC004805.2","2":"transcript","3":"1"},{"1":"AC004805.3","2":"transcript","3":"1"},{"1":"AC004808.1","2":"transcript","3":"1"},{"1":"AC004808.2","2":"transcript","3":"1"},{"1":"AC004812.1","2":"transcript","3":"1"},{"1":"AC004812.2","2":"transcript","3":"1"},{"1":"AC004816.1","2":"transcript","3":"2"},{"1":"AC004816.2","2":"transcript","3":"1"},{"1":"AC004817.1","2":"transcript","3":"1"},{"1":"AC004817.2","2":"transcript","3":"1"},{"1":"AC004817.3","2":"transcript","3":"1"},{"1":"AC004817.4","2":"transcript","3":"1"},{"1":"AC004817.5","2":"transcript","3":"2"},{"1":"AC004825.1","2":"transcript","3":"1"},{"1":"AC004825.2","2":"transcript","3":"1"},{"1":"AC004825.3","2":"transcript","3":"1"},{"1":"AC004828.1","2":"transcript","3":"1"},{"1":"AC004828.2","2":"transcript","3":"1"},{"1":"AC004830.1","2":"transcript","3":"1"},{"1":"AC004832.1","2":"transcript","3":"2"},{"1":"AC004832.2","2":"transcript","3":"1"},{"1":"AC004832.3","2":"transcript","3":"1"},{"1":"AC004832.4","2":"transcript","3":"1"},{"1":"AC004832.5","2":"transcript","3":"1"},{"1":"AC004832.6","2":"transcript","3":"1"},{"1":"AC004834.1","2":"transcript","3":"3"},{"1":"AC004835.1","2":"transcript","3":"1"},{"1":"AC004835.2","2":"transcript","3":"1"},{"1":"AC004836.1","2":"transcript","3":"1"},{"1":"AC004837.1","2":"transcript","3":"1"},{"1":"AC004837.2","2":"transcript","3":"1"},{"1":"AC004837.3","2":"transcript","3":"1"},{"1":"AC004837.4","2":"transcript","3":"1"},{"1":"AC004839.1","2":"transcript","3":"1"},{"1":"AC004840.1","2":"transcript","3":"1"},{"1":"AC004840.2","2":"transcript","3":"1"},{"1":"AC004846.1","2":"transcript","3":"1"},{"1":"AC004846.2","2":"transcript","3":"1"},{"1":"AC004847.1","2":"transcript","3":"1"},{"1":"AC004852.1","2":"transcript","3":"1"},{"1":"AC004852.2","2":"transcript","3":"2"},{"1":"AC004852.3","2":"transcript","3":"1"},{"1":"AC004853.1","2":"transcript","3":"1"},{"1":"AC004854.1","2":"transcript","3":"1"},{"1":"AC004854.2","2":"transcript","3":"1"},{"1":"AC004858.1","2":"transcript","3":"1"},{"1":"AC004862.1","2":"transcript","3":"2"},{"1":"AC004863.1","2":"transcript","3":"1"},{"1":"AC004865.1","2":"transcript","3":"1"},{"1":"AC004865.2","2":"transcript","3":"1"},{"1":"AC004866.1","2":"transcript","3":"1"},{"1":"AC004866.2","2":"transcript","3":"1"},{"1":"AC004869.1","2":"transcript","3":"1"},{"1":"AC004869.2","2":"transcript","3":"1"},{"1":"AC004870.1","2":"transcript","3":"1"},{"1":"AC004870.2","2":"transcript","3":"3"},{"1":"AC004870.3","2":"transcript","3":"1"},{"1":"AC004870.4","2":"transcript","3":"2"},{"1":"AC004875.1","2":"transcript","3":"1"},{"1":"AC004877.1","2":"transcript","3":"1"},{"1":"AC004877.2","2":"transcript","3":"1"},{"1":"AC004879.1","2":"transcript","3":"1"},{"1":"AC004882.1","2":"transcript","3":"1"},{"1":"AC004882.2","2":"transcript","3":"1"},{"1":"AC004882.3","2":"transcript","3":"1"},{"1":"AC004884.1","2":"transcript","3":"1"},{"1":"AC004884.2","2":"transcript","3":"1"},{"1":"AC004888.1","2":"transcript","3":"1"},{"1":"AC004889.1","2":"transcript","3":"6"},{"1":"AC004890.1","2":"transcript","3":"1"},{"1":"AC004890.2","2":"transcript","3":"4"},{"1":"AC004890.3","2":"transcript","3":"1"},{"1":"AC004893.1","2":"transcript","3":"1"},{"1":"AC004893.2","2":"transcript","3":"1"},{"1":"AC004893.3","2":"transcript","3":"1"},{"1":"AC004895.1","2":"transcript","3":"2"},{"1":"AC004898.1","2":"transcript","3":"1"},{"1":"AC004899.1","2":"transcript","3":"1"},{"1":"AC004899.2","2":"transcript","3":"1"},{"1":"AC004900.1","2":"transcript","3":"1"},{"1":"AC004906.1","2":"transcript","3":"1"},{"1":"AC004908.1","2":"transcript","3":"1"},{"1":"AC004908.2","2":"transcript","3":"1"},{"1":"AC004908.3","2":"transcript","3":"1"},{"1":"AC004910.1","2":"transcript","3":"1"},{"1":"AC004911.1","2":"transcript","3":"1"},{"1":"AC004917.1","2":"transcript","3":"3"},{"1":"AC004918.1","2":"transcript","3":"1"},{"1":"AC004918.4","2":"transcript","3":"1"},{"1":"AC004920.1","2":"transcript","3":"1"},{"1":"AC004921.1","2":"transcript","3":"1"},{"1":"AC004921.2","2":"transcript","3":"1"},{"1":"AC004922.1","2":"transcript","3":"1"},{"1":"AC004923.1","2":"transcript","3":"2"},{"1":"AC004923.2","2":"transcript","3":"1"},{"1":"AC004923.3","2":"transcript","3":"1"},{"1":"AC004923.4","2":"transcript","3":"1"},{"1":"AC004925.1","2":"transcript","3":"1"},{"1":"AC004930.1","2":"transcript","3":"1"},{"1":"AC004936.1","2":"transcript","3":"1"},{"1":"AC004938.1","2":"transcript","3":"1"},{"1":"AC004938.2","2":"transcript","3":"1"},{"1":"AC004941.1","2":"transcript","3":"1"},{"1":"AC004941.2","2":"transcript","3":"1"},{"1":"AC004943.1","2":"transcript","3":"3"},{"1":"AC004943.2","2":"transcript","3":"2"},{"1":"AC004943.3","2":"transcript","3":"1"},{"1":"AC004944.1","2":"transcript","3":"1"},{"1":"AC004945.1","2":"transcript","3":"1"},{"1":"AC004946.1","2":"transcript","3":"1"},{"1":"AC004946.2","2":"transcript","3":"1"},{"1":"AC004947.1","2":"transcript","3":"3"},{"1":"AC004947.2","2":"transcript","3":"1"},{"1":"AC004947.3","2":"transcript","3":"1"},{"1":"AC004948.1","2":"transcript","3":"1"},{"1":"AC004949.1","2":"transcript","3":"1"},{"1":"AC004951.1","2":"transcript","3":"1"},{"1":"AC004951.2","2":"transcript","3":"1"},{"1":"AC004951.3","2":"transcript","3":"1"},{"1":"AC004951.4","2":"transcript","3":"1"},{"1":"AC004965.1","2":"transcript","3":"1"},{"1":"AC004967.1","2":"transcript","3":"1"},{"1":"AC004967.2","2":"transcript","3":"1"},{"1":"AC004969.1","2":"transcript","3":"1"},{"1":"AC004972.1","2":"transcript","3":"1"},{"1":"AC004973.1","2":"transcript","3":"1"},{"1":"AC004974.1","2":"transcript","3":"1"},{"1":"AC004975.1","2":"transcript","3":"1"},{"1":"AC004975.2","2":"transcript","3":"1"},{"1":"AC004980.1","2":"transcript","3":"2"},{"1":"AC004980.2","2":"transcript","3":"1"},{"1":"AC004980.3","2":"transcript","3":"1"},{"1":"AC004980.4","2":"transcript","3":"1"},{"1":"AC004982.1","2":"transcript","3":"1"},{"1":"AC004982.2","2":"transcript","3":"1"},{"1":"AC004986.1","2":"transcript","3":"1"},{"1":"AC004987.1","2":"transcript","3":"1"},{"1":"AC004987.2","2":"transcript","3":"1"},{"1":"AC004987.3","2":"transcript","3":"1"},{"1":"AC004987.4","2":"transcript","3":"1"},{"1":"AC004988.1","2":"transcript","3":"1"},{"1":"AC004990.1","2":"transcript","3":"2"},{"1":"AC004994.1","2":"transcript","3":"1"},{"1":"AC004997.1","2":"transcript","3":"1"},{"1":"AC005000.1","2":"transcript","3":"1"},{"1":"AC005000.2","2":"transcript","3":"1"},{"1":"AC005002.1","2":"transcript","3":"1"},{"1":"AC005002.2","2":"transcript","3":"1"},{"1":"AC005005.1","2":"transcript","3":"1"},{"1":"AC005005.2","2":"transcript","3":"1"},{"1":"AC005005.3","2":"transcript","3":"1"},{"1":"AC005005.4","2":"transcript","3":"1"},{"1":"AC005006.1","2":"transcript","3":"1"},{"1":"AC005008.1","2":"transcript","3":"1"},{"1":"AC005008.2","2":"transcript","3":"2"},{"1":"AC005009.1","2":"transcript","3":"1"},{"1":"AC005009.2","2":"transcript","3":"1"},{"1":"AC005011.1","2":"transcript","3":"1"},{"1":"AC005013.1","2":"transcript","3":"1"},{"1":"AC005014.1","2":"transcript","3":"1"},{"1":"AC005014.2","2":"transcript","3":"1"},{"1":"AC005014.3","2":"transcript","3":"1"},{"1":"AC005014.4","2":"transcript","3":"1"},{"1":"AC005019.1","2":"transcript","3":"1"},{"1":"AC005019.2","2":"transcript","3":"1"},{"1":"AC005020.1","2":"transcript","3":"1"},{"1":"AC005020.2","2":"transcript","3":"1"},{"1":"AC005021.1","2":"transcript","3":"1"},{"1":"AC005033.1","2":"transcript","3":"1"},{"1":"AC005034.1","2":"transcript","3":"1"},{"1":"AC005034.2","2":"transcript","3":"1"},{"1":"AC005034.3","2":"transcript","3":"1"},{"1":"AC005034.4","2":"transcript","3":"1"},{"1":"AC005034.5","2":"transcript","3":"1"},{"1":"AC005034.6","2":"transcript","3":"1"},{"1":"AC005037.1","2":"transcript","3":"1"},{"1":"AC005040.1","2":"transcript","3":"1"},{"1":"AC005040.2","2":"transcript","3":"2"},{"1":"AC005041.1","2":"transcript","3":"1"},{"1":"AC005041.2","2":"transcript","3":"1"},{"1":"AC005041.3","2":"transcript","3":"1"},{"1":"AC005041.4","2":"transcript","3":"1"},{"1":"AC005041.5","2":"transcript","3":"1"},{"1":"AC005042.1","2":"transcript","3":"1"},{"1":"AC005042.2","2":"transcript","3":"1"},{"1":"AC005042.3","2":"transcript","3":"1"},{"1":"AC005046.1","2":"transcript","3":"1"},{"1":"AC005050.1","2":"transcript","3":"1"},{"1":"AC005050.2","2":"transcript","3":"1"},{"1":"AC005050.3","2":"transcript","3":"1"},{"1":"AC005052.1","2":"transcript","3":"1"},{"1":"AC005052.2","2":"transcript","3":"1"},{"1":"AC005062.1","2":"transcript","3":"5"},{"1":"AC005064.1","2":"transcript","3":"1"},{"1":"AC005066.1","2":"transcript","3":"1"},{"1":"AC005070.1","2":"transcript","3":"1"},{"1":"AC005070.2","2":"transcript","3":"1"},{"1":"AC005070.3","2":"transcript","3":"1"},{"1":"AC005071.1","2":"transcript","3":"1"},{"1":"AC005072.1","2":"transcript","3":"1"},{"1":"AC005076.1","2":"transcript","3":"1"},{"1":"AC005077.1","2":"transcript","3":"1"},{"1":"AC005077.2","2":"transcript","3":"1"},{"1":"AC005077.3","2":"transcript","3":"2"},{"1":"AC005077.4","2":"transcript","3":"1"},{"1":"AC005081.1","2":"transcript","3":"1"},{"1":"AC005082.1","2":"transcript","3":"1"},{"1":"AC005082.2","2":"transcript","3":"1"},{"1":"AC005083.1","2":"transcript","3":"1"},{"1":"AC005084.1","2":"transcript","3":"1"},{"1":"AC005086.1","2":"transcript","3":"1"},{"1":"AC005086.2","2":"transcript","3":"1"},{"1":"AC005086.3","2":"transcript","3":"1"},{"1":"AC005086.4","2":"transcript","3":"1"},{"1":"AC005088.1","2":"transcript","3":"1"},{"1":"AC005089.1","2":"transcript","3":"1"},{"1":"AC005090.1","2":"transcript","3":"1"},{"1":"AC005091.1","2":"transcript","3":"1"},{"1":"AC005094.1","2":"transcript","3":"1"},{"1":"AC005096.1","2":"transcript","3":"1"},{"1":"AC005099.1","2":"transcript","3":"1"},{"1":"AC005100.1","2":"transcript","3":"3"},{"1":"AC005100.2","2":"transcript","3":"1"},{"1":"AC005102.1","2":"transcript","3":"1"},{"1":"AC005104.1","2":"transcript","3":"1"},{"1":"AC005104.2","2":"transcript","3":"1"},{"1":"AC005104.3","2":"transcript","3":"1"},{"1":"AC005105.1","2":"transcript","3":"1"},{"1":"AC005144.1","2":"transcript","3":"1"},{"1":"AC005153.1","2":"transcript","3":"1"},{"1":"AC005154.1","2":"transcript","3":"1"},{"1":"AC005154.2","2":"transcript","3":"1"},{"1":"AC005154.3","2":"transcript","3":"1"},{"1":"AC005154.4","2":"transcript","3":"1"},{"1":"AC005154.5","2":"transcript","3":"1"},{"1":"AC005156.1","2":"transcript","3":"1"},{"1":"AC005160.1","2":"transcript","3":"1"},{"1":"AC005162.1","2":"transcript","3":"1"},{"1":"AC005162.2","2":"transcript","3":"1"},{"1":"AC005162.3","2":"transcript","3":"1"},{"1":"AC005165.1","2":"transcript","3":"5"},{"1":"AC005165.2","2":"transcript","3":"1"},{"1":"AC005165.3","2":"transcript","3":"1"},{"1":"AC005176.1","2":"transcript","3":"3"},{"1":"AC005176.2","2":"transcript","3":"1"},{"1":"AC005178.1","2":"transcript","3":"1"},{"1":"AC005180.1","2":"transcript","3":"1"},{"1":"AC005180.2","2":"transcript","3":"1"},{"1":"AC005181.1","2":"transcript","3":"1"},{"1":"AC005183.1","2":"transcript","3":"1"},{"1":"AC005185.1","2":"transcript","3":"1"},{"1":"AC005186.1","2":"transcript","3":"3"},{"1":"AC005189.1","2":"transcript","3":"1"},{"1":"AC005191.1","2":"transcript","3":"1"},{"1":"AC005197.1","2":"transcript","3":"1"},{"1":"AC005208.1","2":"transcript","3":"2"},{"1":"AC005209.1","2":"transcript","3":"1"},{"1":"AC005220.1","2":"transcript","3":"1"},{"1":"AC005224.1","2":"transcript","3":"1"},{"1":"AC005224.2","2":"transcript","3":"1"},{"1":"AC005224.3","2":"transcript","3":"1"},{"1":"AC005225.1","2":"transcript","3":"1"},{"1":"AC005225.2","2":"transcript","3":"2"},{"1":"AC005225.3","2":"transcript","3":"1"},{"1":"AC005225.4","2":"transcript","3":"1"},{"1":"AC005229.1","2":"transcript","3":"1"},{"1":"AC005229.2","2":"transcript","3":"1"},{"1":"AC005229.3","2":"transcript","3":"1"},{"1":"AC005229.4","2":"transcript","3":"1"},{"1":"AC005229.5","2":"transcript","3":"1"},{"1":"AC005230.1","2":"transcript","3":"1"},{"1":"AC005232.1","2":"transcript","3":"3"},{"1":"AC005234.1","2":"transcript","3":"1"},{"1":"AC005234.2","2":"transcript","3":"1"},{"1":"AC005237.1","2":"transcript","3":"1"},{"1":"AC005237.2","2":"transcript","3":"1"},{"1":"AC005244.1","2":"transcript","3":"1"},{"1":"AC005244.2","2":"transcript","3":"1"},{"1":"AC005252.1","2":"transcript","3":"1"},{"1":"AC005252.2","2":"transcript","3":"1"},{"1":"AC005252.3","2":"transcript","3":"1"},{"1":"AC005252.4","2":"transcript","3":"1"},{"1":"AC005253.1","2":"transcript","3":"1"},{"1":"AC005253.2","2":"transcript","3":"1"},{"1":"AC005255.1","2":"transcript","3":"4"},{"1":"AC005255.2","2":"transcript","3":"1"},{"1":"AC005256.1","2":"transcript","3":"1"},{"1":"AC005258.1","2":"transcript","3":"1"},{"1":"AC005258.2","2":"transcript","3":"1"},{"1":"AC005261.1","2":"transcript","3":"4"},{"1":"AC005261.2","2":"transcript","3":"1"},{"1":"AC005261.3","2":"transcript","3":"1"},{"1":"AC005261.4","2":"transcript","3":"1"},{"1":"AC005261.5","2":"transcript","3":"1"},{"1":"AC005261.6","2":"transcript","3":"1"},{"1":"AC005262.1","2":"transcript","3":"1"},{"1":"AC005262.2","2":"transcript","3":"1"},{"1":"AC005264.1","2":"transcript","3":"1"},{"1":"AC005274.1","2":"transcript","3":"1"},{"1":"AC005277.1","2":"transcript","3":"1"},{"1":"AC005277.2","2":"transcript","3":"1"},{"1":"AC005280.1","2":"transcript","3":"3"},{"1":"AC005280.2","2":"transcript","3":"3"},{"1":"AC005280.3","2":"transcript","3":"1"},{"1":"AC005281.1","2":"transcript","3":"1"},{"1":"AC005288.1","2":"transcript","3":"1"},{"1":"AC005291.1","2":"transcript","3":"1"},{"1":"AC005291.2","2":"transcript","3":"1"},{"1":"AC005293.1","2":"transcript","3":"1"},{"1":"AC005294.1","2":"transcript","3":"1"},{"1":"AC005296.1","2":"transcript","3":"1"},{"1":"AC005297.1","2":"transcript","3":"1"},{"1":"AC005297.2","2":"transcript","3":"1"},{"1":"AC005297.3","2":"transcript","3":"1"},{"1":"AC005300.1","2":"transcript","3":"1"},{"1":"AC005301.1","2":"transcript","3":"1"},{"1":"AC005301.2","2":"transcript","3":"1"},{"1":"AC005303.1","2":"transcript","3":"1"},{"1":"AC005304.1","2":"transcript","3":"1"},{"1":"AC005304.2","2":"transcript","3":"1"},{"1":"AC005304.3","2":"transcript","3":"1"},{"1":"AC005306.1","2":"transcript","3":"1"},{"1":"AC005307.1","2":"transcript","3":"1"},{"1":"AC005323.1","2":"transcript","3":"3"},{"1":"AC005323.2","2":"transcript","3":"2"},{"1":"AC005324.1","2":"transcript","3":"2"},{"1":"AC005324.2","2":"transcript","3":"1"},{"1":"AC005324.3","2":"transcript","3":"1"},{"1":"AC005324.4","2":"transcript","3":"1"},{"1":"AC005324.5","2":"transcript","3":"1"},{"1":"AC005324.6","2":"transcript","3":"1"},{"1":"AC005326.1","2":"transcript","3":"1"},{"1":"AC005329.1","2":"transcript","3":"1"},{"1":"AC005329.2","2":"transcript","3":"1"},{"1":"AC005329.3","2":"transcript","3":"1"},{"1":"AC005330.1","2":"transcript","3":"1"},{"1":"AC005332.1","2":"transcript","3":"1"},{"1":"AC005332.2","2":"transcript","3":"1"},{"1":"AC005332.3","2":"transcript","3":"1"},{"1":"AC005332.4","2":"transcript","3":"1"},{"1":"AC005332.5","2":"transcript","3":"1"},{"1":"AC005332.6","2":"transcript","3":"1"},{"1":"AC005332.7","2":"transcript","3":"1"},{"1":"AC005336.1","2":"transcript","3":"1"},{"1":"AC005336.2","2":"transcript","3":"1"},{"1":"AC005336.3","2":"transcript","3":"1"},{"1":"AC005339.1","2":"transcript","3":"1"},{"1":"AC005341.1","2":"transcript","3":"1"},{"1":"AC005342.1","2":"transcript","3":"1"},{"1":"AC005342.2","2":"transcript","3":"1"},{"1":"AC005343.1","2":"transcript","3":"1"},{"1":"AC005343.4","2":"transcript","3":"1"},{"1":"AC005344.1","2":"transcript","3":"1"},{"1":"AC005345.1","2":"transcript","3":"1"},{"1":"AC005351.1","2":"transcript","3":"1"},{"1":"AC005355.1","2":"transcript","3":"1"},{"1":"AC005355.2","2":"transcript","3":"1"},{"1":"AC005357.1","2":"transcript","3":"1"},{"1":"AC005357.2","2":"transcript","3":"2"},{"1":"AC005358.1","2":"transcript","3":"1"},{"1":"AC005358.2","2":"transcript","3":"1"},{"1":"AC005363.1","2":"transcript","3":"1"},{"1":"AC005363.2","2":"transcript","3":"1"},{"1":"AC005377.1","2":"transcript","3":"1"},{"1":"AC005379.1","2":"transcript","3":"1"},{"1":"AC005381.1","2":"transcript","3":"2"},{"1":"AC005383.1","2":"transcript","3":"1"},{"1":"AC005387.1","2":"transcript","3":"1"},{"1":"AC005387.2","2":"transcript","3":"1"},{"1":"AC005391.1","2":"transcript","3":"1"},{"1":"AC005392.1","2":"transcript","3":"1"},{"1":"AC005392.2","2":"transcript","3":"1"},{"1":"AC005392.3","2":"transcript","3":"1"},{"1":"AC005393.1","2":"transcript","3":"1"},{"1":"AC005394.1","2":"transcript","3":"1"},{"1":"AC005394.2","2":"transcript","3":"1"},{"1":"AC005400.1","2":"transcript","3":"1"},{"1":"AC005410.1","2":"transcript","3":"2"},{"1":"AC005410.2","2":"transcript","3":"1"},{"1":"AC005412.1","2":"transcript","3":"1"},{"1":"AC005414.1","2":"transcript","3":"1"},{"1":"AC005476.1","2":"transcript","3":"1"},{"1":"AC005476.2","2":"transcript","3":"2"},{"1":"AC005479.1","2":"transcript","3":"1"},{"1":"AC005479.2","2":"transcript","3":"1"},{"1":"AC005480.1","2":"transcript","3":"1"},{"1":"AC005480.2","2":"transcript","3":"1"},{"1":"AC005481.1","2":"transcript","3":"1"},{"1":"AC005482.1","2":"transcript","3":"1"},{"1":"AC005486.1","2":"transcript","3":"1"},{"1":"AC005487.1","2":"transcript","3":"3"},{"1":"AC005495.1","2":"transcript","3":"1"},{"1":"AC005495.2","2":"transcript","3":"1"},{"1":"AC005498.1","2":"transcript","3":"1"},{"1":"AC005498.2","2":"transcript","3":"3"},{"1":"AC005498.3","2":"transcript","3":"1"},{"1":"AC005514.1","2":"transcript","3":"1"},{"1":"AC005515.1","2":"transcript","3":"3"},{"1":"AC005515.2","2":"transcript","3":"1"},{"1":"AC005518.1","2":"transcript","3":"1"},{"1":"AC005518.2","2":"transcript","3":"1"},{"1":"AC005519.1","2":"transcript","3":"1"},{"1":"AC005520.1","2":"transcript","3":"1"},{"1":"AC005520.2","2":"transcript","3":"2"},{"1":"AC005520.3","2":"transcript","3":"1"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[59050]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


3. Final task is to compute the number of transcripts per gene per chromosome.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGdyb3VwX2J5KGdlbmVfbmFtZSwgY2hyb20pICU+JSBjb3VudCgpXG5gYGAifQ== -->

```r
d2 %>% group_by(gene_name, chrom) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjU5NDc5LCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjU5LDQ3OSB4IDMiXSwiR3JvdXBzIjpbImdlbmVfbmFtZSwgY2hyb20gWzU5LDQ3OV0iXX19LCJyZGYiOiJINHNJQUFBQUFBQUFCdDJkWFc4Y3R4V0dWOVpLc3RVa01KQ2IvZ2tMU3g1eWhyemNYZFdLRVRzUkpBRjFyd28zY1pPZ2podzREdHJML3R6OGlDSXBTZThIK2I0enV6UDdJYm05a0VVK2MzaDRlRWdlZnN3b3VUcC9LYWN2VHdlRHdlRmdlSEF3T0R3S3ljSFI5TkliUHhnTUg0VE13V0E0ZUJSLy95c0lmWjRrQjRQSDRkZXZzd2ZEc1pwY3pOSVBZL3JKK0ZvdG4wMmZ6dEtIWS8xaWxqd0p5VXpxS0dTZnp6T1BVaVo3dWdBNkU3OVVDMVZ5TVg1K00zOTJQRFl4dTVBMEYxL2RMR3daajYrWDZlbjFva2hJWHk1dEdaK1Bwd3ZsTWZOOHJ2d1BzMnhtM0V4Q3lxd3BzcVh1cFczamkvRmthYytYbWRTTDgrbnl3WXZMNVlPdnhsbHJyblNXUHMvUzE4c0NWOWRMejRUTXVjcHp1V0hYNTE4cytqQm1MaSt6bXE0empUZVR6TGFicDFuNnk0WHV5Zmo1MTE4dG5rd1dSbjgybmt6R0l6VWFqYnpZTTQyMHFxdnFUQUVWVlRWUVkwZTFhYUhMY1RLWmpwY05qcG5Sb21OaVRsMFdEM1dSazBLMHZzeVZGalZJbmpGNXh1YVpLcy9VZWNibEdiOFk5VEZUVEpMSmRKSzNackpvemNNUHVVVjN6dk1DZVZPVUxoVG5kazl5dXllNTNaUGM3a2x1OThSbm1XbHU1blJoNW9kYytVd1h1ZHlWMDl6SjArSkpidXpVWmc2YlduRFlkRzc5U2Nwa1VTTmxpeXJ5NWt6ejVwem5wYzdWb3RRc0syWFdsTm5jbCtkNWZlZDVrODd6SnYwcGI4SFRJcE5YL1ZRWHpRblpYUDNUWFAxRnJ1UWlGN3ZJSzc3SWpiM0kvUEZGRmpkaVpsVGs1czlPUCtTeUx2andQRE02NWliRlF5a2VtdkVzKzhrcysyUTgvWXNxUmNyeXRxamNGcUU1a21wY1ppZEZ0cDQvZlRUTExseTZBSUxBSUxBSUtnUTFBb2ZBbDJhQmxkT2l5VVhINkR3amVjYmtHWnRucWp3ejF4Ymk5TE9zMzU2cGhTdmlFNTJsSlpPU3llWFMxR2MzVitNWHk1YWxiSzdsZVo3T1p2N3paeTlVa1N1ZnplczdIRSsrWGlhdmxycXV4a3Y1cTdEOExzUGUxZmpsY2tETTgxbGJicko2YjFTMkhrNXVKa1ZtWHVaMFBBMnIxMGowbVFKaWlWUklqRUppUjBRY2tvcEtWVlJYdFZ4TjU2UW1HZGJzcUpRN2s1TFUxTkthTk5lazJaSE5qdlE0YW9YelNMd1FvVktlMmg0SXRNS2pacVhRd2tnMEVLeGRhZXl2U0RRUkFjSjZoRW9KbFRKUVNpcjBvVkF2Qy9XWDFLakhqTUFlcFhYVlFBb0w5Y2lBbmtSWVJrcGlLeXhsU2JPdFNhWW1HVTh5dmtFR2FxL0k1b3BzcnNqbWlteXVxUGFLYXErbzlucUVwYkIzOUFoN1IrYzcyRG14SkdOSmhteXV5YytlN1BHcWdXQXBzdENUaFo1OEdJZ2hZZ3VpUmxpN1V0aFNwYkJka1pTMUs0M2pSMm5YUU1wU01rTE5vdEEva1dnaVFzUVFLVnNxZ2kwVmd6WkhBblhoNmhBSTZhR3hTakVoRVBTRzBJZ1NqT3FCVUNtTTZvRUlFYkxIa3g2UGZXRkcyRkpEWXlNU0xLV2dMeUl4UUxCZGhzYUdvYkZoTk03M1NFQ0craUlTbE1HNVl5Z2VHb3FIQnZjQTJsVGtud3BIcHFtd0x3eEZQNE03aDBSUUQzbU00bGdrVUlvaVd5UXNJMFNndnh6VjVhZ3VHbU9Sb0F5MndvN1FHeGIzQUlsb0lnSUV4MFlrVUFyWDdrRFE4NWJHbURYWWRtdlFxNUZBWFFiNzNScXF5NkRITE1XV1NFQXpqWEJycVM0clZBcEhieVFzQTE2bDBlc2QrdERqamxGN0QrMlNFY2FOUkRRUVRUSzZRVWFBV0NwbHFSVGI0NUhnU1NFUjBJTjltZ2pLR0pJaHpUaHpFOUZFYWlEa3c1cDhpUE5kNkZ3Z0k1ekxnWkROT0pjRElUMmU2dkxVWDNoU0NBVEdvZEIrSXhGTlJJQmc3VW9ocVhCM0dwWmw3SXNLVitwQXFCU3VzSUZRWGVUREN1ZEZJaG9JMVU1K3JoeDZMSkpTajhlNEVRaHE5cmlmRHdRMWU0d0o0c2xqSHRjbThUWDJjaVFvUTNySWh4UmJoR0pMSU5RdVhHVVNFU0RVVWp6VkpnSjZjQitlQ01wQUs4S21hVVRFRUttSStBYWlpVWhKY013SFFuVXBnM28wbGRLYVpJUmtTTE13b1ZiZ2p0cU1MTldPNjFjZ3BCbEhyeG5odVRzUTBvT3pPeEZvS2U2c0RKMHJEWjByRFowUURaMzFESjMxRE1YZVJGaEdpQmdpbGtoRnBBWkMvc0haWkNpS0dvVTdva1Ewa2RKbUpkaFNSYU5GMGRoUWVOcEtSSWdZSWhhSUk4ME9OZU0rS2hEeUJwNENBaUhOZUFwSXBDeWxGYlkwRXBEUmFJK21PYWp4aGlvUklXS0FvT2NqQVQxNEZrNEVOT1A5YWlLb2gyd1dzbG5JWnJ5RFRVUVQ0VktHU0RrU05NVVdqWHZqUUtoM0tKSkVBcVh3SEpkSUtVTm44MFJZUm9CZzdYUitUNlRVRXlZenlFUUNNbmlUWXd6TkZFTXpKUkloWW9CZ2xERGtlVU9lTjNpTG5naktvSjhOUlhWRFVUMFNzSm5pUEoyZ0F5RjdjSThkQ1BrUTk5aUpZQ21NWXdiM05vWk8wTUdwWkNIdVNRTEJXV253YmpDUjBoNUxld0JMZTRCSWhJZ0JndVBINHIyTm9STjBJaWhEOXVDWjJ0Q1pPaEFjZFhRNlRnVDAwQTdFMG40akVpaUZ1OU5FV0FZOFJyMXM4UlJnTFBXeXhYTlRJbGdLbzZpbGZyZDRFZzhFeDJwRnEwd2xKSVBueWtRMEVLeTl3anZ6UUhDMFJBSXllQUpLaEdYS3RsZmt3MGlnRk0yZEN2ZnpnVkJMOFZRYkNIbzFrbEtteHZOcElPaWZtazRCa2FBZWpHTTEzcDJhR3M5b0lkaGdTMnVLWTVFSUVVUEVBdUc2eUI2YUtUWDFUazI5VTVQbmF4cXJEdThLRXRGRWhJZ2hZb2xVUkdvZ09GTWMzajRsZ3ZaWXNnZG5nYU9WMnRFK1BCS1V3ZkhqOEMxTUlPUkQybmxHSWtRTUVmQVkzcDBtb29tQVpsb3ZISzBYanM0T2puYWVqbmFla1FnUlE4UVNnWDZuYzRyRHU4RkVzSGJxQzRxaWtXQXA4cnlRNStsTTdXZzlkYlNlT3J4YlRnUmx5RUphR1NQUlJNQm0ydWs1V2s4ZHJhZU9UbEtPVHZTTzl1R09ZcDNEKzZoQXFPMjBQM1IweG5kMHhuZTBQM1I0VDV1SUVJRWVwSmpwS0dZNjJqRzZtaXpFRzdORU5CR3doNkt4bzMyTHcvdXhRS2gyaXVHT1luZ2tVRHZ0WkJ6dFpCenRaQngrVHhJSVdZaGZqeVJTYXZaMEYrZHB6ZlVValQxRjQwaEtDejIrbFE0RTF5WlBzZGNycWt2aFNjcmoxeXlCa0dhTnE1Nm4vYnluZloybjJ3TlB0d2VlYmc4OHhYbFA4ZGxUOVBOQ0xSWHlLcDM2UFozNlBlMzVQYjV6U1FSYWdXOWhBcUZXVUlUMEZDRTlSVWhQTjFTZWJxZzhuYWs5blZNOG5WTThuVk1pRVNMUU94UVBQWjF6SXdITkZQMDg3ZkRwVmo4UThpcEZOby9md0JoUGQ1NmU3anc5M1hsNnV2T2s5d1dKb0I3cVV6cE5lRHBOZURwTlJJSzFVNTlTUFBSMC92SzRnN1gwZGlBUkRVU1RqQ1laU3pLMlFVYUlHQ0FWNlhFTkJEVjdrdkVvZy9Fd0VDRmlHZ2pxTWRnS1piQVYrUFkvRWRDRGNUVVJsQ0diOFQ3VDBsdVBSRFFSSVdLSVdDSVZFQm8vdVBkTEJHckh1Sm9JeXdnUlF3UXN4RnVhUkZDekpzMDB4dkI5WlNLYUNPakIzV2tpVUFyM2tKYStOclQwVnNqU0czbExiK1FUQVh2d2xHM3AvYnVsdDBLVzNzZ25nblhWVkZlTnZZTjN5NWErNWswRU5PUGRzcVczLzViZUNpV0NlaXEwTUJDMGtDSUo3aW90ZmYxbzZldEhTOTg2QmtJVzRtN1FLb3EwaWlJdGZVVVFDTllWQ1piQzJhMG9HaXZjZ1ZpRnA2UkFXSWJxc2xTWHhVZ1NpUVdDL3FHM1ZJRlEyeXNjTFpGQTdiamZTQVJMNGJxamFCWW9mRGNhQ0k0V2hUdUhSS0FVelFKRkkxemhIV3dnWkErTlRFWGpVTkZxcm1tdDFMUXlhbHAzNkgyY3BmZHhpUWdRYklYRys1WkV1SlFCd2hiaVdxbnhmVzRpcktjY2RSclBCWmJlR0FaQ2JhZlZVOU9xcC9IbXhHcWFYeHEvZTdlYTFndE42NFhHMjR4RW9LVTBjK2tkWWlEVU81WjZoMmFseGp1UVJLQVV2aU5MaEdXRWlDRmlpVlJBeUdNVUUraTlwOVY0TGdpRWVwRFdPRTJ6VzlQczFuZ3VDSVM4UWF1TXBwbXI4WjQvRU9wVFdtVTByVEthSTRDbmRubGN1NFZXSXNGNzdFUTBFTFJRYUcwU1dwdUUxaWI2b2o0UWJJWGdYVUVpb0ptaWxsRFVFb3Bha1JnaWxrZ0ZoR3ltcUNVVXRZU2lsbEJFRW9wSWdqZkppYUFlakFtQ044bUpWRVJxSU5RdXdYVXdFcXlkMms0N2ZLR2R1ZERPWFBBdUpSRm9GOFZWd1pzVFMzL3ZFQWpMWVBRVC9Nb2lFWlRCdVN3VUR3WHZoQk1CR1lvL2dyZXBscjdOc1BUZFJTQ2tCMjhQckZDMEVYeVRsUWlXb2pGR0VVa29JZ20reDdmME54clc0TTFrSWxBSzN5NEZnblVaMnJNWjJyTVo4bW9rSUVOeG52NTJJQkQwb2FHekRIMGRZZW12Q2F6QmUrTkVvQzY4U1U2RVpjcmVzZVFmaTkvU0pLS0JZRjJSb0F6NjBOS08wZEpOaFUxL3oxaitKeitPdnZuK25mS3RtVkdlMGJ2S0RHTW1lNkR6QjlLcHVHMG9zU3F0V3RLNWpNa3J5eXNvckZCdEQrcThSYU5NeXVWU1ZmYWdhaXFlVzBKVzVaVVg5YWsyUzVyTklsT2tVMHY4bXZUS2h1Z1cyd3M3Zk51RGJnMVVqWmEwWjNKYnFCYTdOdFBXTEV6bk1pL2IzS0RibWw0MVBhQk1PUVJHclUxZk4xdFcxMUptVkpQOWVTUFpsc0prM2VDamxaRkNOYzJNM0Vmb2VWbkRDOCt0bkc1TnRtSzZyYmIyT1ZoVTF5V1F0VTdVWWt4WHJSWDZCcysyeG1QS3FNYUoyNTZoS1czYlhGM01MOWZrb3BXZWFGKzFta0lRK1V2YTdHMTZRSU55MU5Bb1hLUmFRNlRMMGxXYmQwWnJoTWdmL2J1Mk9iTXFxcldPOEo1MWZHU1pyazF1Q3VUM2xTN0c2cHBNeCtXa2wyLzJOSm82TjMzZEFydDFCWGVRb1NpeFBrTm1kaXpmY1hudm1HbHZXdXVhL25KTmV1V0dvdGR3NnVPSkRUb3NOOTYycFBjZEJOcjJSRTBiZ3J1UDRkdTJyaGdTMjR4dlVyWnV0N2FENExEMURPcVkwZDM4c200Szl2Rnh6NjVZV1dXbnM5a0dmcm1uSlduM3E0ZGY1N2xWN3RXTm82TjNHTjdhK0Q1bDJ1ZEt0NFh3cnJwM3F5aDJyN3ZGMXV1R3BpUGF4NUplSDZqMjVUdlg1THY3RHd5YlRNRnV3YnJid0dsZmVEWXhjNU1WY3V2dDY5YlgyaDNkM3FwZ3hYVkx0eHVXVG1GODdScTdrOFdwMzM0eVNtM3c3cURUUXRlODZuVnMxK3BCMzZYQjdiM1ZPMGowaW5QOWR6cjlmZDF0YkcyUzJUNklkb3gwdmNmV0NsMjdiMWIvNFVEVm1XMjdaT3R0WDI1TjA5RzRUM2UxKzN0bkx5VTNEdkw5ZDF0RnhueWNtYnU3RUdpTE1IdmZBYTl0OTE1cVcxV2c3UzMxZloxYStpMHduVmFiankyOXNtOWNoL1NkR3FyNlpmWm1SN2Q5NlgyNDZTN1N4YjVnbjRIajN1WjFsNTBmZXVKZWI0bnZLOTNscTREL3hmU21Vd0xUYmZML3I2SGhZMHYzNmV1OUhGbnVJbXkxbjVCdFU5TTZIbndvMHEvYndQWTRHWmJmNUhRMGJJc2QvRVplYlZQVUo1YjBPV1Iyek96Mi9kVDlaM2JXKzYxemVaTk8yT01PWU1jMzJKdG82NW5wRm5EdXo4NTd2VVhCRWRIMkdVVEhlNS8yVnE2OVVWMmplYlN0eTdiSjdHUnk3dVYycC9ONnNOdngxL3RXdTNzYkI0TlAwNTg0eFAvbDZXQVEvOWVuSjFuNndTeDlrS1dIczkrSExYSUgyZlBoN0hmOGVUVDcvU0FyaXo5SERUb2Z3TE9tZWc1QUw5YjlJSHMrSEhTelk1ajU0emo4UE16MERBZWxuWE85eDVtUDVuVThuUEVtSHpXMUZYMStuTFVEeXd5ejlMemNYTzRZNUE4RzdlMUZldzVuUHBpbjg5OVI1cFBaN3hPd1kxNys0YXkvSDJVeUo2Qy9xUS9YMmJWTzVtUDZRWHNQZ1hWcDg2NXRhSnM3dTY3elpOQnN3M3ljSHUrcDN2dnc0YTUwcnFxbmFYN3Y0cWVwSDFiWjBkWnZtL2dJNDltdS9McXBMb3lYdXhxais1cm4rNWhEZmZ2MVlZdE0yMXE5YlgzMzdmZDhEV3V5ZWJpbS9LbzlRTjh5MjdSdkV6dXczajU5bGUrTHR1bnpWVzN1cTJjZmZ0M1VuazNrTnkxekY3cjd6UDlkdFdHWDgvMm9oMTU4dnNsYzNyWWRUZk5xWFN4cXFuY2ZNVE0vZisxYWQ5K2ZiZnRtblh6Zi9mYmhpbWVnYy9qaDkvencvdWJWeno4UDBuK2ZZQUZQdjMzMS90WFozOSs5K3ZFMWlEOTY5L2FmWjdlQnh5S2ZScDMvRHYvOC92dC8vb2g2NTBLUGswR3owdCs5dm4zOTEvZ2t1enA0KytNc2MzQWIvdmtOOUp5OC9lbjlEMjl2ZzZZSG53OCtUS2ZjMG9OM0FCNy9jaHYxZi92a20rOS91ZjNIazNRdmNUcHIvMkJtYzBqLyt1ZGxPdm9yMWpuOGZhYnJhS2JyK1BYdGR6L2NMbXg5OCtwdnI5L01NcDhGUDZRV252MzA3b2ZiOTNPL0JmcnoyZnUzNzEvTjVVNi9lZnRtVGxMakJyLzlGMGN2Wm1nMGhnQUEifQ== -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_name"],"name":[1],"type":["chr"],"align":["left"]},{"label":["chrom"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"A1BG","2":"chr19","3":"1"},{"1":"A1BG-AS1","2":"chr19","3":"2"},{"1":"A1CF","2":"chr10","3":"7"},{"1":"A2M","2":"chr12","3":"1"},{"1":"A2M-AS1","2":"chr12","3":"2"},{"1":"A2ML1","2":"chr12","3":"2"},{"1":"A2ML1-AS1","2":"chr12","3":"1"},{"1":"A2ML1-AS2","2":"chr12","3":"1"},{"1":"A2MP1","2":"chr12","3":"2"},{"1":"A3GALT2","2":"chr1","3":"1"},{"1":"A4GALT","2":"chr22","3":"4"},{"1":"A4GNT","2":"chr3","3":"1"},{"1":"AAAS","2":"chr12","3":"3"},{"1":"AACS","2":"chr12","3":"1"},{"1":"AACSP1","2":"chr5","3":"2"},{"1":"AADAC","2":"chr3","3":"2"},{"1":"AADACL2","2":"chr3","3":"1"},{"1":"AADACL2-AS1","2":"chr3","3":"1"},{"1":"AADACL3","2":"chr1","3":"1"},{"1":"AADACL4","2":"chr1","3":"1"},{"1":"AADACP1","2":"chr3","3":"3"},{"1":"AADAT","2":"chr4","3":"4"},{"1":"AAGAB","2":"chr15","3":"3"},{"1":"AAK1","2":"chr2","3":"3"},{"1":"AAMDC","2":"chr11","3":"9"},{"1":"AAMP","2":"chr2","3":"3"},{"1":"AANAT","2":"chr17","3":"2"},{"1":"AAR2","2":"chr20","3":"3"},{"1":"AARD","2":"chr8","3":"1"},{"1":"AARS","2":"chr16","3":"1"},{"1":"AARS2","2":"chr6","3":"1"},{"1":"AARSD1","2":"chr17","3":"1"},{"1":"AARSP1","2":"chr4","3":"1"},{"1":"AASDH","2":"chr4","3":"5"},{"1":"AASDHPPT","2":"chr11","3":"1"},{"1":"AASS","2":"chr7","3":"2"},{"1":"AATBC","2":"chr21","3":"2"},{"1":"AATF","2":"chr17","3":"1"},{"1":"AATK","2":"chr17","3":"2"},{"1":"ABALON","2":"chr20","3":"1"},{"1":"ABAT","2":"chr16","3":"5"},{"1":"ABBA01000935.2","2":"chr3","3":"1"},{"1":"ABBA01006766.1","2":"chr17","3":"1"},{"1":"ABBA01031666.1","2":"chr20","3":"1"},{"1":"ABBA01045074.1","2":"chr9","3":"1"},{"1":"ABBA01045074.2","2":"chr9","3":"1"},{"1":"ABCA1","2":"chr9","3":"3"},{"1":"ABCA10","2":"chr17","3":"1"},{"1":"ABCA11P","2":"chr4","3":"2"},{"1":"ABCA12","2":"chr2","3":"3"},{"1":"ABCA13","2":"chr7","3":"1"},{"1":"ABCA17P","2":"chr16","3":"3"},{"1":"ABCA2","2":"chr9","3":"4"},{"1":"ABCA3","2":"chr16","3":"3"},{"1":"ABCA4","2":"chr1","3":"3"},{"1":"ABCA5","2":"chr17","3":"2"},{"1":"ABCA6","2":"chr17","3":"2"},{"1":"ABCA7","2":"chr19","3":"3"},{"1":"ABCA8","2":"chr17","3":"4"},{"1":"ABCA9","2":"chr17","3":"3"},{"1":"ABCA9-AS1","2":"chr17","3":"2"},{"1":"ABCB1","2":"chr7","3":"3"},{"1":"ABCB10","2":"chr1","3":"1"},{"1":"ABCB10P1","2":"chr15","3":"1"},{"1":"ABCB10P3","2":"chr15","3":"1"},{"1":"ABCB10P4","2":"chr15","3":"1"},{"1":"ABCB11","2":"chr2","3":"1"},{"1":"ABCB4","2":"chr7","3":"5"},{"1":"ABCB5","2":"chr7","3":"4"},{"1":"ABCB6","2":"chr2","3":"2"},{"1":"ABCB7","2":"chrX","3":"7"},{"1":"ABCB8","2":"chr7","3":"6"},{"1":"ABCB9","2":"chr12","3":"8"},{"1":"ABCC1","2":"chr16","3":"2"},{"1":"ABCC10","2":"chr6","3":"2"},{"1":"ABCC11","2":"chr16","3":"4"},{"1":"ABCC12","2":"chr16","3":"1"},{"1":"ABCC13","2":"chr21","3":"2"},{"1":"ABCC2","2":"chr10","3":"2"},{"1":"ABCC3","2":"chr17","3":"3"},{"1":"ABCC4","2":"chr13","3":"4"},{"1":"ABCC5","2":"chr3","3":"6"},{"1":"ABCC5-AS1","2":"chr3","3":"1"},{"1":"ABCC6","2":"chr16","3":"4"},{"1":"ABCC6P1","2":"chr16","3":"3"},{"1":"ABCC6P2","2":"chr16","3":"2"},{"1":"ABCC8","2":"chr11","3":"8"},{"1":"ABCC9","2":"chr12","3":"6"},{"1":"ABCD1","2":"chrX","3":"2"},{"1":"ABCD1P2","2":"chr10","3":"1"},{"1":"ABCD1P3","2":"chr16","3":"1"},{"1":"ABCD1P4","2":"chr22","3":"1"},{"1":"ABCD1P5","2":"chr2","3":"1"},{"1":"ABCD2","2":"chr12","3":"1"},{"1":"ABCD3","2":"chr1","3":"2"},{"1":"ABCD4","2":"chr14","3":"2"},{"1":"ABCE1","2":"chr4","3":"1"},{"1":"ABCF1","2":"chr6","3":"2"},{"1":"ABCF2","2":"chr7","3":"2"},{"1":"ABCF2P1","2":"chr3","3":"1"},{"1":"ABCF2P2","2":"chr7","3":"1"},{"1":"ABCF3","2":"chr3","3":"2"},{"1":"ABCG1","2":"chr21","3":"6"},{"1":"ABCG2","2":"chr4","3":"3"},{"1":"ABCG4","2":"chr11","3":"3"},{"1":"ABCG5","2":"chr2","3":"1"},{"1":"ABCG8","2":"chr2","3":"1"},{"1":"ABHD1","2":"chr2","3":"2"},{"1":"ABHD10","2":"chr3","3":"2"},{"1":"ABHD11","2":"chr7","3":"4"},{"1":"ABHD11-AS1","2":"chr7","3":"2"},{"1":"ABHD12","2":"chr20","3":"2"},{"1":"ABHD12B","2":"chr14","3":"2"},{"1":"ABHD13","2":"chr13","3":"1"},{"1":"ABHD14A","2":"chr3","3":"3"},{"1":"ABHD14A-ACY1","2":"chr3","3":"1"},{"1":"ABHD14B","2":"chr3","3":"6"},{"1":"ABHD15","2":"chr17","3":"1"},{"1":"ABHD15-AS1","2":"chr17","3":"1"},{"1":"ABHD16A","2":"chr6","3":"2"},{"1":"ABHD16B","2":"chr20","3":"1"},{"1":"ABHD17A","2":"chr19","3":"3"},{"1":"ABHD17AP1","2":"chr1","3":"1"},{"1":"ABHD17AP3","2":"chr1","3":"1"},{"1":"ABHD17AP4","2":"chr22","3":"1"},{"1":"ABHD17AP5","2":"chr22","3":"1"},{"1":"ABHD17AP6","2":"chr17","3":"1"},{"1":"ABHD17AP7","2":"chr16","3":"1"},{"1":"ABHD17AP8","2":"chr16","3":"1"},{"1":"ABHD17AP9","2":"chr16","3":"1"},{"1":"ABHD17B","2":"chr9","3":"2"},{"1":"ABHD17C","2":"chr15","3":"3"},{"1":"ABHD18","2":"chr4","3":"5"},{"1":"ABHD2","2":"chr15","3":"2"},{"1":"ABHD3","2":"chr18","3":"3"},{"1":"ABHD4","2":"chr14","3":"2"},{"1":"ABHD5","2":"chr3","3":"3"},{"1":"ABHD6","2":"chr3","3":"2"},{"1":"ABHD8","2":"chr19","3":"1"},{"1":"ABI1","2":"chr10","3":"12"},{"1":"ABI1P1","2":"chr14","3":"1"},{"1":"ABI2","2":"chr2","3":"7"},{"1":"ABI3","2":"chr17","3":"2"},{"1":"ABI3BP","2":"chr3","3":"4"},{"1":"ABITRAM","2":"chr9","3":"2"},{"1":"ABITRAMP1","2":"chr13","3":"1"},{"1":"ABL1","2":"chr9","3":"2"},{"1":"ABL2","2":"chr1","3":"8"},{"1":"ABLIM1","2":"chr10","3":"9"},{"1":"ABLIM2","2":"chr4","3":"9"},{"1":"ABLIM3","2":"chr5","3":"7"},{"1":"ABO","2":"chr9","3":"2"},{"1":"ABR","2":"chr17","3":"7"},{"1":"ABRA","2":"chr8","3":"1"},{"1":"ABRACL","2":"chr6","3":"2"},{"1":"ABRAXAS1","2":"chr4","3":"3"},{"1":"ABRAXAS2","2":"chr10","3":"1"},{"1":"ABT1","2":"chr6","3":"1"},{"1":"ABT1P1","2":"chr4","3":"1"},{"1":"ABTB1","2":"chr3","3":"3"},{"1":"ABTB2","2":"chr11","3":"1"},{"1":"AC000032.1","2":"chr1","3":"1"},{"1":"AC000035.1","2":"chr22","3":"1"},{"1":"AC000036.1","2":"chr22","3":"1"},{"1":"AC000041.1","2":"chr22","3":"1"},{"1":"AC000050.1","2":"chr22","3":"1"},{"1":"AC000058.1","2":"chr7","3":"1"},{"1":"AC000061.1","2":"chr7","3":"1"},{"1":"AC000065.1","2":"chr7","3":"2"},{"1":"AC000065.2","2":"chr7","3":"1"},{"1":"AC000067.1","2":"chr22","3":"1"},{"1":"AC000068.1","2":"chr22","3":"1"},{"1":"AC000068.2","2":"chr22","3":"1"},{"1":"AC000068.3","2":"chr22","3":"1"},{"1":"AC000072.1","2":"chr22","3":"1"},{"1":"AC000077.1","2":"chr22","3":"1"},{"1":"AC000078.1","2":"chr22","3":"1"},{"1":"AC000081.1","2":"chr22","3":"1"},{"1":"AC000082.1","2":"chr22","3":"1"},{"1":"AC000085.1","2":"chr22","3":"1"},{"1":"AC000089.1","2":"chr22","3":"1"},{"1":"AC000093.1","2":"chr22","3":"1"},{"1":"AC000095.1","2":"chr22","3":"1"},{"1":"AC000095.2","2":"chr22","3":"1"},{"1":"AC000095.3","2":"chr22","3":"1"},{"1":"AC000099.1","2":"chr7","3":"1"},{"1":"AC000111.1","2":"chr7","3":"1"},{"1":"AC000111.2","2":"chr7","3":"1"},{"1":"AC000113.1","2":"chrX","3":"1"},{"1":"AC000120.1","2":"chr7","3":"1"},{"1":"AC000120.2","2":"chr7","3":"1"},{"1":"AC000120.3","2":"chr7","3":"1"},{"1":"AC000123.1","2":"chr7","3":"1"},{"1":"AC000123.2","2":"chr7","3":"1"},{"1":"AC000123.3","2":"chr7","3":"1"},{"1":"AC000124.1","2":"chr7","3":"1"},{"1":"AC000362.1","2":"chr7","3":"1"},{"1":"AC000367.1","2":"chr7","3":"1"},{"1":"AC000372.1","2":"chr7","3":"1"},{"1":"AC000374.1","2":"chr7","3":"1"},{"1":"AC000403.1","2":"chr13","3":"1"},{"1":"AC001226.1","2":"chr13","3":"1"},{"1":"AC001226.2","2":"chr13","3":"1"},{"1":"AC002044.1","2":"chr16","3":"1"},{"1":"AC002044.2","2":"chr16","3":"1"},{"1":"AC002044.3","2":"chr16","3":"1"},{"1":"AC002056.1","2":"chr22","3":"1"},{"1":"AC002056.2","2":"chr22","3":"1"},{"1":"AC002057.1","2":"chr7","3":"1"},{"1":"AC002057.2","2":"chr7","3":"1"},{"1":"AC002059.1","2":"chr22","3":"1"},{"1":"AC002059.2","2":"chr22","3":"1"},{"1":"AC002059.3","2":"chr22","3":"2"},{"1":"AC002064.1","2":"chr7","3":"1"},{"1":"AC002064.2","2":"chr7","3":"1"},{"1":"AC002064.3","2":"chr7","3":"1"},{"1":"AC002066.1","2":"chr7","3":"3"},{"1":"AC002069.1","2":"chr7","3":"2"},{"1":"AC002069.2","2":"chr7","3":"1"},{"1":"AC002069.3","2":"chr7","3":"1"},{"1":"AC002070.1","2":"chr12","3":"3"},{"1":"AC002072.1","2":"chrX","3":"1"},{"1":"AC002074.1","2":"chr7","3":"1"},{"1":"AC002074.2","2":"chr7","3":"1"},{"1":"AC002075.1","2":"chr7","3":"1"},{"1":"AC002075.2","2":"chr7","3":"1"},{"1":"AC002076.1","2":"chr7","3":"1"},{"1":"AC002076.2","2":"chr7","3":"1"},{"1":"AC002090.1","2":"chr17","3":"1"},{"1":"AC002091.1","2":"chr17","3":"1"},{"1":"AC002091.2","2":"chr17","3":"1"},{"1":"AC002094.1","2":"chr17","3":"1"},{"1":"AC002094.2","2":"chr17","3":"1"},{"1":"AC002094.3","2":"chr17","3":"1"},{"1":"AC002094.4","2":"chr17","3":"1"},{"1":"AC002094.5","2":"chr17","3":"1"},{"1":"AC002101.1","2":"chr9","3":"1"},{"1":"AC002115.1","2":"chr19","3":"1"},{"1":"AC002116.1","2":"chr19","3":"1"},{"1":"AC002116.2","2":"chr19","3":"1"},{"1":"AC002127.1","2":"chr7","3":"1"},{"1":"AC002128.1","2":"chr19","3":"1"},{"1":"AC002128.2","2":"chr19","3":"1"},{"1":"AC002306.1","2":"chr19","3":"1"},{"1":"AC002310.1","2":"chr16","3":"1"},{"1":"AC002310.2","2":"chr16","3":"2"},{"1":"AC002310.3","2":"chr16","3":"1"},{"1":"AC002310.4","2":"chr16","3":"1"},{"1":"AC002310.5","2":"chr16","3":"1"},{"1":"AC002331.1","2":"chr16","3":"1"},{"1":"AC002347.1","2":"chr17","3":"1"},{"1":"AC002347.2","2":"chr17","3":"1"},{"1":"AC002350.1","2":"chr12","3":"1"},{"1":"AC002351.1","2":"chr12","3":"3"},{"1":"AC002366.1","2":"chrX","3":"1"},{"1":"AC002367.1","2":"chrX","3":"1"},{"1":"AC002368.1","2":"chrX","3":"1"},{"1":"AC002375.1","2":"chr12","3":"1"},{"1":"AC002377.1","2":"chrX","3":"1"},{"1":"AC002378.1","2":"chr22","3":"1"},{"1":"AC002381.1","2":"chr7","3":"1"},{"1":"AC002383.1","2":"chr7","3":"1"},{"1":"AC002386.1","2":"chr7","3":"1"},{"1":"AC002398.1","2":"chr19","3":"1"},{"1":"AC002398.2","2":"chr19","3":"1"},{"1":"AC002400.1","2":"chr16","3":"1"},{"1":"AC002401.1","2":"chr17","3":"1"},{"1":"AC002401.2","2":"chr17","3":"1"},{"1":"AC002401.3","2":"chr17","3":"1"},{"1":"AC002401.4","2":"chr17","3":"1"},{"1":"AC002407.1","2":"chrX","3":"1"},{"1":"AC002428.1","2":"chr5","3":"1"},{"1":"AC002428.2","2":"chr5","3":"1"},{"1":"AC002429.1","2":"chr7","3":"1"},{"1":"AC002429.2","2":"chr7","3":"2"},{"1":"AC002451.1","2":"chr7","3":"7"},{"1":"AC002451.2","2":"chr7","3":"1"},{"1":"AC002454.1","2":"chr7","3":"1"},{"1":"AC002456.1","2":"chr7","3":"2"},{"1":"AC002456.2","2":"chr7","3":"1"},{"1":"AC002458.1","2":"chr7","3":"1"},{"1":"AC002460.1","2":"chr4","3":"2"},{"1":"AC002460.2","2":"chr4","3":"2"},{"1":"AC002463.1","2":"chr7","3":"6"},{"1":"AC002464.1","2":"chr6","3":"1"},{"1":"AC002465.1","2":"chr7","3":"1"},{"1":"AC002465.2","2":"chr7","3":"1"},{"1":"AC002467.1","2":"chr7","3":"1"},{"1":"AC002470.1","2":"chr22","3":"1"},{"1":"AC002470.2","2":"chr22","3":"1"},{"1":"AC002472.1","2":"chr22","3":"1"},{"1":"AC002472.2","2":"chr22","3":"1"},{"1":"AC002472.3","2":"chr22","3":"1"},{"1":"AC002472.4","2":"chr22","3":"1"},{"1":"AC002480.1","2":"chr7","3":"1"},{"1":"AC002480.2","2":"chr7","3":"1"},{"1":"AC002486.1","2":"chr7","3":"1"},{"1":"AC002486.2","2":"chr7","3":"1"},{"1":"AC002487.1","2":"chr7","3":"1"},{"1":"AC002504.1","2":"chrX","3":"1"},{"1":"AC002511.1","2":"chr19","3":"1"},{"1":"AC002511.2","2":"chr19","3":"1"},{"1":"AC002511.3","2":"chr19","3":"1"},{"1":"AC002519.1","2":"chr16","3":"1"},{"1":"AC002519.2","2":"chr16","3":"2"},{"1":"AC002524.1","2":"chrX","3":"1"},{"1":"AC002525.1","2":"chr13","3":"1"},{"1":"AC002529.1","2":"chr7","3":"1"},{"1":"AC002540.1","2":"chr7","3":"1"},{"1":"AC002542.1","2":"chr7","3":"1"},{"1":"AC002542.2","2":"chr7","3":"1"},{"1":"AC002543.1","2":"chr7","3":"1"},{"1":"AC002545.1","2":"chr17","3":"1"},{"1":"AC002546.1","2":"chr17","3":"1"},{"1":"AC002550.1","2":"chr16","3":"1"},{"1":"AC002550.2","2":"chr16","3":"1"},{"1":"AC002551.1","2":"chr16","3":"1"},{"1":"AC002553.1","2":"chr17","3":"1"},{"1":"AC002553.2","2":"chr17","3":"1"},{"1":"AC002558.1","2":"chr17","3":"1"},{"1":"AC002558.2","2":"chr17","3":"1"},{"1":"AC002558.3","2":"chr17","3":"1"},{"1":"AC002563.1","2":"chr12","3":"1"},{"1":"AC002984.1","2":"chr19","3":"1"},{"1":"AC002985.1","2":"chr19","3":"1"},{"1":"AC002996.1","2":"chr12","3":"1"},{"1":"AC003001.1","2":"chrX","3":"2"},{"1":"AC003001.2","2":"chrX","3":"1"},{"1":"AC003002.1","2":"chr19","3":"1"},{"1":"AC003002.2","2":"chr19","3":"1"},{"1":"AC003002.3","2":"chr19","3":"1"},{"1":"AC003005.1","2":"chr19","3":"1"},{"1":"AC003005.2","2":"chr19","3":"1"},{"1":"AC003006.1","2":"chr19","3":"1"},{"1":"AC003009.1","2":"chr16","3":"1"},{"1":"AC003035.1","2":"chrX","3":"1"},{"1":"AC003035.2","2":"chrX","3":"1"},{"1":"AC003043.1","2":"chr17","3":"1"},{"1":"AC003043.2","2":"chr17","3":"1"},{"1":"AC003044.1","2":"chr7","3":"1"},{"1":"AC003045.1","2":"chr7","3":"1"},{"1":"AC003070.1","2":"chr17","3":"1"},{"1":"AC003070.2","2":"chr17","3":"1"},{"1":"AC003070.7","2":"chr17","3":"1"},{"1":"AC003071.1","2":"chr22","3":"1"},{"1":"AC003071.2","2":"chr22","3":"1"},{"1":"AC003072.1","2":"chr22","3":"1"},{"1":"AC003077.1","2":"chr7","3":"2"},{"1":"AC003080.1","2":"chr7","3":"1"},{"1":"AC003084.1","2":"chr7","3":"1"},{"1":"AC003086.1","2":"chr7","3":"1"},{"1":"AC003087.1","2":"chr7","3":"1"},{"1":"AC003092.1","2":"chr7","3":"2"},{"1":"AC003092.2","2":"chr7","3":"1"},{"1":"AC003093.1","2":"chr7","3":"1"},{"1":"AC003098.1","2":"chr17","3":"1"},{"1":"AC003101.1","2":"chr17","3":"1"},{"1":"AC003101.2","2":"chr17","3":"1"},{"1":"AC003101.3","2":"chr17","3":"1"},{"1":"AC003102.1","2":"chr17","3":"2"},{"1":"AC003112.1","2":"chr19","3":"2"},{"1":"AC003659.1","2":"chrX","3":"1"},{"1":"AC003665.1","2":"chr17","3":"3"},{"1":"AC003666.1","2":"chrX","3":"1"},{"1":"AC003669.1","2":"chrX","3":"1"},{"1":"AC003681.1","2":"chr22","3":"1"},{"1":"AC003682.1","2":"chr19","3":"1"},{"1":"AC003684.1","2":"chrX","3":"1"},{"1":"AC003685.1","2":"chrX","3":"1"},{"1":"AC003685.2","2":"chrX","3":"1"},{"1":"AC003686.1","2":"chr12","3":"1"},{"1":"AC003687.1","2":"chr17","3":"1"},{"1":"AC003688.1","2":"chr17","3":"1"},{"1":"AC003688.2","2":"chr17","3":"1"},{"1":"AC003950.1","2":"chr17","3":"1"},{"1":"AC003956.1","2":"chr19","3":"1"},{"1":"AC003957.1","2":"chr17","3":"1"},{"1":"AC003958.1","2":"chr17","3":"1"},{"1":"AC003958.2","2":"chr17","3":"1"},{"1":"AC003965.1","2":"chr16","3":"1"},{"1":"AC003965.2","2":"chr16","3":"1"},{"1":"AC003973.1","2":"chr19","3":"6"},{"1":"AC003973.2","2":"chr19","3":"1"},{"1":"AC003975.1","2":"chr7","3":"1"},{"1":"AC003982.1","2":"chr12","3":"1"},{"1":"AC003984.1","2":"chr7","3":"1"},{"1":"AC003985.1","2":"chr7","3":"2"},{"1":"AC003986.1","2":"chr7","3":"1"},{"1":"AC003986.2","2":"chr7","3":"1"},{"1":"AC003986.3","2":"chr7","3":"1"},{"1":"AC003988.1","2":"chr7","3":"1"},{"1":"AC003989.1","2":"chr7","3":"1"},{"1":"AC003989.2","2":"chr7","3":"1"},{"1":"AC003991.1","2":"chr7","3":"6"},{"1":"AC003991.2","2":"chr7","3":"1"},{"1":"AC003992.1","2":"chr7","3":"1"},{"1":"AC004000.1","2":"chrX","3":"1"},{"1":"AC004004.1","2":"chr19","3":"1"},{"1":"AC004006.1","2":"chr7","3":"1"},{"1":"AC004009.1","2":"chr7","3":"2"},{"1":"AC004009.2","2":"chr7","3":"1"},{"1":"AC004009.3","2":"chr7","3":"1"},{"1":"AC004012.1","2":"chr7","3":"1"},{"1":"AC004014.1","2":"chr7","3":"1"},{"1":"AC004014.2","2":"chr7","3":"1"},{"1":"AC004022.1","2":"chr7","3":"1"},{"1":"AC004022.2","2":"chr7","3":"1"},{"1":"AC004023.1","2":"chr7","3":"1"},{"1":"AC004024.1","2":"chr12","3":"1"},{"1":"AC004034.1","2":"chr16","3":"1"},{"1":"AC004039.1","2":"chr5","3":"1"},{"1":"AC004047.1","2":"chr4","3":"1"},{"1":"AC004052.1","2":"chr4","3":"3"},{"1":"AC004053.1","2":"chr4","3":"1"},{"1":"AC004054.1","2":"chr4","3":"1"},{"1":"AC004057.1","2":"chr4","3":"2"},{"1":"AC004062.1","2":"chr4","3":"1"},{"1":"AC004063.1","2":"chr4","3":"1"},{"1":"AC004066.1","2":"chr4","3":"1"},{"1":"AC004066.2","2":"chr4","3":"1"},{"1":"AC004067.1","2":"chr4","3":"1"},{"1":"AC004069.1","2":"chr4","3":"1"},{"1":"AC004069.2","2":"chr4","3":"1"},{"1":"AC004074.1","2":"chrX","3":"1"},{"1":"AC004076.1","2":"chr19","3":"1"},{"1":"AC004076.2","2":"chr19","3":"1"},{"1":"AC004080.1","2":"chr7","3":"1"},{"1":"AC004080.2","2":"chr7","3":"1"},{"1":"AC004080.3","2":"chr7","3":"1"},{"1":"AC004080.4","2":"chr7","3":"1"},{"1":"AC004080.5","2":"chr7","3":"1"},{"1":"AC004080.6","2":"chr7","3":"1"},{"1":"AC004080.7","2":"chr7","3":"1"},{"1":"AC004083.1","2":"chr8","3":"2"},{"1":"AC004086.1","2":"chr12","3":"2"},{"1":"AC004112.1","2":"chr7","3":"1"},{"1":"AC004129.1","2":"chr7","3":"1"},{"1":"AC004129.2","2":"chr7","3":"1"},{"1":"AC004129.3","2":"chr7","3":"1"},{"1":"AC004130.1","2":"chr7","3":"1"},{"1":"AC004134.1","2":"chr17","3":"1"},{"1":"AC004147.1","2":"chr17","3":"1"},{"1":"AC004147.2","2":"chr17","3":"1"},{"1":"AC004147.3","2":"chr17","3":"1"},{"1":"AC004147.4","2":"chr17","3":"1"},{"1":"AC004147.5","2":"chr17","3":"1"},{"1":"AC004148.1","2":"chr17","3":"1"},{"1":"AC004148.2","2":"chr17","3":"1"},{"1":"AC004151.1","2":"chr19","3":"6"},{"1":"AC004156.1","2":"chr19","3":"1"},{"1":"AC004156.2","2":"chr19","3":"1"},{"1":"AC004158.1","2":"chr16","3":"2"},{"1":"AC004160.1","2":"chr7","3":"6"},{"1":"AC004160.2","2":"chr7","3":"1"},{"1":"AC004217.1","2":"chr12","3":"1"},{"1":"AC004217.2","2":"chr12","3":"1"},{"1":"AC004221.1","2":"chr19","3":"1"},{"1":"AC004223.1","2":"chr17","3":"1"},{"1":"AC004223.2","2":"chr17","3":"1"},{"1":"AC004223.3","2":"chr17","3":"1"},{"1":"AC004223.4","2":"chr17","3":"1"},{"1":"AC004224.1","2":"chr16","3":"1"},{"1":"AC004224.2","2":"chr16","3":"1"},{"1":"AC004231.1","2":"chr17","3":"1"},{"1":"AC004231.3","2":"chr17","3":"1"},{"1":"AC004232.1","2":"chr16","3":"1"},{"1":"AC004232.2","2":"chr16","3":"1"},{"1":"AC004233.1","2":"chr16","3":"1"},{"1":"AC004233.2","2":"chr16","3":"1"},{"1":"AC004233.3","2":"chr16","3":"1"},{"1":"AC004241.1","2":"chr12","3":"3"},{"1":"AC004241.2","2":"chr12","3":"1"},{"1":"AC004241.3","2":"chr12","3":"1"},{"1":"AC004241.4","2":"chr12","3":"1"},{"1":"AC004241.5","2":"chr12","3":"1"},{"1":"AC004253.1","2":"chr17","3":"1"},{"1":"AC004253.2","2":"chr17","3":"1"},{"1":"AC004257.1","2":"chr19","3":"1"},{"1":"AC004263.1","2":"chr12","3":"1"},{"1":"AC004263.2","2":"chr12","3":"1"},{"1":"AC004264.1","2":"chr22","3":"1"},{"1":"AC004264.2","2":"chr22","3":"1"},{"1":"AC004381.1","2":"chr16","3":"1"},{"1":"AC004381.2","2":"chr16","3":"1"},{"1":"AC004381.3","2":"chr16","3":"1"},{"1":"AC004383.1","2":"chrX","3":"1"},{"1":"AC004386.1","2":"chrX","3":"1"},{"1":"AC004386.2","2":"chrX","3":"1"},{"1":"AC004408.1","2":"chr17","3":"1"},{"1":"AC004408.2","2":"chr17","3":"1"},{"1":"AC004415.1","2":"chr7","3":"1"},{"1":"AC004448.1","2":"chr17","3":"1"},{"1":"AC004448.2","2":"chr17","3":"6"},{"1":"AC004448.3","2":"chr17","3":"1"},{"1":"AC004448.4","2":"chr17","3":"1"},{"1":"AC004449.1","2":"chr19","3":"1"},{"1":"AC004453.1","2":"chr7","3":"1"},{"1":"AC004453.2","2":"chr7","3":"1"},{"1":"AC004461.1","2":"chr22","3":"1"},{"1":"AC004461.2","2":"chr22","3":"1"},{"1":"AC004464.1","2":"chr2","3":"1"},{"1":"AC004466.1","2":"chr12","3":"1"},{"1":"AC004466.2","2":"chr12","3":"1"},{"1":"AC004466.3","2":"chr12","3":"1"},{"1":"AC004467.1","2":"chrX","3":"1"},{"1":"AC004470.1","2":"chrX","3":"1"},{"1":"AC004471.1","2":"chr22","3":"1"},{"1":"AC004471.2","2":"chr22","3":"1"},{"1":"AC004475.1","2":"chr19","3":"1"},{"1":"AC004477.1","2":"chr17","3":"1"},{"1":"AC004477.2","2":"chr17","3":"1"},{"1":"AC004477.3","2":"chr17","3":"1"},{"1":"AC004485.1","2":"chr7","3":"1"},{"1":"AC004486.1","2":"chr12","3":"1"},{"1":"AC004490.1","2":"chr19","3":"1"},{"1":"AC004491.1","2":"chr7","3":"1"},{"1":"AC004492.1","2":"chr7","3":"1"},{"1":"AC004494.1","2":"chr16","3":"2"},{"1":"AC004494.2","2":"chr16","3":"1"},{"1":"AC004522.1","2":"chr7","3":"1"},{"1":"AC004522.2","2":"chr7","3":"1"},{"1":"AC004522.3","2":"chr7","3":"1"},{"1":"AC004522.4","2":"chr7","3":"1"},{"1":"AC004528.1","2":"chr19","3":"1"},{"1":"AC004528.2","2":"chr19","3":"1"},{"1":"AC004540.1","2":"chr7","3":"8"},{"1":"AC004540.2","2":"chr7","3":"2"},{"1":"AC004542.1","2":"chr22","3":"1"},{"1":"AC004542.2","2":"chr22","3":"1"},{"1":"AC004543.1","2":"chr7","3":"1"},{"1":"AC004549.1","2":"chr7","3":"1"},{"1":"AC004551.1","2":"chr12","3":"5"},{"1":"AC004551.2","2":"chr12","3":"1"},{"1":"AC004552.1","2":"chrX","3":"1"},{"1":"AC004554.1","2":"chrX","3":"1"},{"1":"AC004554.2","2":"chrX","3":"1"},{"1":"AC004584.1","2":"chr17","3":"1"},{"1":"AC004584.2","2":"chr17","3":"1"},{"1":"AC004584.3","2":"chr17","3":"1"},{"1":"AC004585.1","2":"chr17","3":"1"},{"1":"AC004585.2","2":"chr17","3":"1"},{"1":"AC004590.1","2":"chr17","3":"1"},{"1":"AC004593.1","2":"chr7","3":"1"},{"1":"AC004593.2","2":"chr7","3":"1"},{"1":"AC004593.3","2":"chr7","3":"1"},{"1":"AC004594.1","2":"chr7","3":"2"},{"1":"AC004596.1","2":"chr17","3":"1"},{"1":"AC004597.1","2":"chr19","3":"1"},{"1":"AC004623.1","2":"chr19","3":"1"},{"1":"AC004637.1","2":"chr19","3":"1"},{"1":"AC004672.1","2":"chr12","3":"1"},{"1":"AC004672.2","2":"chr12","3":"1"},{"1":"AC004674.1","2":"chrX","3":"1"},{"1":"AC004674.2","2":"chrX","3":"1"},{"1":"AC004678.1","2":"chr19","3":"1"},{"1":"AC004678.2","2":"chr19","3":"1"},{"1":"AC004687.1","2":"chr17","3":"1"},{"1":"AC004687.2","2":"chr17","3":"1"},{"1":"AC004687.3","2":"chr17","3":"1"},{"1":"AC004690.1","2":"chr7","3":"1"},{"1":"AC004690.2","2":"chr7","3":"1"},{"1":"AC004691.1","2":"chr7","3":"1"},{"1":"AC004691.2","2":"chr7","3":"1"},{"1":"AC004692.1","2":"chr7","3":"1"},{"1":"AC004692.2","2":"chr7","3":"3"},{"1":"AC004696.1","2":"chr19","3":"1"},{"1":"AC004696.2","2":"chr19","3":"1"},{"1":"AC004702.1","2":"chr17","3":"1"},{"1":"AC004704.1","2":"chr4","3":"1"},{"1":"AC004706.1","2":"chr17","3":"1"},{"1":"AC004706.2","2":"chr17","3":"1"},{"1":"AC004706.3","2":"chr17","3":"1"},{"1":"AC004707.1","2":"chr17","3":"1"},{"1":"AC004765.1","2":"chr12","3":"1"},{"1":"AC004771.1","2":"chr17","3":"1"},{"1":"AC004771.2","2":"chr17","3":"1"},{"1":"AC004771.3","2":"chr17","3":"1"},{"1":"AC004771.4","2":"chr17","3":"1"},{"1":"AC004771.5","2":"chr17","3":"1"},{"1":"AC004775.1","2":"chr5","3":"1"},{"1":"AC004777.1","2":"chr5","3":"1"},{"1":"AC004784.1","2":"chr19","3":"1"},{"1":"AC004790.1","2":"chr19","3":"1"},{"1":"AC004790.2","2":"chr19","3":"1"},{"1":"AC004792.1","2":"chr19","3":"1"},{"1":"AC004797.1","2":"chr17","3":"7"},{"1":"AC004801.1","2":"chr12","3":"1"},{"1":"AC004801.2","2":"chr12","3":"2"},{"1":"AC004801.3","2":"chr12","3":"1"},{"1":"AC004801.4","2":"chr12","3":"1"},{"1":"AC004801.5","2":"chr12","3":"2"},{"1":"AC004801.6","2":"chr12","3":"1"},{"1":"AC004801.7","2":"chr12","3":"1"},{"1":"AC004803.1","2":"chr12","3":"4"},{"1":"AC004805.1","2":"chr17","3":"1"},{"1":"AC004805.2","2":"chr17","3":"1"},{"1":"AC004805.3","2":"chr17","3":"1"},{"1":"AC004808.1","2":"chr7","3":"1"},{"1":"AC004808.2","2":"chr7","3":"1"},{"1":"AC004812.1","2":"chr12","3":"1"},{"1":"AC004812.2","2":"chr12","3":"1"},{"1":"AC004816.1","2":"chr14","3":"2"},{"1":"AC004816.2","2":"chr14","3":"1"},{"1":"AC004817.1","2":"chr14","3":"1"},{"1":"AC004817.2","2":"chr14","3":"1"},{"1":"AC004817.3","2":"chr14","3":"1"},{"1":"AC004817.4","2":"chr14","3":"1"},{"1":"AC004817.5","2":"chr14","3":"2"},{"1":"AC004825.1","2":"chr14","3":"1"},{"1":"AC004825.2","2":"chr14","3":"1"},{"1":"AC004825.3","2":"chr14","3":"1"},{"1":"AC004828.1","2":"chr14","3":"1"},{"1":"AC004828.2","2":"chr14","3":"1"},{"1":"AC004830.1","2":"chr7","3":"1"},{"1":"AC004832.1","2":"chr22","3":"2"},{"1":"AC004832.2","2":"chr22","3":"1"},{"1":"AC004832.3","2":"chr22","3":"1"},{"1":"AC004832.4","2":"chr22","3":"1"},{"1":"AC004832.5","2":"chr22","3":"1"},{"1":"AC004832.6","2":"chr22","3":"1"},{"1":"AC004834.1","2":"chr7","3":"3"},{"1":"AC004835.1","2":"chrX","3":"1"},{"1":"AC004835.2","2":"chrX","3":"1"},{"1":"AC004836.1","2":"chr7","3":"1"},{"1":"AC004837.1","2":"chr7","3":"1"},{"1":"AC004837.2","2":"chr7","3":"1"},{"1":"AC004837.3","2":"chr7","3":"1"},{"1":"AC004837.4","2":"chr7","3":"1"},{"1":"AC004839.1","2":"chr7","3":"1"},{"1":"AC004840.1","2":"chr7","3":"1"},{"1":"AC004840.2","2":"chr7","3":"1"},{"1":"AC004846.1","2":"chr14","3":"1"},{"1":"AC004846.2","2":"chr14","3":"1"},{"1":"AC004847.1","2":"chr7","3":"1"},{"1":"AC004852.1","2":"chr7","3":"1"},{"1":"AC004852.2","2":"chr7","3":"2"},{"1":"AC004852.3","2":"chr7","3":"1"},{"1":"AC004853.1","2":"chr7","3":"1"},{"1":"AC004854.1","2":"chr7","3":"1"},{"1":"AC004854.2","2":"chr7","3":"1"},{"1":"AC004858.1","2":"chr14","3":"1"},{"1":"AC004862.1","2":"chr7","3":"2"},{"1":"AC004863.1","2":"chr7","3":"1"},{"1":"AC004865.1","2":"chr1","3":"1"},{"1":"AC004865.2","2":"chr1","3":"1"},{"1":"AC004866.1","2":"chr7","3":"1"},{"1":"AC004866.2","2":"chr7","3":"1"},{"1":"AC004869.1","2":"chr7","3":"1"},{"1":"AC004869.2","2":"chr7","3":"1"},{"1":"AC004870.1","2":"chr7","3":"1"},{"1":"AC004870.2","2":"chr7","3":"3"},{"1":"AC004870.3","2":"chr7","3":"1"},{"1":"AC004870.4","2":"chr7","3":"2"},{"1":"AC004875.1","2":"chr7","3":"1"},{"1":"AC004877.1","2":"chr7","3":"1"},{"1":"AC004877.2","2":"chr7","3":"1"},{"1":"AC004879.1","2":"chr7","3":"1"},{"1":"AC004882.1","2":"chr22","3":"1"},{"1":"AC004882.2","2":"chr22","3":"1"},{"1":"AC004882.3","2":"chr22","3":"1"},{"1":"AC004884.1","2":"chr7","3":"1"},{"1":"AC004884.2","2":"chr7","3":"1"},{"1":"AC004888.1","2":"chr7","3":"1"},{"1":"AC004889.1","2":"chr7","3":"6"},{"1":"AC004890.1","2":"chr7","3":"1"},{"1":"AC004890.2","2":"chr7","3":"4"},{"1":"AC004890.3","2":"chr7","3":"1"},{"1":"AC004893.1","2":"chr7","3":"1"},{"1":"AC004893.2","2":"chr7","3":"1"},{"1":"AC004893.3","2":"chr7","3":"1"},{"1":"AC004895.1","2":"chr7","3":"2"},{"1":"AC004898.1","2":"chr7","3":"1"},{"1":"AC004899.1","2":"chr7","3":"1"},{"1":"AC004899.2","2":"chr7","3":"1"},{"1":"AC004900.1","2":"chr14","3":"1"},{"1":"AC004906.1","2":"chr7","3":"1"},{"1":"AC004908.1","2":"chr8","3":"1"},{"1":"AC004908.2","2":"chr8","3":"1"},{"1":"AC004908.3","2":"chr8","3":"1"},{"1":"AC004910.1","2":"chr7","3":"1"},{"1":"AC004911.1","2":"chr7","3":"1"},{"1":"AC004917.1","2":"chr7","3":"3"},{"1":"AC004918.1","2":"chr7","3":"1"},{"1":"AC004918.4","2":"chr7","3":"1"},{"1":"AC004920.1","2":"chr7","3":"1"},{"1":"AC004921.1","2":"chr7","3":"1"},{"1":"AC004921.2","2":"chr7","3":"1"},{"1":"AC004922.1","2":"chr7","3":"1"},{"1":"AC004923.1","2":"chr11","3":"2"},{"1":"AC004923.2","2":"chr11","3":"1"},{"1":"AC004923.3","2":"chr11","3":"1"},{"1":"AC004923.4","2":"chr11","3":"1"},{"1":"AC004925.1","2":"chr7","3":"1"},{"1":"AC004930.1","2":"chr7","3":"1"},{"1":"AC004936.1","2":"chr7","3":"1"},{"1":"AC004938.1","2":"chr7","3":"1"},{"1":"AC004938.2","2":"chr7","3":"1"},{"1":"AC004941.1","2":"chr7","3":"1"},{"1":"AC004941.2","2":"chr7","3":"1"},{"1":"AC004943.1","2":"chr16","3":"3"},{"1":"AC004943.2","2":"chr16","3":"2"},{"1":"AC004943.3","2":"chr16","3":"1"},{"1":"AC004944.1","2":"chr8","3":"1"},{"1":"AC004945.1","2":"chr7","3":"1"},{"1":"AC004946.1","2":"chr7","3":"1"},{"1":"AC004946.2","2":"chr7","3":"1"},{"1":"AC004947.1","2":"chr7","3":"3"},{"1":"AC004947.2","2":"chr7","3":"1"},{"1":"AC004947.3","2":"chr7","3":"1"},{"1":"AC004948.1","2":"chr7","3":"1"},{"1":"AC004949.1","2":"chr7","3":"1"},{"1":"AC004951.1","2":"chr7","3":"1"},{"1":"AC004951.2","2":"chr7","3":"1"},{"1":"AC004951.3","2":"chr7","3":"1"},{"1":"AC004951.4","2":"chr7","3":"1"},{"1":"AC004965.1","2":"chr7","3":"1"},{"1":"AC004967.1","2":"chr7","3":"1"},{"1":"AC004967.2","2":"chr7","3":"1"},{"1":"AC004969.1","2":"chr7","3":"1"},{"1":"AC004972.1","2":"chr7","3":"1"},{"1":"AC004973.1","2":"chrX","3":"1"},{"1":"AC004974.1","2":"chr14","3":"1"},{"1":"AC004975.1","2":"chr7","3":"1"},{"1":"AC004975.2","2":"chr7","3":"1"},{"1":"AC004980.1","2":"chr7","3":"2"},{"1":"AC004980.2","2":"chr7","3":"1"},{"1":"AC004980.3","2":"chr7","3":"1"},{"1":"AC004980.4","2":"chr7","3":"1"},{"1":"AC004982.1","2":"chr7","3":"1"},{"1":"AC004982.2","2":"chr7","3":"1"},{"1":"AC004986.1","2":"chr7","3":"1"},{"1":"AC004987.1","2":"chr7","3":"1"},{"1":"AC004987.2","2":"chr7","3":"1"},{"1":"AC004987.3","2":"chr7","3":"1"},{"1":"AC004987.4","2":"chr7","3":"1"},{"1":"AC004988.1","2":"chr7","3":"1"},{"1":"AC004990.1","2":"chr7","3":"2"},{"1":"AC004994.1","2":"chr7","3":"1"},{"1":"AC004997.1","2":"chr22","3":"1"},{"1":"AC005000.1","2":"chrX","3":"1"},{"1":"AC005000.2","2":"chrX","3":"1"},{"1":"AC005002.1","2":"chrX","3":"1"},{"1":"AC005002.2","2":"chrX","3":"1"},{"1":"AC005005.1","2":"chr22","3":"1"},{"1":"AC005005.2","2":"chr22","3":"1"},{"1":"AC005005.3","2":"chr22","3":"1"},{"1":"AC005005.4","2":"chr22","3":"1"},{"1":"AC005006.1","2":"chr22","3":"1"},{"1":"AC005008.1","2":"chr7","3":"1"},{"1":"AC005008.2","2":"chr7","3":"2"},{"1":"AC005009.1","2":"chr7","3":"1"},{"1":"AC005009.2","2":"chr7","3":"1"},{"1":"AC005011.1","2":"chr7","3":"1"},{"1":"AC005013.1","2":"chr7","3":"1"},{"1":"AC005014.1","2":"chr7","3":"1"},{"1":"AC005014.2","2":"chr7","3":"1"},{"1":"AC005014.3","2":"chr7","3":"1"},{"1":"AC005014.4","2":"chr7","3":"1"},{"1":"AC005019.1","2":"chr7","3":"1"},{"1":"AC005019.2","2":"chr7","3":"1"},{"1":"AC005020.1","2":"chr7","3":"1"},{"1":"AC005020.2","2":"chr7","3":"1"},{"1":"AC005021.1","2":"chr7","3":"1"},{"1":"AC005033.1","2":"chr2","3":"1"},{"1":"AC005034.1","2":"chr2","3":"1"},{"1":"AC005034.2","2":"chr2","3":"1"},{"1":"AC005034.3","2":"chr2","3":"1"},{"1":"AC005034.4","2":"chr2","3":"1"},{"1":"AC005034.5","2":"chr2","3":"1"},{"1":"AC005034.6","2":"chr2","3":"1"},{"1":"AC005037.1","2":"chr2","3":"1"},{"1":"AC005040.1","2":"chr2","3":"1"},{"1":"AC005040.2","2":"chr2","3":"2"},{"1":"AC005041.1","2":"chr2","3":"1"},{"1":"AC005041.2","2":"chr2","3":"1"},{"1":"AC005041.3","2":"chr2","3":"1"},{"1":"AC005041.4","2":"chr2","3":"1"},{"1":"AC005041.5","2":"chr2","3":"1"},{"1":"AC005042.1","2":"chr2","3":"1"},{"1":"AC005042.2","2":"chr2","3":"1"},{"1":"AC005042.3","2":"chr2","3":"1"},{"1":"AC005046.1","2":"chr7","3":"1"},{"1":"AC005050.1","2":"chr7","3":"1"},{"1":"AC005050.2","2":"chr7","3":"1"},{"1":"AC005050.3","2":"chr7","3":"1"},{"1":"AC005052.1","2":"chrX","3":"1"},{"1":"AC005052.2","2":"chrX","3":"1"},{"1":"AC005062.1","2":"chr7","3":"5"},{"1":"AC005064.1","2":"chr7","3":"1"},{"1":"AC005066.1","2":"chr8","3":"1"},{"1":"AC005070.1","2":"chr7","3":"1"},{"1":"AC005070.2","2":"chr7","3":"1"},{"1":"AC005070.3","2":"chr7","3":"1"},{"1":"AC005071.1","2":"chr7","3":"1"},{"1":"AC005072.1","2":"chr7","3":"1"},{"1":"AC005076.1","2":"chr7","3":"1"},{"1":"AC005077.1","2":"chr7","3":"1"},{"1":"AC005077.2","2":"chr7","3":"1"},{"1":"AC005077.3","2":"chr7","3":"2"},{"1":"AC005077.4","2":"chr7","3":"1"},{"1":"AC005081.1","2":"chr7","3":"1"},{"1":"AC005082.1","2":"chr7","3":"1"},{"1":"AC005082.2","2":"chr7","3":"1"},{"1":"AC005083.1","2":"chr7","3":"1"},{"1":"AC005084.1","2":"chr7","3":"1"},{"1":"AC005086.1","2":"chr7","3":"1"},{"1":"AC005086.2","2":"chr7","3":"1"},{"1":"AC005086.3","2":"chr7","3":"1"},{"1":"AC005086.4","2":"chr7","3":"1"},{"1":"AC005088.1","2":"chr7","3":"1"},{"1":"AC005089.1","2":"chr7","3":"1"},{"1":"AC005090.1","2":"chr7","3":"1"},{"1":"AC005091.1","2":"chr7","3":"1"},{"1":"AC005094.1","2":"chr7","3":"1"},{"1":"AC005096.1","2":"chr7","3":"1"},{"1":"AC005099.1","2":"chr7","3":"1"},{"1":"AC005100.1","2":"chr7","3":"3"},{"1":"AC005100.2","2":"chr7","3":"1"},{"1":"AC005102.1","2":"chr7","3":"1"},{"1":"AC005104.1","2":"chr2","3":"1"},{"1":"AC005104.2","2":"chr2","3":"1"},{"1":"AC005104.3","2":"chr2","3":"1"},{"1":"AC005105.1","2":"chr7","3":"1"},{"1":"AC005144.1","2":"chr17","3":"1"},{"1":"AC005153.1","2":"chr7","3":"1"},{"1":"AC005154.1","2":"chr7","3":"1"},{"1":"AC005154.2","2":"chr7","3":"1"},{"1":"AC005154.3","2":"chr7","3":"1"},{"1":"AC005154.4","2":"chr7","3":"1"},{"1":"AC005154.5","2":"chr7","3":"1"},{"1":"AC005156.1","2":"chr7","3":"1"},{"1":"AC005160.1","2":"chr7","3":"1"},{"1":"AC005162.1","2":"chr7","3":"1"},{"1":"AC005162.2","2":"chr7","3":"1"},{"1":"AC005162.3","2":"chr7","3":"1"},{"1":"AC005165.1","2":"chr7","3":"5"},{"1":"AC005165.2","2":"chr7","3":"1"},{"1":"AC005165.3","2":"chr7","3":"1"},{"1":"AC005176.1","2":"chr19","3":"3"},{"1":"AC005176.2","2":"chr19","3":"1"},{"1":"AC005178.1","2":"chr5","3":"1"},{"1":"AC005180.1","2":"chr17","3":"1"},{"1":"AC005180.2","2":"chr17","3":"1"},{"1":"AC005181.1","2":"chr17","3":"1"},{"1":"AC005183.1","2":"chr12","3":"1"},{"1":"AC005185.1","2":"chrX","3":"1"},{"1":"AC005186.1","2":"chr12","3":"3"},{"1":"AC005189.1","2":"chr7","3":"1"},{"1":"AC005191.1","2":"chrX","3":"1"},{"1":"AC005197.1","2":"chr19","3":"1"},{"1":"AC005208.1","2":"chr17","3":"2"},{"1":"AC005209.1","2":"chr17","3":"1"},{"1":"AC005220.1","2":"chr20","3":"1"},{"1":"AC005224.1","2":"chr17","3":"1"},{"1":"AC005224.2","2":"chr17","3":"1"},{"1":"AC005224.3","2":"chr17","3":"1"},{"1":"AC005225.1","2":"chr14","3":"1"},{"1":"AC005225.2","2":"chr14","3":"2"},{"1":"AC005225.3","2":"chr14","3":"1"},{"1":"AC005225.4","2":"chr14","3":"1"},{"1":"AC005229.1","2":"chr7","3":"1"},{"1":"AC005229.2","2":"chr7","3":"1"},{"1":"AC005229.3","2":"chr7","3":"1"},{"1":"AC005229.4","2":"chr7","3":"1"},{"1":"AC005229.5","2":"chr7","3":"1"},{"1":"AC005230.1","2":"chr14","3":"1"},{"1":"AC005232.1","2":"chr7","3":"3"},{"1":"AC005234.1","2":"chr2","3":"1"},{"1":"AC005234.2","2":"chr2","3":"1"},{"1":"AC005237.1","2":"chr2","3":"1"},{"1":"AC005237.2","2":"chr2","3":"1"},{"1":"AC005244.1","2":"chr17","3":"1"},{"1":"AC005244.2","2":"chr17","3":"1"},{"1":"AC005252.1","2":"chr12","3":"1"},{"1":"AC005252.2","2":"chr12","3":"1"},{"1":"AC005252.3","2":"chr12","3":"1"},{"1":"AC005252.4","2":"chr12","3":"1"},{"1":"AC005253.1","2":"chr19","3":"1"},{"1":"AC005253.2","2":"chr19","3":"1"},{"1":"AC005255.1","2":"chr19","3":"4"},{"1":"AC005255.2","2":"chr19","3":"1"},{"1":"AC005256.1","2":"chr19","3":"1"},{"1":"AC005258.1","2":"chr19","3":"1"},{"1":"AC005258.2","2":"chr19","3":"1"},{"1":"AC005261.1","2":"chr19","3":"4"},{"1":"AC005261.2","2":"chr19","3":"1"},{"1":"AC005261.3","2":"chr19","3":"1"},{"1":"AC005261.4","2":"chr19","3":"1"},{"1":"AC005261.5","2":"chr19","3":"1"},{"1":"AC005261.6","2":"chr19","3":"1"},{"1":"AC005262.1","2":"chr19","3":"1"},{"1":"AC005262.2","2":"chr19","3":"1"},{"1":"AC005264.1","2":"chr19","3":"1"},{"1":"AC005274.1","2":"chr17","3":"1"},{"1":"AC005277.1","2":"chr17","3":"1"},{"1":"AC005277.2","2":"chr17","3":"1"},{"1":"AC005280.1","2":"chr14","3":"3"},{"1":"AC005280.2","2":"chr14","3":"3"},{"1":"AC005280.3","2":"chr14","3":"1"},{"1":"AC005281.1","2":"chr7","3":"1"},{"1":"AC005288.1","2":"chr17","3":"1"},{"1":"AC005291.1","2":"chr17","3":"1"},{"1":"AC005291.2","2":"chr17","3":"1"},{"1":"AC005293.1","2":"chr12","3":"1"},{"1":"AC005294.1","2":"chr12","3":"1"},{"1":"AC005296.1","2":"chrX","3":"1"},{"1":"AC005297.1","2":"chrX","3":"1"},{"1":"AC005297.2","2":"chrX","3":"1"},{"1":"AC005297.3","2":"chrX","3":"1"},{"1":"AC005300.1","2":"chr22","3":"1"},{"1":"AC005301.1","2":"chr22","3":"1"},{"1":"AC005301.2","2":"chr22","3":"1"},{"1":"AC005303.1","2":"chr17","3":"1"},{"1":"AC005304.1","2":"chr17","3":"1"},{"1":"AC005304.2","2":"chr17","3":"1"},{"1":"AC005304.3","2":"chr17","3":"1"},{"1":"AC005306.1","2":"chr19","3":"1"},{"1":"AC005307.1","2":"chr19","3":"1"},{"1":"AC005323.1","2":"chr17","3":"3"},{"1":"AC005323.2","2":"chr17","3":"2"},{"1":"AC005324.1","2":"chr17","3":"2"},{"1":"AC005324.2","2":"chr17","3":"1"},{"1":"AC005324.3","2":"chr17","3":"1"},{"1":"AC005324.4","2":"chr17","3":"1"},{"1":"AC005324.5","2":"chr17","3":"1"},{"1":"AC005324.6","2":"chr17","3":"1"},{"1":"AC005326.1","2":"chr7","3":"1"},{"1":"AC005329.1","2":"chr19","3":"1"},{"1":"AC005329.2","2":"chr19","3":"1"},{"1":"AC005329.3","2":"chr19","3":"1"},{"1":"AC005330.1","2":"chr19","3":"1"},{"1":"AC005332.1","2":"chr17","3":"1"},{"1":"AC005332.2","2":"chr17","3":"1"},{"1":"AC005332.3","2":"chr17","3":"1"},{"1":"AC005332.4","2":"chr17","3":"1"},{"1":"AC005332.5","2":"chr17","3":"1"},{"1":"AC005332.6","2":"chr17","3":"1"},{"1":"AC005332.7","2":"chr17","3":"1"},{"1":"AC005336.1","2":"chr19","3":"1"},{"1":"AC005336.2","2":"chr19","3":"1"},{"1":"AC005336.3","2":"chr19","3":"1"},{"1":"AC005339.1","2":"chr19","3":"1"},{"1":"AC005341.1","2":"chr17","3":"1"},{"1":"AC005342.1","2":"chr12","3":"1"},{"1":"AC005342.2","2":"chr12","3":"1"},{"1":"AC005343.1","2":"chr12","3":"1"},{"1":"AC005343.4","2":"chr12","3":"1"},{"1":"AC005344.1","2":"chr12","3":"1"},{"1":"AC005345.1","2":"chrX","3":"1"},{"1":"AC005351.1","2":"chr5","3":"1"},{"1":"AC005355.1","2":"chr5","3":"1"},{"1":"AC005355.2","2":"chr5","3":"1"},{"1":"AC005357.1","2":"chr19","3":"1"},{"1":"AC005357.2","2":"chr19","3":"2"},{"1":"AC005358.1","2":"chr17","3":"1"},{"1":"AC005358.2","2":"chr17","3":"1"},{"1":"AC005363.1","2":"chr16","3":"1"},{"1":"AC005363.2","2":"chr16","3":"1"},{"1":"AC005377.1","2":"chr7","3":"1"},{"1":"AC005379.1","2":"chr19","3":"1"},{"1":"AC005381.1","2":"chr19","3":"2"},{"1":"AC005383.1","2":"chr10","3":"1"},{"1":"AC005387.1","2":"chr19","3":"1"},{"1":"AC005387.2","2":"chr19","3":"1"},{"1":"AC005391.1","2":"chr19","3":"1"},{"1":"AC005392.1","2":"chr19","3":"1"},{"1":"AC005392.2","2":"chr19","3":"1"},{"1":"AC005392.3","2":"chr19","3":"1"},{"1":"AC005393.1","2":"chr19","3":"1"},{"1":"AC005394.1","2":"chr19","3":"1"},{"1":"AC005394.2","2":"chr19","3":"1"},{"1":"AC005400.1","2":"chr7","3":"1"},{"1":"AC005410.1","2":"chr17","3":"2"},{"1":"AC005410.2","2":"chr17","3":"1"},{"1":"AC005412.1","2":"chr17","3":"1"},{"1":"AC005414.1","2":"chr12","3":"1"},{"1":"AC005476.1","2":"chr14","3":"1"},{"1":"AC005476.2","2":"chr14","3":"2"},{"1":"AC005479.1","2":"chr14","3":"1"},{"1":"AC005479.2","2":"chr14","3":"1"},{"1":"AC005480.1","2":"chr14","3":"1"},{"1":"AC005480.2","2":"chr14","3":"1"},{"1":"AC005481.1","2":"chr7","3":"1"},{"1":"AC005482.1","2":"chr7","3":"1"},{"1":"AC005486.1","2":"chr7","3":"1"},{"1":"AC005487.1","2":"chr7","3":"3"},{"1":"AC005495.1","2":"chr17","3":"1"},{"1":"AC005495.2","2":"chr17","3":"1"},{"1":"AC005498.1","2":"chr19","3":"1"},{"1":"AC005498.2","2":"chr19","3":"3"},{"1":"AC005498.3","2":"chr19","3":"1"},{"1":"AC005514.1","2":"chr19","3":"1"},{"1":"AC005515.1","2":"chr19","3":"3"},{"1":"AC005515.2","2":"chr19","3":"1"},{"1":"AC005518.1","2":"chr7","3":"1"},{"1":"AC005518.2","2":"chr7","3":"1"},{"1":"AC005519.1","2":"chr14","3":"1"},{"1":"AC005520.1","2":"chr14","3":"1"},{"1":"AC005520.2","2":"chr14","3":"2"},{"1":"AC005520.3","2":"chr14","3":"1"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[59479]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


### **3.2. Gene length in the GENCODE**

1. What is the average length of human genes?

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDMgPC0gZmlsdGVyKGQsIGQkZmVhdHVyZV90eXBlID09ICdnZW5lJylcbmQzJGdlbmVfdHlwZSA8LSBhcy5jaGFyYWN0ZXIoZG8uY2FsbChyYmluZC5kYXRhLmZyYW1lLCBzdHJzcGxpdChkMyRpbmZvLCAnZ2VuZV90eXBlXFxcXHMrXCInKSlbWzJdXSlcbmQzJGdlbmVfdHlwZSA8LSBhcy5jaGFyYWN0ZXIoZG8uY2FsbChyYmluZC5kYXRhLmZyYW1lLCBzdHJzcGxpdChkMyRnZW5lX3R5cGUsJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d3 <- filter(d, d$feature_type == 'gene')
d3$gene_type <- as.character(do.call(rbind.data.frame, strsplit(d3$info, 'gene_type\\s+"'))[[2]])
d3$gene_type <- as.character(do.call(rbind.data.frame, strsplit(d3$gene_type,'\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDMkZ2VuZV9sZW5ndGggPC0gKGQzJGVuZCAtIGQzJHN0YXJ0KVxubWVhbihkMyRnZW5lX2xlbmd0aClcbmBgYCJ9 -->

```r
d3$gene_length <- (d3$end - d3$start)
mean(d3$gene_length)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiWzFdIDMyNjI4LjAyXG4ifQ== -->

```
[1] 32628.02
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. Is the distribution of gene length differed by autosomal and sex chromosomes? Please calculate the quantiles (0%, 25%, 50%, 75%, 100%) of the gene length for each group.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxubXlfc3VtbWFyeSA8LSBmdW5jdGlvbihkMyl7XG4gIHggPC0gcXVhbnRpbGUoZDMkZ2VuZV9sZW5ndGgsIHByb2JzPSBjKDAsIDAuMjUsIDAuNSwgMC43NSwgMSkpXG4gIGRhdGFfZnJhbWUobWluID0geFsxXSwgUTEgPSB4WzJdLCBtZWRpYW4gPSB4WzNdLCBRMyA9IHhbNF0sIG1heCA9IHhbNV0pXG59XG5kM19jaHJvbV9xdWFuIDwtIGQzICU+JSBcbiBncm91cF9ieShjaHJvbSkgJT4lIFxuICBkbyhteV9zdW1tYXJ5KC4pKVxuYGBgIn0= -->

```r
my_summary <- function(d3){
  x <- quantile(d3$gene_length, probs= c(0, 0.25, 0.5, 0.75, 1))
  data_frame(min = x[1], Q1 = x[2], median = x[3], Q3 = x[4], max = x[5])
}
d3_chrom_quan <- d3 %>% 
 group_by(chrom) %>% 
  do(my_summary(.))
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiV2FybmluZzogYGRhdGFfZnJhbWUoKWAgd2FzIGRlcHJlY2F0ZWQgaW4gdGliYmxlIDEuMS4wLlxuUGxlYXNlIHVzZSBgdGliYmxlKClgIGluc3RlYWQuXG5UaGlzIHdhcm5pbmcgaXMgZGlzcGxheWVkIG9uY2UgZXZlcnkgOCBob3Vycy5cbkNhbGwgYGxpZmVjeWNsZTo6bGFzdF9saWZlY3ljbGVfd2FybmluZ3MoKWAgdG8gc2VlIHdoZXJlIHRoaXMgd2FybmluZyB3YXMgZ2VuZXJhdGVkLlxuIn0= -->

```
Warning: `data_frame()` was deprecated in tibble 1.1.0.
Please use `tibble()` instead.
This warning is displayed once every 8 hours.
Call `lifecycle::last_lifecycle_warnings()` to see where this warning was generated.
```



<!-- rnb-output-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDNfY2hyb21fcXVhblxuYGBgIn0= -->

```r
d3_chrom_quan
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjI1LCJuY29sIjo2LCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjI1IHggNiJdLCJHcm91cHMiOlsiY2hyb20gWzI1XSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJ1MlczMHNVVVJUSDc2NjdHeTRwVWxBWUJTSnBFYm1zczcvY0RKc3hJL3RocWZXd3ZUbnVycm0wT3l2clNrSllhL1FEb2NnUUh5b0pCVXNUczVRZXdub1FLdWpCQjFNeVJWRXJJcCtGN0tGeW03bm56SHAzS1A4QlhSZyszKytkYzgvY3VlZk1jaXRMUERhengwd0lTU0VHblk2a0dHVkpqSWZMM1hZM0lRYTliSFRFUUZJVk5zcEJXMlZoa3E4TStjckVHd1p2YlNRZnRWSFJWdFlrM2VGWVkyT05uVFVPMWpoWjQySk5BV3ZjekdyWXgzRHNhamgyTlJ6SFRMRXgyczVvQjZPZGpIWXh1b0RSN0RMS0dPMWg5RGxDOU9uS0J2SWxoUDc0NHpGZ0tiSVF4NCtpMzRuZWlqeEdrdU8wODRvMWNTZVFQTElBNlVRZTBhd2pWNU5YWlpsbVhhZUJtazR4U21MSVgwK1N1MFJ2elZsM2lwQ1ZSS21iWDhDV05jOENXM0FMcjNZREwwTnQrSWE5d0taTzRFMk12N2FFOFZ1QXQ0dUFWMW94YnhaNkt6Q21VcjAvaHI0Sy9XdU14K2ZIUm5COEZGaU8vaExtdWJISWxGb2YxMVEzaFhQa2JFaVViTmtIbTJEN25uZlJldk5QVzhBUFBnRGY4eGg4eHkxZzN6NWcvd3p3MlVOZzcyN2d5ejh3cjM4YjV2a0I3TGJoL0svQW9SNmM3OE44T0w4UDIrWkpEZWI5aWI0RFdCRUQzc2U0THZ1YVpYY2srbjFEc21XZitwMUZ0Mi91VEN2bFJBVlBPVE1CMnpxM0hUamVQMEk1alcweElZNVJmbmhWQmZHdVRvZ2J0bEl1bFBaU1R0bWdEU1luSWU5NE5zejdQTDlBK2VWZEJ2Z281UC8wSHZKTWY0TThzOWt3UHJVWm5udmRBeHhkaHJnM2I5Y3N1MnUxNGRlOVpNb3V1QWJPS3BzbUZFNjNVZkp0OVBNU2JOeEhTbWN6L1Z3Rjl5TDlXeEQyTHc5UUZzL1RkaEh5aDlzcHVhSmh5bDN3OVFzSFY1WXBEOVg0NFA3ZGFzbzl6bE9VQis0OWd2eS83bEM2aG1CK1VlVlRTcUY5Q2VhSHZPQmJuRXIvOEIwbkliK3dJMEtaMnoyN1J0a04rZFpFeDIvby8ybmFEOG5uSVc5UXJGZlBRK3FnMlNkR1JVdE5SRDRxYWNKVEkrR0xGdlVJbFNaZmVxVlc4WGo4dTZZdXBrVCsya2c0cFBaa0tDQ3BSNUFLOWJockN2bDlBWEYxM0pZSUZodi9zZUJONGJwb0lDekp6OWNyQjM2alp1bTZpR1lnbzBGUzF1dkw4OVkyU0JmeTdKenlmdlErL05KUVp6TGFwSGtabzdwVXYzUStJUG5WVnd1SzFmNGdtblI1WStpK1dPb2lBU21xYnFROFdtK0pocU9pR21mMmhvUHFDSDA1c3ZJWE4yOSt2K2dNQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["chrom"],"name":[1],"type":["chr"],"align":["left"]},{"label":["min"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Q1"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["median"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Q3"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["max"],"name":[6],"type":["dbl"],"align":["right"]}],"data":[{"1":"chr1","2":"40","3":"566.50","4":"4477.0","5":"25584.50","6":"1551956"},{"1":"chr10","2":"53","3":"571.50","4":"4257.5","5":"31054.25","6":"1825171"},{"1":"chr11","2":"49","3":"816.00","4":"3780.5","5":"19781.00","6":"2172910"},{"1":"chr12","2":"27","3":"596.50","4":"4507.5","5":"28492.00","6":"1258197"},{"1":"chr13","2":"47","3":"484.00","4":"3027.0","5":"30812.00","6":"1475061"},{"1":"chr14","2":"7","3":"338.50","4":"1827.0","5":"19123.00","6":"1697917"},{"1":"chr15","2":"16","3":"474.00","4":"3349.0","5":"26148.00","6":"949079"},{"1":"chr16","2":"50","3":"763.50","4":"3693.5","5":"19847.25","6":"2473536"},{"1":"chr17","2":"27","3":"670.25","4":"4047.0","5":"18157.50","6":"1161877"},{"1":"chr18","2":"49","3":"578.25","4":"3090.0","5":"27870.50","6":"1195706"},{"1":"chr19","2":"27","3":"935.75","4":"6397.5","5":"19176.75","6":"485248"},{"1":"chr2","2":"36","3":"505.00","4":"3595.0","5":"33349.25","6":"1900278"},{"1":"chr20","2":"50","3":"548.00","4":"4597.0","5":"24782.00","6":"2057828"},{"1":"chr21","2":"54","3":"499.00","4":"2585.5","5":"23385.00","6":"1216866"},{"1":"chr22","2":"32","3":"518.00","4":"3441.5","5":"18571.25","6":"760615"},{"1":"chr3","2":"24","3":"530.00","4":"4775.0","5":"36607.00","6":"1743269"},{"1":"chr4","2":"22","3":"570.00","4":"4018.0","5":"38440.50","6":"1506191"},{"1":"chr5","2":"42","3":"524.00","4":"3602.0","5":"35750.00","6":"1553082"},{"1":"chr6","2":"53","3":"567.50","4":"3359.0","5":"24354.50","6":"1987245"},{"1":"chr7","2":"11","3":"506.25","4":"3251.0","5":"26516.75","6":"2304996"},{"1":"chr8","2":"49","3":"536.00","4":"3195.5","5":"28811.00","6":"2059619"},{"1":"chr9","2":"49","3":"569.50","4":"3278.0","5":"24624.00","6":"2298477"},{"1":"chrM","2":"58","3":"67.00","4":"70.0","5":"683.00","6":"1811"},{"1":"chrX","2":"47","3":"435.00","4":"1679.5","5":"14829.25","6":"2241764"},{"1":"chrY","2":"63","3":"733.00","4":"2202.0","5":"10120.00","6":"741998"}],"options":{"columns":{"min":{},"max":[10],"total":[6]},"rows":{"min":[10],"max":[10],"total":[25]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


3. Is the distribution of gene length differed by gene biotype? Please calculate the quantiles (0%, 25%, 50%, 75%, 100%) of the gene length for each group.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxubXlfc3VtbWFyeSA8LSBmdW5jdGlvbihkMyl7XG4gIHggPC0gcXVhbnRpbGUoZDIkZ2VuZV9sZW5ndGgsIHByb2JzPSBjKDAsIDAuMjUsIDAuNSwgMC43NSwgMSkpXG4gIGRhdGFfZnJhbWUobWluID0geFsxXSwgUTEgPSB4WzJdLCBtZWRpYW4gPSB4WzNdLCBRMyA9IHhbNF0sIG1heCA9IHhbNV0pXG59XG5kM190eXBlX3F1YW4gPC0gZDMgJT4lIFxuICBncm91cF9ieShnZW5lX3R5cGUpICU+JSBcbiAgZG8obXlfc3VtbWFyeSguKSlcbmBgYCJ9 -->

```r
my_summary <- function(d3){
  x <- quantile(d2$gene_length, probs= c(0, 0.25, 0.5, 0.75, 1))
  data_frame(min = x[1], Q1 = x[2], median = x[3], Q3 = x[4], max = x[5])
}
d3_type_quan <- d3 %>% 
  group_by(gene_type) %>% 
  do(my_summary(.))
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbldhcm5pbmc6IFVua25vd24gb3IgdW5pbml0aWFsaXNlZCBjb2x1bW46IGBnZW5lX2xlbmd0aGAuXG5XYXJuaW5nOiBVbmtub3duIG9yIHVuaW5pdGlhbGlzZWQgY29sdW1uOiBgZ2VuZV9sZW5ndGhgLlxuV2FybmluZzogVW5rbm93biBvciB1bmluaXRpYWxpc2VkIGNvbHVtbjogYGdlbmVfbGVuZ3RoYC5cbiJ9 -->

```
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
Warning: Unknown or uninitialised column: `gene_length`.
```



<!-- rnb-output-end -->

<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDNfdHlwZV9xdWFuXG5gYGAifQ== -->

```r
d3_type_quan
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjQwLCJuY29sIjo2LCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjQwIHggNiJdLCJHcm91cHMiOlsiZ2VuZV90eXBlIFs0MF0iXX19LCJyZGYiOiJINHNJQUFBQUFBQUFCdTFZM1dyYk1CUldITnRKREVsTE4zcTVQeWowcHFGZFdrWXZSekxHQ2kxYktLRjNSclcxVk15V2pDVnZ6VzYyWjlsVDdUSDJCTTBrMTRwdE9jM0ZXR0FaRGpqNnpxZFA4amxIT2dKNVBMb2FPRmNPQUtBSnpFWUROQzBCZ1RWOGYzcDhDb0JwQ0tNQlROQ1I3YTBRUFJMQUZzKzJlUGF6anM2N3QrN1FuU0tDTW1JckpTS0dFcDhXYUtrYnVScHhwZzg4cXc3c0NucnBiQk45OEtTcXN3UGlqUzllWjVZVjR0eG9oNWg1Ym02M3pya2JsMDJlbTdzUkRXWWhqYU1iN0ZWZjh6aUtxWWNZUTM2MXJ5ZjZPTUxFOWFpUHlUUmpuWXF1SGVOcituVVdLdHNzT0xNbDhaTG9tQWNMMGJGQ3FEWWp0TmhGY3NOa09XNWV2aG1xbEY2T3l3c3BpWkZPbEZjc0phcUxJK2lKcmx1eU9NOTREQW56Uk53eWJ3OW44RWxSbHhETVlUeXJxbDZVVlN2bWU1b3FBOGhYdi9aNVFiWnl2cDBIZmRwZE9hNzlHU1pCdXNlQTBaTWw5ZTJYckwvV2o3cjlzMVk3c3l3Q1E4UkErYnd5RHZkcTlFOGdBTzdxamY5MzJ2dU5iOHhCZWE4M1g1N3MxWENUWUYwVGE2K0prOFVoVk1PTmdIVk5yTDBtWHVXblVBMDNBZFkxc2U2YU1JOE9GOGRRamY4bm5OWk8rYUxvQlpDcGk2SWlIUjl5MlA4WWl6dWtKdS9FOUV0ZjNTMjc0akcraTcvNWZQNVQyMHUyR2lBdi9TNmZSVWpWY0loSkJvMFBSeG15UStSam1QT0RoUmplTG5HNlJTT09LUkUrR1BLN25LVzUzNGcxWWpzaDBtZi93THRKeUtlRDQ0R01NZTIvLzNVenZGL0F0aGFRcFZ4RlpJb1gzekdzQUY2aklETjZJamxwYnZwUmpBbFh5UlFzNjNQS29kSTVIZzBVa3dZSDduNERaR1NhZkk4VUFBQT0ifQ== -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_type"],"name":[1],"type":["chr"],"align":["left"]},{"label":["min"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Q1"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["median"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Q3"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["max"],"name":[6],"type":["dbl"],"align":["right"]}],"data":[{"1":"IG_C_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_C_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_D_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_J_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_J_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_V_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"IG_V_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"lncRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"miRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"misc_RNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"Mt_rRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"Mt_tRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"polymorphic_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"processed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"protein_coding","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"ribozyme","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"rRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"rRNA_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"scaRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"scRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"snoRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"snRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"sRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TEC","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_C_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_D_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_J_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_J_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_V_gene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"TR_V_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"transcribed_processed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"transcribed_unitary_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"transcribed_unprocessed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"translated_processed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"translated_unprocessed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"unitary_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"unprocessed_pseudogene","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"},{"1":"vaultRNA","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA"}],"options":{"columns":{"min":{},"max":[10],"total":[6]},"rows":{"min":[10],"max":[10],"total":[40]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



### **3.3. Transcript support levels (TSL)**

The GENCODE TSL provides a consistent method of evaluating the level of support that a GENCODE transcript annotation is actually expressed in humans.

1. With `transcript`, how many transcripts are categorized for each TSL?

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGdyb3VwX2J5KHRyYW5zY3JpcHRfc3VwcG9ydF9sZXZlbCwgdHJhbnNjcmlwdF90eXBlKSAlPiUgY291bnQoKVxuYGBgIn0= -->

```r
d2 %>% group_by(transcript_support_level, transcript_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjkxLCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjkxIHggMyJdLCJHcm91cHMiOlsidHJhbnNjcmlwdF9zdXBwb3J0X2xldmVsLCB0cmFuc2NyaXB0X3R5cGUgWzkxXSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJ1MVh6VzdUUUJBZXgzYWFIMmhEQ3hVWG9GU2dJa1FESmVWUWhJU2dSWWhLL0tncW9ZaUQ1ZG9tTlRocnk3c3BEUmQ0QVY2QUd4S2NRRnk1Y09ESU8vQUNTTHhEdzZ5OWJtd25CRUphdFlnZUpwNzk5cHZabmI4a1hscFlxUlJXQ2dBZ2d5SkpJS3VvZ2pwL2IyNTJEa0RKNEVJQ0JmTDh1WUdrc1lBSlVFSjVGRzNNREtaY0hFeXBES2JNOXFOYzJuRWxWN09JcGRubXhIK3l6dHk1dHEvdG9wWWM1Znl0bTlxOHhrc2tnS3hEaktVdGczSFBkWnAxMS9mV2JFUHpxTlV3M1JoMzJQTmRadGxFTTF6VEpqV0JUakJmSjlUdzdWWEwxSkJoV0pSeUxXMTlQTTVyRUp2cGZyT1ROWmxrOWZBMytNM2w1UnZ6KzBGc2R4QjdxMHM2R3A0RDFlMmRnRDFRZ2hNQjA5Rlo3MlBIKzh0VlA2azUzTU54OTdRcGZ0djNDTmM3RFZWSzJweC9KODJqdnp5cXp3S01CRUJYM2tLNnFSZlRob3VkaGdjUjd1cXRtamF1L203TTFicmRYdVRxTmpXMDlucm9OdFA4NUpMdFhDTVZPbmc1cktYN3ZGbTMvcnpWc3RUUVk5SFJXS2haU3R6NFZxd25GZHExUC9QTFM2bHZIUVFXMGtDeVlnSFFXUnlFcTJsZWwrTHMxa1NjakUxRVQrTGZ6a1J1WFc4NFFmTmcrd2IvWlBnYkMyU1hlVEFBN0FFK3o2SmNScmdJVUh3TndRdk0xRWJJQTI1M0RPVXJYbUUweFBLVS96ZENtVUU1Z2tLd3FsOFFlaS9XL053cndoNTlGYi9qOHhEQUdUTmNRd1hsQXNvYndlR0M3MVdUMTNtY29ZL2lPWUhueFJQdDRUektLWlJ2QWp1QWNwWFBoK0NkRnZheTJIK0Y4aG0zUGlIbElhYmlyYmozZUNoVEh6QVBUV0dUQys4bC9SQnhvYjM4RWUyYzhHNEtQei9MT3hibHJuZys0UVZFbHp5SG1CUDVoZkIvSDJkS0RYMGszaEJWdzlFcFQxNHBCaFpNbmVubHg3Nk8wNWFrNTMzM1daa2dUa1h0TWkveG85VnF2VXY3alVpbElQZ1FQQnIxb01jMDJ2QTgxMmVhWTYxYlRqUUlzWDNXOUtKK2tiQ1lzSms2WU1qMW1PMFNQQ0l6RnVZakVZTGtwNENSQnVGWE1xZU50UVo1T3MzclhSQTFBUkdMSkhveDB1WHdTS1VsWEtuUmw0ZEZhbmI3QjgzUlY3Y2lHTWI4QkpHWFBkOG1MTW9ub3JUTVhLWkh2SUxoT2hFU3hBYWJQd0ZzTVFkVTF3OEFBQT09In0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["transcript_support_level"],"name":[1],"type":["chr"],"align":["left"]},{"label":["transcript_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"1","2":"IG_C_gene","3":"1"},{"1":"1","2":"lncRNA","3":"1620"},{"1":"1","2":"polymorphic_pseudogene","3":"30"},{"1":"1","2":"protein_coding","3":"29783"},{"1":"1","2":"transcribed_processed_pseudogene","3":"42"},{"1":"1","2":"transcribed_unitary_pseudogene","3":"58"},{"1":"1","2":"transcribed_unprocessed_pseudogene","3":"267"},{"1":"2","2":"lncRNA","3":"2970"},{"1":"2","2":"polymorphic_pseudogene","3":"3"},{"1":"2","2":"protein_coding","3":"10104"},{"1":"2","2":"TEC","3":"1"},{"1":"2","2":"transcribed_processed_pseudogene","3":"65"},{"1":"2","2":"transcribed_unitary_pseudogene","3":"29"},{"1":"2","2":"transcribed_unprocessed_pseudogene","3":"200"},{"1":"3","2":"lncRNA","3":"4626"},{"1":"3","2":"polymorphic_pseudogene","3":"1"},{"1":"3","2":"protein_coding","3":"2419"},{"1":"3","2":"TEC","3":"2"},{"1":"3","2":"transcribed_processed_pseudogene","3":"49"},{"1":"3","2":"transcribed_unitary_pseudogene","3":"21"},{"1":"3","2":"transcribed_unprocessed_pseudogene","3":"110"},{"1":"4","2":"lncRNA","3":"1472"},{"1":"4","2":"protein_coding","3":"683"},{"1":"4","2":"transcribed_processed_pseudogene","3":"21"},{"1":"4","2":"transcribed_unitary_pseudogene","3":"9"},{"1":"4","2":"transcribed_unprocessed_pseudogene","3":"60"},{"1":"5","2":"IG_C_gene","3":"1"},{"1":"5","2":"IG_V_gene","3":"3"},{"1":"5","2":"lncRNA","3":"3048"},{"1":"5","2":"polymorphic_pseudogene","3":"17"},{"1":"5","2":"protein_coding","3":"10340"},{"1":"5","2":"TEC","3":"3"},{"1":"5","2":"transcribed_processed_pseudogene","3":"51"},{"1":"5","2":"transcribed_unitary_pseudogene","3":"48"},{"1":"5","2":"transcribed_unprocessed_pseudogene","3":"161"},{"1":"5","2":"translated_processed_pseudogene","3":"1"},{"1":"5","2":"unprocessed_pseudogene","3":"1"},{"1":"gene_id","2":"IG_C_gene","3":"5"},{"1":"gene_id","2":"lncRNA","3":"8770"},{"1":"gene_id","2":"polymorphic_pseudogene","3":"18"},{"1":"gene_id","2":"processed_pseudogene","3":"21"},{"1":"gene_id","2":"protein_coding","3":"2860"},{"1":"gene_id","2":"rRNA","3":"1"},{"1":"gene_id","2":"rRNA_pseudogene","3":"9"},{"1":"gene_id","2":"snRNA","3":"1"},{"1":"gene_id","2":"TEC","3":"17"},{"1":"gene_id","2":"transcribed_processed_pseudogene","3":"47"},{"1":"gene_id","2":"transcribed_unitary_pseudogene","3":"36"},{"1":"gene_id","2":"transcribed_unprocessed_pseudogene","3":"219"},{"1":"gene_id","2":"translated_processed_pseudogene","3":"1"},{"1":"gene_id","2":"unitary_pseudogene","3":"12"},{"1":"gene_id","2":"unprocessed_pseudogene","3":"63"},{"1":"__NA__","2":"IG_C_gene","3":"7"},{"1":"__NA__","2":"IG_C_pseudogene","3":"9"},{"1":"__NA__","2":"IG_D_gene","3":"37"},{"1":"__NA__","2":"IG_J_gene","3":"18"},{"1":"__NA__","2":"IG_J_pseudogene","3":"3"},{"1":"__NA__","2":"IG_pseudogene","3":"1"},{"1":"__NA__","2":"IG_V_gene","3":"141"},{"1":"__NA__","2":"IG_V_pseudogene","3":"188"},{"1":"__NA__","2":"lncRNA","3":"2487"},{"1":"__NA__","2":"miRNA","3":"1881"},{"1":"__NA__","2":"misc_RNA","3":"2212"},{"1":"__NA__","2":"Mt_rRNA","3":"2"},{"1":"__NA__","2":"Mt_tRNA","3":"22"},{"1":"__NA__","2":"polymorphic_pseudogene","3":"22"},{"1":"__NA__","2":"processed_pseudogene","3":"10156"},{"1":"__NA__","2":"protein_coding","3":"1657"},{"1":"__NA__","2":"pseudogene","3":"18"},{"1":"__NA__","2":"ribozyme","3":"8"},{"1":"__NA__","2":"rRNA","3":"51"},{"1":"__NA__","2":"rRNA_pseudogene","3":"491"},{"1":"__NA__","2":"scaRNA","3":"49"},{"1":"__NA__","2":"scRNA","3":"1"},{"1":"__NA__","2":"snoRNA","3":"942"},{"1":"__NA__","2":"snRNA","3":"1900"},{"1":"__NA__","2":"sRNA","3":"5"},{"1":"__NA__","2":"TEC","3":"1041"},{"1":"__NA__","2":"TR_C_gene","3":"6"},{"1":"__NA__","2":"TR_D_gene","3":"4"},{"1":"__NA__","2":"TR_J_gene","3":"79"},{"1":"__NA__","2":"TR_J_pseudogene","3":"4"},{"1":"__NA__","2":"TR_V_gene","3":"106"},{"1":"__NA__","2":"TR_V_pseudogene","3":"33"},{"1":"__NA__","2":"transcribed_processed_pseudogene","3":"599"},{"1":"__NA__","2":"transcribed_unitary_pseudogene","3":"115"},{"1":"__NA__","2":"transcribed_unprocessed_pseudogene","3":"895"},{"1":"__NA__","2":"translated_unprocessed_pseudogene","3":"2"},{"1":"__NA__","2":"unitary_pseudogene","3":"85"},{"1":"__NA__","2":"unprocessed_pseudogene","3":"2565"},{"1":"__NA__","2":"vaultRNA","3":"1"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[91]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. From the first question, please count the number of transcript for each TSL by gene biotype.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxudGFibGUoZDIkdHJhbnNjcmlwdF9zdXBwb3J0X2xldmVsKVxuYGBgIn0= -->

```r
table(d2$transcript_support_level)
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiXG4gICAgICAgMSAgICAgICAgMiAgICAgICAgMyAgICAgICAgNCAgICAgICAgNSBnZW5lX2lkICAgICAgICBOQSBcbiAgIDMxODAxICAgIDEzMzcyICAgICA3MjI4ICAgICAyMjQ1ICAgIDEzNjc0ICAgIDEyMDgwICAgIDI3ODQzIFxuIn0= -->

```

       1        2        3        4        5 gene_id        NA 
   31801    13372     7228     2245    13674    12080    27843 
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->




<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcih0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgPT0gMSkgJT4lIGdyb3VwX2J5KGdlbmVfdHlwZSwgZmVhdHVyZV90eXBlKSAlPiUgY291bnQoKVxuYGBgIn0= -->

```r
d2 %>% filter(transcript_support_level == 1) %>% group_by(gene_type, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjcsIm5jb2wiOjMsInN1bW1hcnkiOnsiQSB0aWJibGUiOlsiNyB4IDMiXSwiR3JvdXBzIjpbImdlbmVfdHlwZSwgZmVhdHVyZV90eXBlIFs3XSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJyMVNRVXZETUJSKzNkcU5WYWNEd2FPSVIyRzl6TXU4eVFUeElqSUVkeXRabW0zQkxnbEppdTdtMzk1bE5XbVRzVlhQQnBLOGZQbmV5L3RlM3ZSeE5vcG5NUUMwSVF3Q2FFZkdoR2p5T3I0YkE0UXRjd2dnaEo3ZHZ3enBvbUlDRE16c3VvdmU4MU02U1plRUVRZDBjb2FuTHcvdWRDbDR2bGx6S1ZZVXAwS1JJdU1IM0RNaHVTYVVwWmhubEMwZGVxMGxZZ3BMT2lkWmFoaVlLR1d0cHZmVklhOWdWQ081K2MyNk9XYjlHZTlZVXV3OWhQNVhCS0JmWldHckRwMDNxeEJBdjV2OTFzeDdBNTgwZmlYQ09WTEtwZS9CT0VNYUpRdUoxcVJCNzBuK21UQ0RLL2RXNjlzc1pWbHVtM0U5YVZEM1J1MXRTNVhxamZDVlBWMFFwQXQ1aEFYTUxMdEd1QzRYbW5KbUFyWnNDMFdOaEFQWkFNNExaaFBJaG5oVnNJL2h5SXFxcTFLTnZyTzdCM2E3ZmpJc1hhaklkeU5oUzdydmhTaEhjNUw3NWpQVnFIUW1RbEsyL3hDRHFrUnpqVHd2eGp6M1NLVU5kai9UVlZSU09RTUFBQT09In0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_type"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"IG_C_gene","2":"transcript","3":"1"},{"1":"lncRNA","2":"transcript","3":"1620"},{"1":"polymorphic_pseudogene","2":"transcript","3":"30"},{"1":"protein_coding","2":"transcript","3":"29783"},{"1":"transcribed_processed_pseudogene","2":"transcript","3":"42"},{"1":"transcribed_unitary_pseudogene","2":"transcript","3":"58"},{"1":"transcribed_unprocessed_pseudogene","2":"transcript","3":"267"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[7]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcih0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgPT0gJ05BJykgJT4lIGdyb3VwX2J5KGdlbmVfdHlwZSwgZmVhdHVyZV90eXBlKSAlPiUgY291bnQoKVxuYGBgIn0= -->

```r
d2 %>% filter(transcript_support_level == 'NA') %>% group_by(gene_type, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjM5LCJuY29sIjozLCJzdW1tYXJ5Ijp7IkEgdGliYmxlIjpbIjM5IHggMyJdLCJHcm91cHMiOlsiZ2VuZV90eXBlLCBmZWF0dXJlX3R5cGUgWzM5XSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJ1MVZ6VzdVTUJCMk5rbTdHd0ZiS05vakFpVFVVMWRDN2FWSHRJc1FsZmpScWl6bEZMbmVkR3ZJMnBIdEFPRUNMOEJiY0VSY2UrbUw4QXk4UTVkeFlqZC8yM0pHNm1GaXorZHZKak8yWnp3WkgrNEVod0ZDeUVXZTR5RFhoeW55UjYvM2R2Y1E4anFnT01oRFBUMStCdEptemtSb0EyVExMUFNlUHd0SDRUeGlrUUg2T1pESUtKM3hDcXg1NDdBQjdEY045OXVHTndGZTZXM2FOSjYyZVdzeEk1T1hUNHptTDJpcGRCZFVrckRVMTErb1VOUlZWYXFEaE1mWmdvdmtoSkwyYis0bWdwTkl5bWpXWHJzRmF5cWlMQ1I4UnRuY29FR0wxeFgwaUgvSkZsYjNLc0gwOVh4RmRwTGdTbmF5a3VxYVpMeTZ4RXJGaytYY1BYZzZzbHQ2TUtrZnBBYkdUYUIrWWpuUVBoeUFwMDNlaXNPNXJ3Um1ra0RlZXQ4dTM4RjdWVjdLcU1JaWE3TWUxbGxYK0h1UU0yT3Mva1c4YytuUEJsZmFkVC9pTk00dlQ3MVdBaHRpb3E2UmErUy9RYUFKNTdjWXVxSXViNUJIdWpyTWErQ0FmQWM1ZzZWVG9MeURBdmdCdW40K0JvVnMvWVNPbEJtYkxzZ09tUDJCOFhGaDcvNEN1MWczS21oUHQzWDcwbjBLNUpVWjMrdXlCWmR2WVpUQS8ycjh2NEZBL2NKSDdibnlTWXlsTk9WbndXQ0dGUjRlQ3d3OXRrN3ZDZjVweUFDWEp0Zk9OL2dzbDh2ZlRiK1d0SkVuYjZ4MTZZY3FTMno5M3ppT3NFcEZEWE1ZZk00Yjd0WjVvaWhuNExDeldXUmZDOWdSRGFDZk1oM0FiSnVjcE96RDlxNU95cHdBTXBFNzVxVHMzQzErNlMyTks5OCtFQkdiMDR1TzVjZjRLSXJ0aXdXN2tlYzVUQVJsRnhjQ1VEbFVYR0hMQ3dpUExaTG5oczcvQWwwRUZXWlNDQUFBIn0= -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["gene_type"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"IG_C_gene","2":"transcript","3":"7"},{"1":"IG_C_pseudogene","2":"transcript","3":"9"},{"1":"IG_D_gene","2":"transcript","3":"37"},{"1":"IG_J_gene","2":"transcript","3":"18"},{"1":"IG_J_pseudogene","2":"transcript","3":"3"},{"1":"IG_pseudogene","2":"transcript","3":"1"},{"1":"IG_V_gene","2":"transcript","3":"141"},{"1":"IG_V_pseudogene","2":"transcript","3":"188"},{"1":"lncRNA","2":"transcript","3":"2487"},{"1":"miRNA","2":"transcript","3":"1881"},{"1":"misc_RNA","2":"transcript","3":"2212"},{"1":"Mt_rRNA","2":"transcript","3":"2"},{"1":"Mt_tRNA","2":"transcript","3":"22"},{"1":"polymorphic_pseudogene","2":"transcript","3":"22"},{"1":"processed_pseudogene","2":"transcript","3":"10156"},{"1":"protein_coding","2":"transcript","3":"1657"},{"1":"pseudogene","2":"transcript","3":"18"},{"1":"ribozyme","2":"transcript","3":"8"},{"1":"rRNA","2":"transcript","3":"51"},{"1":"rRNA_pseudogene","2":"transcript","3":"491"},{"1":"scaRNA","2":"transcript","3":"49"},{"1":"scRNA","2":"transcript","3":"1"},{"1":"snoRNA","2":"transcript","3":"942"},{"1":"snRNA","2":"transcript","3":"1900"},{"1":"sRNA","2":"transcript","3":"5"},{"1":"TEC","2":"transcript","3":"1041"},{"1":"TR_C_gene","2":"transcript","3":"6"},{"1":"TR_D_gene","2":"transcript","3":"4"},{"1":"TR_J_gene","2":"transcript","3":"79"},{"1":"TR_J_pseudogene","2":"transcript","3":"4"},{"1":"TR_V_gene","2":"transcript","3":"106"},{"1":"TR_V_pseudogene","2":"transcript","3":"33"},{"1":"transcribed_processed_pseudogene","2":"transcript","3":"599"},{"1":"transcribed_unitary_pseudogene","2":"transcript","3":"115"},{"1":"transcribed_unprocessed_pseudogene","2":"transcript","3":"895"},{"1":"translated_unprocessed_pseudogene","2":"transcript","3":"2"},{"1":"unitary_pseudogene","2":"transcript","3":"85"},{"1":"unprocessed_pseudogene","2":"transcript","3":"2565"},{"1":"vaultRNA","2":"transcript","3":"1"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[39]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


3. From the first question, please count the number of transcript for each TSL by source.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcih0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgPT0gJzEnKSAlPiUgZ3JvdXBfYnkoc291cmNlLCBmZWF0dXJlX3R5cGUpICU+JSBjb3VudCgpICAgICAgIFxuYGBgIn0= -->

```r
d2 %>% filter(transcript_support_level == '1') %>% group_by(source, feature_type) %>% count()       
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjIsIm5jb2wiOjMsInN1bW1hcnkiOnsiQSB0aWJibGUiOlsiMiB4IDMiXSwiR3JvdXBzIjpbInNvdXJjZSwgZmVhdHVyZV90eXBlIFsyXSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJtMVF3VXJFTUJDZHRJMUxneXNGdjJPTG9wYzlTZFVGRDdxSXdySTNpZG1zRm10U2toVDE1bmQ3MkpxbWlVallRSktaTjQ4M2IrYmhlbjFHMWdRQVVzZ1FnaFRiRVBEVi9meDhEcEFsTmtHUVFUNzhuNVowN0pnQWhiMkpMMHdXeThmRjNlV3RUdzl1cWxXMXJDSVNNWW9LelZUZG1qMEl3TlJ4SWI4QVVEOVJaOHdhcXJVWERDRFpVRVBMcmFMdlBLTG5TbjZVd3VJNjZIN2JwKy83WGF3YlNNVTQvMmhmeTA0eDdyUERMYWVtVS96SmZMVUJROEkrc2RaRXRxYVd3cW9sdzQ1dzVCYXBDQ2c2TVhUZnpOaHJKOTVtcHlmRFNLNCtucW1QazM5eE92Yk1lcStGZzJVdVhtb1I3T0dHUHZQR0owZDJGMjdLc2xXMStGdTlSWFZwcEtHQlI1aHNBdUtHZzkwdi8zWGF3eHNDQUFBPSJ9 -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["source"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"ENSEMBL","2":"transcript","3":"2367"},{"1":"HAVANA","2":"transcript","3":"29434"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[2]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDIgJT4lIGZpbHRlcih0cmFuc2NyaXB0X3N1cHBvcnRfbGV2ZWwgPT0gJ05BJykgJT4lIGdyb3VwX2J5KHNvdXJjZSwgZmVhdHVyZV90eXBlKSAlPiUgY291bnQoKVxuYGBgIn0= -->

```r
d2 %>% filter(transcript_support_level == 'NA') %>% group_by(source, feature_type) %>% count()
```

<!-- rnb-source-end -->

<!-- rnb-frame-begin eyJtZXRhZGF0YSI6eyJjbGFzc2VzIjpbImdyb3VwZWRfZGYiLCJ0YmxfZGYiLCJ0YmwiLCJkYXRhLmZyYW1lIl0sIm5yb3ciOjIsIm5jb2wiOjMsInN1bW1hcnkiOnsiQSB0aWJibGUiOlsiMiB4IDMiXSwiR3JvdXBzIjpbInNvdXJjZSwgZmVhdHVyZV90eXBlIFsyXSJdfX0sInJkZiI6Ikg0c0lBQUFBQUFBQUJtMVF3VW9ETVJDZDdHNHNEVllXdlBzSFhTaDY2WEhWZ2dkYlJFRjZLekZOZFhGTmxpU0xldlAzL0NVUFhiUFpSQ1Ewa0dUbXplUE5tN20vWHArVE5RR0FGREtFSU1VMkJIeDFONytZQTJTSlRSQmtNTzcvRDBzNmRVeUEzTjdFRjBhTDFjTmllWG5yMDZPYjhyRmNsUkdKR0VXRlpxcHF6QUVFWU9LNGNQWU5zUHlKT21OV1U2MjlZQURKbGhwYTdCUjk0eEY5ck9SN0lTeXVnKzZYZmJxdTI4ZTZnWlFQOHcvMnRXd1Y0ejQ3M25GcVdzVTM1ck1KR0JMMmliVkdzakdWRkZZdDZYZUVJN2RJUlVEZWlyNzdkc3BlV3ZFNm5jMzZrVng5T0JNZkovL2lkT2laZFY0TEI4dGNQRmNpMk1NMWZlSzFUMDdzTHR5VVJhTXE4YmQ2aStyQ1NFTURqekJaQjhRTkIvdGZEa1AyNXhzQ0FBQT0ifQ== -->

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["source"],"name":[1],"type":["chr"],"align":["left"]},{"label":["feature_type"],"name":[2],"type":["chr"],"align":["left"]},{"label":["n"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"ENSEMBL","2":"transcript","3":"7881"},{"1":"HAVANA","2":"transcript","3":"19962"}],"options":{"columns":{"min":{},"max":[10],"total":[3]},"rows":{"min":[10],"max":[10],"total":[2]},"pages":{}}}
  </script>
</div>

<!-- rnb-frame-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



### **3.4. CCDS in the GENCODE**

1. With `gene`, please create a data frame with the columns - gene_id, gene_name, hgnc_id, gene_type, chromosome, start, end, and strand. Then, please create new columns for presence of hgnc and ccds. For example, you can put `1` in the column `isHgnc`, if hgnc annotation is avaiable, or `0` if not. Then, you can put 1 in the column `isCCDS`, if ccds annotation is avaiable, or `0` if not.Please count the number of hgnc by gene biotypes.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDQgPC0gZCAlPiUgZmlsdGVyKGZlYXR1cmVfdHlwZSA9PSAnZ2VuZScpXG5gYGAifQ== -->

```r
d4 <- d %>% filter(feature_type == 'gene')
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDQkZ2VuZV9pZCA8LSBhcy5jaGFyYWN0ZXIoZG8uY2FsbChyYmluZC5kYXRhLmZyYW1lLCBcbiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBzdHJzcGxpdChkMiRpbmZvLCAnZ2VuZV9pZFxcXFxzK1wiJykpW1syXV0pXG5gYGAifQ== -->

```r
d4$gene_id <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d2$info, 'gene_id\\s+"'))[[2]])
```

<!-- rnb-source-end -->

<!-- rnb-output-begin eyJkYXRhIjoiRXJyb3I6IEFzc2lnbmVkIGRhdGEgYGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIHN0cnNwbGl0KGQyJGluZm8sIFwiZ2VuZV9pZFxcXFxcXFxccytcXFxcXCJcIikpW1syXV0pYCBtdXN0IGJlIGNvbXBhdGlibGUgd2l0aCBleGlzdGluZyBkYXRhLlxueCBFeGlzdGluZyBkYXRhIGhhcyA2MDYwMyByb3dzLlxueCBBc3NpZ25lZCBkYXRhIGhhcyAxMDgyNDMgcm93cy5cbmkgT25seSB2ZWN0b3JzIG9mIHNpemUgMSBhcmUgcmVjeWNsZWQuXG5SdW4gYHJsYW5nOjpsYXN0X2Vycm9yKClgIHRvIHNlZSB3aGVyZSB0aGUgZXJyb3Igb2NjdXJyZWQuXG4ifQ== -->

```
Error: Assigned data `as.character(do.call(rbind.data.frame, strsplit(d2$info, "gene_id\\\\s+\\""))[[2]])` must be compatible with existing data.
x Existing data has 60603 rows.
x Assigned data has 108243 rows.
i Only vectors of size 1 are recycled.
Run `rlang::last_error()` to see where the error occurred.
```



<!-- rnb-output-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDQkZ2VuZV9uYW1lIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQ0JGluZm8sICdnZW5lX25hbWVcXFxccytcIicpKVtbMl1dKVxuXG5kNCRnZW5lX25hbWUgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQ0JGdlbmVfbmFtZSwgJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d4$gene_name <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d4$info, 'gene_name\\s+"'))[[2]])

d4$gene_name <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d4$gene_name, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDQkZ2VuZV90eXBlIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQ0JGluZm8sICdnZW5lX3R5cGVcXFxccytcIicpKVtbMl1dKVxuXG5kNCRnZW5lX3R5cGUgPC0gYXMuY2hhcmFjdGVyKGRvLmNhbGwocmJpbmQuZGF0YS5mcmFtZSwgXG4gICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgc3Ryc3BsaXQoIGQ0JGdlbmVfdHlwZSwgJ1xcXFxcIicpKVtbMV1dKVxuYGBgIn0= -->

```r
d4$gene_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d4$info, 'gene_type\\s+"'))[[2]])

d4$gene_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d4$gene_type, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->


<!-- rnb-source-begin eyJkYXRhIjoiYGBgclxuZDQkdHJhbnNjcmlwdF90eXBlIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KGQ0JGluZm8sICdoZ25jX2lkXFxcXHMrXCInKSlbWzJdXSlcblxuZDQkdHJhbnNjcmlwdF90eXBlIDwtIGFzLmNoYXJhY3Rlcihkby5jYWxsKHJiaW5kLmRhdGEuZnJhbWUsIFxuICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHN0cnNwbGl0KCBkNCRoZ25jX2lkLCAnXFxcXFwiJykpW1sxXV0pXG5gYGAifQ== -->

```r
d4$transcript_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit(d4$info, 'hgnc_id\\s+"'))[[2]])

d4$transcript_type <- as.character(do.call(rbind.data.frame, 
                                                    strsplit( d4$hgnc_id, '\\"'))[[1]])
```

<!-- rnb-source-end -->

<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. Please count the number of hgnc by level. Please note that level in this question is not TSL. Please find information in this link: 1 (verified loci), 2 (manually annotated loci), 3 (automatically annotated loci).

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



## **3.5. Transcripts in the GENCODE**
1. Which gene has the largest number of transcripts?

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. Please calculate the quantiles (0%, 25%, 50%, 75%, 100%) of the gene length for protein coding genes and long noncoding genes.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


3. Please count the number of transcripts by chromosomes.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



### **3.6. Autosomal vs. Sex chromosomes.**

1. Please calculate the number of genes per chromosome.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


2. Please compare the number of genes between autosomal and sex chromosome (Mean, Median).

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->


3. Please divide the genes into groups ‘protein coding’ and ‘long noncoding’, and then compare the number of genes in each chromosomes within groups.

<!-- rnb-text-end -->


<!-- rnb-chunk-begin -->



<!-- rnb-chunk-end -->


<!-- rnb-text-begin -->



<!-- rnb-text-end -->


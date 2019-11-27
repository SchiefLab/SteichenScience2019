# Steichen *et al* - Data Analysis 

This repository contains instructions for obtaining the Next-Generation Sequencing (NGS) raw reads as well as post-production formatted sequencing data used in [J.M Steichen *et al*., *Science* 10.1126/aax4380 (2019)](https://science.sciencemag.org/content/early/2019/10/30/science.aax4380). 

In addition, this repository contains an example [Zeppelin](https://zeppelin.apache.org/) notebook containing [PySpark](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) code used to execute searches on the sequencing data.

## Raw Sequencing Reads

Paired reads for NextSeq and HiSeq sequencing data obtained in this study are available for download from our publicly available Amazon Web Services S3 data bucket found [here](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/raw_sequences/paired_reads.tgz). Paired reads from HiSeq sequencing data from [Briney *et al*, Nature 2019](https://www.ncbi.nlm.nih.gov/pubmed/30664748) that were used in Steichen et al are also avaialable in our S3 data bucket using the above link. 


## Joined, Annotated, and Clustered Sequences

Beyond the raw reads, we are also making available the joined, annotated and clustered sequences, as a compressed csv file. It is these sequences that were analyzed in the manuscript. You may use your own database framework to analyze the clustered sequences in the csv file. 

The compressed csv file is available for download [here](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/analyzed_sequences/AllDataMerged.csv.gz).

**Caution - the compressed csv file is 100 GB while the uncompressed file is over 700GB**

In addition, we are also providing all the sequences in the csv file as a converted parquet file which is more convenient for those that wish to carry out analyses using Amazon EMR (see below), as we did in this study. The compressed parquet file can be found at [here](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/analyzed_sequences/parquet.gz). Instructions for loading and querying parquet files are found below.

The fields in the csv file include the following annotations from [AbStar](https://github.com/briney/abstar) as well as several clustering and other metadata fields.


### Abstar Annotations 
| Name        | Description     |
| ------------- |:-------------:| 
| _id      | hash id of sequence|
| chain     | heavy or light      | 
| cdr3_aa | CDR3 amino acid sequence, IMGT definition      |   
| cdr3_nt | CDR3 amino acid sequence, IMGT definition      |   
| junct_nt | Junctional nucleotide sequence as defined by IMGT     |   
| isotype | Antibody Isotype, e.g. IgM, IgG etc..|   
| vdj_aa | The V,D and J gene segment amino acid sequences |   
| vdj_nt | The V,D and J gene segment nucleotide sequences|   
| v_full | The full V gene segment name, e.g. IGHV1-69*01      |   
| v_fam | The V gene segment family, e.g. IGHV1       |   
| v_gene | The V gene segment, e.g. IGHV1-69  |   
| d_full | The full D gene segment name, e.g. IGHD3-3*01      |   
| d_fam | The D gene segment family, e.g. IGHD3       |   
| d_gene | The D gene segment, e.g. IGHD3-3  |   
| j_full | The full J gene segment name, e.g. IGHJ6*01      |   
| j_gene | The J gene segment, e.g. IGHJ6   |

### Clustering Metadata 

| Name        | Description     |
| ------------- |:-------------:| 
| donor      | The de-identified donor id, e.g donor_316188|
| collection      | The demultiplexed technical replicate collection specification |
| generation      | The sequencing generation, see below|
| method      | The sequencing platform used, e.g. HiSeq, see below|
| ez_donor      | An integer based designation of the donor |
| unique_donors      | How many unique donors this sequence cluster was found in, see below |
| unique_mRNA      | How many mRNA transcripts were found across replicates, see below |
| original_cursor      | Legacy field, not needed|
| original_collection      | Legacy field, not needed|


### Method

The method designation is either HiSeq, NextSeq or Clustered HiSeq (cHiSeq).

### Generation

Generations correspond to the the sequencing generation as obtained during the course of the study. Generation 1 was the first four donors which were run through multiple independent HiSeq and NextSeq runs. Generation 2 is the next 8 donors in which a unique molecular identifier (UMI) was used to group PCR bias before HiSeq. Generation 3 was the last two donors using HiSeq and UMIs.

### Unique mRNA

The Unique mRNA corresponds to the number of times a sequence was found in the same donor but in multiple collections. Each collection corresponds to an individual PCR reaction from different mRNA preps. 

# Querying Datasets with PySpark/Zeppelin

While we have provided the sequencing data via formatted csv and raw sequencing reads to enable the user to employ their own sequence analysis pipelines, we found that a managed Hadoop cluster via Spark was an effective database implementation that could handle the large datasets with relatively low overhead.

We recommend [Amazon Web Services Elastic Map Reduce (EMR) service](https://aws.amazon.com/emr/) which has the option to automatically setup a managed [Spark Cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark.html). AWS has the added convenience of allowing the user you to designate the amount of worker nodes. More nodes means faster queries but will have a higher cost.

Finally, while one can run any given Spark Job by writing a custom application in Scala, we found it easiest to interact with Spark through a notebook implementation called [Zeppelin](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-zeppelin.html) (analogous to Jupyter notebooks for Python). Zeppelin automatically configures the user's Spark app, and will automatically distribute the user's queries across all available nodes.

## Changing CSV to Parquet File Format

The Hadoop ecosystem uses a column based format called Parquet. PySpark can be used to read in CSV files and store them as parquet. Optionally you can read in CSV files directly to your Spark dataframe, but this may take more time.

```python

##CSV2Parquet.py
from pyspark import SparkContext
from pyspark.sql import SQLContext

#Start with Spark Context
sc = SparkContext(appName="CSV2Parquet")

#Change to SQL context 
sql = SQLContext(sc)

##S3 bucket link to merged CSV
csv_file = "s3://steichenetalpublicdata/analyzed_sequences/AllDataMerged.csv.gz"

#Read CSV to DataFrame
df = (sql.read
          .format("com.databricks.spark.csv")
          .option("header", "true")
          .load(csv_file))

#Output path can also be an S3 bucket
p_path = "myoutputpath.parquet"
df.write.parquet(p_path, mode='overwrite')
```




## Querying Donors with Zeppelin

In the Zeppelin notebook folder, we have provided an [example notebook](https://github.com/SchiefLab/HIVPrimeDonors/tree/master/zeppelin_notebooks) for querying our dataset. This includes an example showing how to load parquet data as well as an example query using BG18 germline definitions used in Steichen et al. Please note, in order to view the Zeppelin notebook, you will need to upload the Zeppelin JSON file to a Zeppelin notebook server. In addition you must untar the [parquet.tgz](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/analyzed_sequences/parquet.gz) file and load in the folder into your Zeppelin environment.




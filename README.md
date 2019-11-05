# Steichen *et al* - Data Analysis 

This repository contains instructions for obtaining the Next-Generation Sequencing (NGS) reads as well as post-production analysis data found in [J.M Steichen *et al*., *Science* 10.1126/aax4380 (2019)](https://science.sciencemag.org/content/early/2019/10/30/science.aax4380). 

In addition, it contains an example [Zeppelin](https://zeppelin.apache.org/) notebooks containing [PySpark](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) code used to execute the analysis.

## Obtaining Raw Sequencing Reads

Forward and reverse reads for NextSeq and HiSeq sequencing are avaialbe for download from our publically avaiable Amazon Web Services S3 data bucket found [here](https://console.aws.amazon.com/s3/buckets/steichenetalpublicdata/raw_sequences/?region=us-east-2&tab=overview). 


## Obtaining Clustered Sequences

For your convience, the processed raw sequences that were joined, annonated and clustered are also avaiable as a compressed csv file. You may use your own database framework to analyze the clustered sequences. 

The compressed CSV file is avaialbe for download [here](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/analyzed_sequences/AllDataMerged.csv.gz) .

**Caution - the compressed CSV file is 100 GB while the uncompressed file is over 700GB**

In addition, the converted parquet file can be found [here](s3://steichenetalpublicdata/analyzed_sequences/parquet). Instructions for loading and querying can are found below.

The analyzed CSV files contains the follwoing annotations from [AbStar](https://github.com/briney/abstar) as well as several clustering and other metadata fields.


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
| vdj_nt | The V,D and J gene segment nucletodie sequences|   
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
| donor      | The full donor name, e.g donor_316188|
| collection      | The demultiplexed technical replicate collection specification |
| generation      | The sequencing generation, see below|
| method      | The sequencing platform used, e.g. HiSeq, see below|
| ez_donor      | An integer based designation of the donor |
| unique_donors      | How many unique donors this sequece cluster was found in |
| unique_mRNA      | How many mRNA transcripts were found across replicates |
| original_cursor      | Schief Lab record keeping|
| original_collection      | Schief Lab record keeping|


### Method

The method designation is either HiSeq, NextSeq or Clustered HiSeq (cHiSeq).


### Generation

Generations correspond to the the sequencing generation as obtained by the Schief Lab. Generation 1 was the first four donors which were run through multiple independent HiSeq and NextSeq runs. Generation 2 is the next 8 donors in which a unique molecular identifier (UMI) was used to group PCR bias before HiSeq. Generation 3 was the last two donors using HiSeq and UMIs.

### Unique Donors/Unique mRNA

The number of donors each sequence was found in is listed here. The Unique mRNA corresponds to the amount of times a sequence was found in the same donor but multiple collections. Each collection corresponds to an individual PCR reaction from different mRNA preps. 

# Quering Datasets with PySpark/Zeppelin

While we provide the analyzed CSV and raw sequencing reads for the user to subject to their own sequence analysis pipelines, we found that a managed Hadoop cluster via Spark was the only database implementation that could handle the large datasets with relatively low overhead.

We recoomend [Amazon Web Services Elastic Map Reduce (EMR) service](https://aws.amazon.com/emr/) which has the option to automatically setup a managed [Spark Cluster](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-spark.html). AWS has the added conviencice of allowing the user you to designate the amount of worker nodes. More nodes mean faster queries but will have a higher cost.

Finally, while you can run any given Spark Job by writing a custom application in Scala, we find it easiest to interact with Spark through a notebook implementation called [Zeppelin](https://docs.aws.amazon.com/emr/latest/ReleaseGuide/emr-zeppelin.html) (think Jupyter notebooks to python). Zeppelin automatically configures your Spark app, and will automatically distribute your queries across all available nodes.

## Changing CSV to Parquet File Format

The Hadoop ecosystem uses a column based format called Parquet. PySpark can be used to read in CSV files and store them as parquet. Optionally you can read in CSV files to your straight to your spark dataframe, but will take a lot of time.

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




## Quering Donors with Zeppelin

In the Zeppelin notebook folder, we have provided an example notebook for quering our dataset.




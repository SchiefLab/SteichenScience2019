# Steichen *et al* - Data Analysis 

This repository contains instructions for obtaining the Next-Generation Sequencing (NGS) raw reads as well as post-production analysis data found in [J.M Steichen *et al*., *Science* 10.1126/aax4380 (2019)](https://science.sciencemag.org/content/early/2019/10/30/science.aax4380). 

In addition, it contains example [Zeppelin](https://zeppelin.apache.org/) notebooks containing [PySpark](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) code used to execute the analysis.

## Obtaining Raw Sequencing Reads

Forward and reverse reads for NextSeq and HiSeq sequencing are avaialbe for download from our publically avaiable Amazon Web Services S3 data bucket found [here](https://console.aws.amazon.com/s3/buckets/steichenetalpublicdata/raw_sequences/?region=us-east-2&tab=overview). 


## Obtaining Clustered Sequences

For your convience, the processed raw sequences that were joined, annonated and clustered are also avaiable as a compressed csv file. You may use your own database framework to analyze the clustered sequences. 

The compressed CSV file is avaialbe for download [here](https://steichenetalpublicdata.s3-us-west-2.amazonaws.com/analyzed_sequences/AllDataMerged.csv.gz) .

**Caution - the compressed CSV file is 100 GB while the uncompressed file is over 700GB**

The analyzed CSV files contain annotations from [AbStar](https://github.com/briney/abstar) as well as clustering information with the following fields:

| Name        | Description     |
| ------------- |:-------------:| 
| _id      | hash id of sequence|
| chain     | heavy or light      | 
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   
| cdr3_aa | CDR3 amino acid sequence      |   




'chain', 
'cdr3_aa', 
'cdr3_nt', 
'isotype', 
'junc_nt', 
'vdj_aa', 
'vdj_nt', 
'v_full', 
'v_fam', 
'v_gene', 
'd_full', 
'd_fam', 
'd_gene', 
'j_full', 
'j_gene', 
'donor', 
'collection', 
'generation', 
'method', 
'ez_donor', 
'unique_donors', 
'unique_mRNA', 
'original_cursor', 
'original_collection'


_id=u'84a65675-7fc2-44b3-87e7-57a931d29665_1', chain=u'heavy', cdr3_aa=u'EGSAGY', cdr3_nt=u'GAGGGATCGGCGGGCTAC', isotype=u'unknown', junc_nt=u'TGTGAGGGATCGGCGGGCTACTGG', vdj_aa=u'VCGVPETLLRSLWIHV**FLEGVERVGSMERDGGEKYYVDSVKGRFTISRDNAKSSLFLQMNSLRADDTAVYYCEGSAGYWGQGTLVTVPS', vdj_nt=u'GTCTGTGGGGTCCCTGAGACTCTCCTGCGCAGCCTCTGGATTCACGTTTAGTAATTCTTGGAGGGGGTGGAGCGGGTGGGCTCCATGGAACGAGATGGAGGTGAGAAATACTATGTGGACTCTGTGAAGGGCCGATTCACCATTTCCAGAGACAACGCCAAGAGCTCACTGTTTTTGCAAATGAATAGCCTGAGAGCCGACGACACGGCTGTATATTATTGTGAGGGATCGGCGGGCTACTGGGGCCAGGGAACCCTGGTCACCGTCCCCTCAG', v_full=u'IGHV3-7*01', v_fam=u'IGHV3', v_gene=u'IGHV3-7', d_full=u'IGHD3-10*01', d_fam=u'IGHD3', d_gene=u'IGHD3-10', j_full=u'IGHJ4*02', j_gene=u'IGHJ4', donor=u'donor_316188', collection=u'D', generation=u'2', method=u'cHiSeq', ez_donor=u'5', unique_donors=u'1', unique_mRNA=u'1', original_cursor=u'eight_donors_clustered', original_collection=u'donor_316188')



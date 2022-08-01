# YeastVC
- Personal yeast variant calling as described in Johnson et al, 2021, eLife
- additional info on steps 3, 5, 6 is found on [DataCarpentry](https://datacarpentry.org/wrangling-genomics/04-variant_calling/index.html)
- additional info on step 3, 4, 6 is found on [GATK best practices workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035535912-Data-pre-processing-for-variant-discovery)
- using spark-enabled GATK tools on local machine is under develop [GATK best practices workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035889871)

## Steps
1. Demultiplex reads (already done by Illumina)
- sorts sequenced reads into separate files for each sample in a sequenced run
2. Trimming reads
-create Bash script to iterate through basenames (lacking R1 or R2) and execute [NGmerge](https://github.com/jsh58/NGmerge)
```NGmerge  -1 sample_R1.fastq.gz  -2 sample_R2.fastq.gz  -o sample_merged.fastq.gz```
3. MPI-based parallelized BWA mem alignment to the W303 genome (fastq->SAM)
-Output is a tab-delimited text file w/ information for each individual read and its alignment to the genome
4. gatk MarkDuplicatesSpark to mark duplicates and sort
-MarkDuplicatesSpark utilizes Apache Spark in order to parallelize the process to better take advantage all available resources
5. samtools view (SAM->BAM)
-Output is a compressed binary version of SAM. This version reduces size and to allows for indexing, which enables efficient random access of the data contained within the file.
6. gatk base quality score recal [GATK best practices workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035890531)
7. gatk ApplyBQSR [GATK best practices workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360035890531)
8. gatk HaplotypeCaller [GATK best practices workflow](https://gatk.broadinstitute.org/hc/en-us/articles/360037225632-HaplotypeCaller)
9. Vcftools merge vcfs
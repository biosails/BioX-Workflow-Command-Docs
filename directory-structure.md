### Directory Structure

This is a common used directory convention in BioX.

```
my_analysis
    /raw
      Sample_01/
         Sample_01.read1.fastq.gz
         Sample_01.read2.fastq.gz
      Sample_02
         Sample_02.read1.fastq.gz
         Sample_02.read2.fastq.gz
   /processed
       trimmomatic/
         Sample_01/
           Sample_01.read1.trimm.fastq.gz
           Sample_01.read2.trimm.fastq.gz
         Sample_02
           Sample_02.read1.trimm.fastq.gz
           Sample_02.read2.trimm.fastq.gz
      aligned/
         Sample_01/
           Sample_01.bam
         Sample_02
           Sample_02.bam
```




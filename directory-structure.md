### Directory Structure

This is a common used directory convention in BioX.

```
my_analysis
    data/raw
      Sample_01/
         Sample_01.read1.fastq.gz
         Sample_01.read2.fastq.gz
      Sample_02
         Sample_02.read1.fastq.gz
         Sample_02.read2.fastq.gz
    data/processed
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

Lets break this down piece by piece.

### Raw Data

```
my_analysis
    data/raw
      Sample_X/
```

This is the most common way to setup the initial data. All samples are folders, and usually the initial \(raw\) data is in a separate location from the secondary \(processed, analysis\) data.

### Processed Data

```
data/processed
  trimmomatic/
    Sample_X/
      Sample_X.read1.trimm.fastq.gz
      Sample_X.read2.trimm.fastq.gz
```

By default, each rule that is created in has a separate outdir. This is declared by using the _outdir._ Declaring the outdir as _'data/processed_' in the global variables would result in the outdirs as '_data/processed/SampleX/RULE\_NAME'. _In this case the rule name is _'trimmomatic'._


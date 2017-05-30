# Quick Start - Simple Workflow

Here is a workflow that trims reads, gzips them, and then aligns them using bowtie2. The initial file structure is 'data/raw/Sample\_01',  'data/raw/Sample\_02', and 'data/raw/Sample\_03'

```
---
global:
    # ROOT Directory configurations
    # Aligning against raw reeds
    - indir: "data/raw"
    - outdir: "data/processed"
    # Find Samples in indir
    - sample_rule: (Sample.*)$
    - by_sample_outdir: 1
    - find_by_dir: 1
    # Additional Dir Setup
    - trimmomatic_dir: "data/processed/{$sample}/trimmomatic"
    - bowtie2_dir: "data/processed/{$sample}/bowtie2"
    # Add additional varibles to make the template a bit cleaner
    - TR1: "{$self->trimmomatic_dir}/{$sample}_read1_trimmomatic"
    - TR2: "{$self->trimmomatic_dir}/{$sample}_read2_trimmomatic"
    - bowtie2_reference: "genome.fa"
rules:
    - trimmomatic:
        local:
            - outdir: "{$self->trimmomatic_dir}"
            - INPUT:
              - "{$self->indir}/{$sample}_read1.fastq.gz"
              - "{$self->indir}/{$sample}_read2.fastq.gz"
            - OUTPUT:
              - "{$self->TR1}_1PE.fastq"
              - "{$self->TR1}_1SE.fastq"
              - "{$self->TR2}_2PE.fastq"
              - "{$self->TR2}_2SE.fastq"
              - "{$self->trimmomatic_dir}/{$sample}_trimmomatic.log"
        process: |
             #TASK tags={$sample}
             trimmomatic \
                PE -threads 6 \
                -trimlog {$self->OUTPUT->[4]} \
                {$self->INPUT->[0]} \
                {$self->INPUT->[1]} \
                {$self->OUTPUT->[0]} \
                {$self->OUTPUT->[1]} \
                {$self->OUTPUT->[2]} \
                {$self->OUTPUT->[3]} \
                TRAILING:3 LEADING:3 SLIDINGWINDOW:4:15 MINLEN:36
    - trimmomatic_gzip:
        local:
          - indir: "{$self->trimmomatic_dir}"
          - outdir: "{$self->trimmomatic_dir}"
          - INPUT:
            - "{$self->TR1}_1PE.fastq"
            - "{$self->TR2}_2PE.fastq"
            - "{$self->TR1}_1SE.fastq"
            - "{$self->TR2}_2SE.fastq"
          - OUTPUT:
            - "{$self->TR1}_1PE.fastq.gz"
            - "{$self->TR2}_2PE.fastq.gz"
            - "{$self->TR1}_1SE.fastq.gz"
            - "{$self->TR2}_2SE.fastq.gz"
        process: |
            #TASK tags={$sample}
            gzip -f {$self->INPUT->[0]}

            #TASK tags={$sample}
            gzip -f {$self->INPUT->[1]}

            #TASK tags={$sample}
            gzip -f {$self->INPUT->[2]}

            #TASK tags={$sample}
            gzip -f {$self->INPUT->[3]}
    - bowtie2:
        local:
              - indir: "{$self->trimmomatic_dir}"
              - outdir: "{$self->bowtie2_dir}"
              - INPUT:
                - "{$self->TR1}_1PE.fastq.gz"
                - "{$self->TR2}_2PE.fastq.gz"
              - OUTPUT: "{$self->bowtie2_dir}/{$sample}_aligned.sam"
        process: |
                #TASK tags={$sample}_{$self->chunk}
                mkdir -p {$self->bowtie2_dir} && \
                bowtie2 -p 7 -x {$self->bowtie2_reference} \
                -1 {$self->INPUT->[0]} \
                -2 {$self->INPUT->[1]} \
                -S {$self->OUTPUT}
```

Starting with trimmomatic\_gzip, I removed the _'_/home/user/my\_analysis' in order to make the file more readable.

    #!/usr/bin/env bash


    #
    # Generated at: 2017-05-30T12:28:55
    # This file was generated with the following options
    #    run
    #    -w    resequencing-human-complete-part1-trimm_reads_chunk.yml
    #    -o    reseq.sh
    #

    #

    # Starting Workflow

    #
    #
    # Global Variables:
    #    indir:
    #        - data/raw
    #    outdir:
    #        - data/processed
    #    sample_rule: (Sample.*)$
    #    by_sample_outdir: 1
    #    find_by_dir: 1
    #    trimmomatic_dir: data/processed/{$sample}/trimmomatic
    #    bowtie2_dir: data/processed/{$sample}/bowtie2
    #    TR1: {$self->trimmomatic_dir}/{$sample}_read1_trimmomatic
    #    TR2: {$self->trimmomatic_dir}/{$sample}_read2_trimmomatic
    #    bowtie2_reference: genome.fa
    #

    #
    #

    #
    # Starting trimmomatic
    #


    #
    # Variables
    # Indir: data/raw
    # Outdir: {$self->trimmomatic_dir}
    #
    # Local Variables:
    #
    #    outdir:
    #        - '{$self->trimmomatic_dir}'

    #    INPUT:
    #        - '{$self->indir}/{$sample}_read1.fastq.gz'
    #        - '{$self->indir}/{$sample}_read2.fastq.gz'

    #    OUTPUT:
    #        - '{$self->TR1}_1PE.fastq'
    #        - '{$self->TR1}_1SE.fastq'
    #        - '{$self->TR2}_2PE.fastq'
    #        - '{$self->TR2}_2SE.fastq'
    #        - '{$self->trimmomatic_dir}/{$sample}_trimmomatic.log'

    #

    #
    ### HPC Directives
    #HPC jobname=trimmomatic
    #


    #TASK tags=Sample_01
    trimmomatic \
    PE -threads 6 \
    -trimlog /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_trimmomatic.log \
    /home/user/my_analysis/data/raw/Sample_01/Sample_01_read1.fastq.gz \
    /home/user/my_analysis/data/raw/Sample_01/Sample_01_read2.fastq.gz \
    /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq \
    /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1SE.fastq \
    /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq \
    /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2SE.fastq \
    TRAILING:3 LEADING:3 SLIDINGWINDOW:4:15 MINLEN:36; \
    biox file_log \
        --exit_code `echo $?`  \
        --file /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_01/trimmomatic/Sample_01_trimmomatic.log \
        --file /home/user/my_analysis/data/raw/Sample_01/Sample_01_read1.fastq.gz \
        --file /home/user/my_analysis/data/raw/Sample_01/Sample_01_read2.fastq.gz


    #TASK tags=Sample_02
    trimmomatic \
    PE -threads 6 \
    -trimlog /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_trimmomatic.log \
    /home/user/my_analysis/data/raw/Sample_02/Sample_02_read1.fastq.gz \
    /home/user/my_analysis/data/raw/Sample_02/Sample_02_read2.fastq.gz \
    /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq \
    /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1SE.fastq \
    /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq \
    /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2SE.fastq \
    TRAILING:3 LEADING:3 SLIDINGWINDOW:4:15 MINLEN:36; \
    biox file_log \
        --exit_code `echo $?`  \
        --file /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_02/trimmomatic/Sample_02_trimmomatic.log \
        --file /home/user/my_analysis/data/raw/Sample_02/Sample_02_read1.fastq.gz \
        --file /home/user/my_analysis/data/raw/Sample_02/Sample_02_read2.fastq.gz


    #TASK tags=Sample_03
    trimmomatic \
    PE -threads 6 \
    -trimlog /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_trimmomatic.log \
    /home/user/my_analysis/data/raw/Sample_03/Sample_03_read1.fastq.gz \
    /home/user/my_analysis/data/raw/Sample_03/Sample_03_read2.fastq.gz \
    /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq \
    /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1SE.fastq \
    /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq \
    /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2SE.fastq \
    TRAILING:3 LEADING:3 SLIDINGWINDOW:4:15 MINLEN:36; \
    biox file_log \
        --exit_code `echo $?`  \
        --file /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2SE.fastq \
        --file /home/user/my_analysis/data/processed/Sample_03/trimmomatic/Sample_03_trimmomatic.log \
        --file /home/user/my_analysis/data/raw/Sample_03/Sample_03_read1.fastq.gz \
        --file /home/user/my_analysis/data/raw/Sample_03/Sample_03_read2.fastq.gz

    #######################################################
    ###FROM HERE DOWN I REMOVED /home/user/my_analysis/
    ######################################################
    #
    #

    #
    # Starting trimmomatic_gzip
    #


    #
    # Variables
    # Indir: {$self->trimmomatic_dir}
    # Outdir: {$self->trimmomatic_dir}
    #
    # Local Variables:
    #
    #    indir:
    #        - '{$self->trimmomatic_dir}'

    #    outdir:
    #        - '{$self->trimmomatic_dir}'

    #    INPUT:
    #        - '{$self->TR1}_1PE.fastq'
    #        - '{$self->TR2}_2PE.fastq'
    #        - '{$self->TR1}_1SE.fastq'
    #        - '{$self->TR2}_2SE.fastq'

    #    OUTPUT:
    #        - '{$self->TR1}_1PE.fastq.gz'
    #        - '{$self->TR2}_2PE.fastq.gz'
    #        - '{$self->TR1}_1SE.fastq.gz'
    #        - '{$self->TR2}_2SE.fastq.gz'

    #

    #
    ### HPC Directives
    #HPC jobname=trimmomatic_gzip
    #


    #TASK tags=Sample_01
    gzip -f data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq
    #TASK tags=Sample_01
    gzip -f data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq
    #TASK tags=Sample_01
    gzip -f data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1SE.fastq
    #TASK tags=Sample_01
    gzip -f data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2SE.fastq; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1SE.fastq \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1SE.fastq.gz \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq.gz \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2SE.fastq \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2SE.fastq.gz


    #TASK tags=Sample_02
    gzip -f data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq
    #TASK tags=Sample_02
    gzip -f data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq
    #TASK tags=Sample_02
    gzip -f data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1SE.fastq
    #TASK tags=Sample_02
    gzip -f data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2SE.fastq; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1SE.fastq \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1SE.fastq.gz \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq.gz \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2SE.fastq \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2SE.fastq.gz


    #TASK tags=Sample_03
    gzip -f data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq
    #TASK tags=Sample_03
    gzip -f data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq
    #TASK tags=Sample_03
    gzip -f data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1SE.fastq
    #TASK tags=Sample_03
    gzip -f data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2SE.fastq; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1SE.fastq \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1SE.fastq.gz \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq.gz \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2SE.fastq \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2SE.fastq.gz



    #
    #

    #
    # Starting bowtie2
    #


    #
    # Variables
    # Indir: {$self->trimmomatic_dir}
    # Outdir: {$self->bowtie2_dir}
    #
    # Local Variables:
    #
    #    indir:
    #        - '{$self->trimmomatic_dir}'

    #    outdir:
    #        - '{$self->bowtie2_dir}'

    #    INPUT:
    #        - '{$self->TR1}_1PE.fastq.gz'
    #        - '{$self->TR2}_2PE.fastq.gz'

    #    OUTPUT: {$self->bowtie2_dir}/{$sample}_aligned.sam

    #

    #
    ### HPC Directives
    #HPC jobname=bowtie2
    #


    #TASK tags=Sample_01_
    mkdir -p data/processed/Sample_01/bowtie2 && \
    bowtie2 -p 7 -x genome.fa \
    -1 data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq.gz \
    -2 data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq.gz \
    -S data/processed/Sample_01/bowtie2/Sample_01_aligned.sam; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_01/bowtie2/Sample_01_aligned.sam \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_01/trimmomatic/Sample_01_read2_trimmomatic_2PE.fastq.gz


    #TASK tags=Sample_02_
    mkdir -p data/processed/Sample_02/bowtie2 && \
    bowtie2 -p 7 -x genome.fa \
    -1 data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq.gz \
    -2 data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq.gz \
    -S data/processed/Sample_02/bowtie2/Sample_02_aligned.sam; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_02/bowtie2/Sample_02_aligned.sam \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_02/trimmomatic/Sample_02_read2_trimmomatic_2PE.fastq.gz


    #TASK tags=Sample_03_
    mkdir -p data/processed/Sample_03/bowtie2 && \
    bowtie2 -p 7 -x genome.fa \
    -1 data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq.gz \
    -2 data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq.gz \
    -S data/processed/Sample_03/bowtie2/Sample_03_aligned.sam; \
    biox file_log \
        --exit_code `echo $?`  \
        --file data/processed/Sample_03/bowtie2/Sample_03_aligned.sam \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read1_trimmomatic_1PE.fastq.gz \
        --file data/processed/Sample_03/trimmomatic/Sample_03_read2_trimmomatic_2PE.fastq.gz




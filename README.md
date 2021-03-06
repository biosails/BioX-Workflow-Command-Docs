## BioX-Workflow-Command

> An extremely opinionated bioinformatics and genomics workflow creator.

BioX-Workflow-Command \(BioX\) adds consistency to your analyses. It provides a uniform way of processing multiple samples over many steps of an analysis, along with a clean and concise directory structure.

Bioinformatics workflows usually involve starting with a set of samples, and analyzing those samples in one or more steps. BioX helps to accomplish this by implementing a templating system that simplifies analysis rules. It has a robust set of defaults in order to get started quickly \(this is the extremely opinionated part\), but gives you the freedom to override these whenever you wish.

Once you have your configuration run BioX to get your shell script.

```
biox run -w my_workflow.yml -o my_workflow.sh
```

## Indepth

### What is a Workflow?

A workflow is set of tasks that make up a scientific analysis. These include quality control, alignment, variant detection, enrichment analysis, and many more.

Specifically, according to BioX, a workflow is a yaml or json configuration file that follows a specific format. At a minimum a workflow has at least one rule.

To see examples of production workflows check out the github repository  [biosails/workflows](https://github.com/biosails/workflows).

### What is a Rule?

BioX-Workflow-Command runs with a list of rules. Each rule is a step in your analysis.

BioX-Workflow-Command assumes you have a set of inputs, known as samples, and these inputs will carry on through your pipeline. There are some exceptions to this, which we will explore in the [Advanced - Adding Custom Lists](/advanced-adding-custom-lists.md), but just iterating over your samples will get you far!

Here is an example of a trimmomatic rule.

```
    - trimmomatic:
        local:
            - outdir: "{$self->trimmomatic_dir}"
            - INPUT:
              - "{$self->raw_fastq_dir}/{$sample}_read1.fastq.gz"
              - "{$self->raw_fastq_dir}/{$sample}_read2.fastq.gz"
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
```

Pointed at a list of samples \([Find Samples](/find-samples.md)\), running biox would generate a single bash command, aka a task, per sample.

### Directory Structure

BioX makes some assumptions about your output structure. It assumes you have each of your processes/rules outputting to a distinct directory.  These directories will be automatically created based on your rule name.

Here is an example directory structure from BioX-Workflow.

```
├── my_analysis
│       ├── Sample01
│       │   ├── analysis
│       │   │   ├── bowtie2
│       │   │   ├── gatk
│       │   │   ├── picard
│       │   │   └── samtools
│       │   └── processed
│       │       ├── trimm_fastqc
│       │       └── trimmomatic
```

This is the extremely beta version of documentation for the 2.0.0 release of BioX-Workflow-Command. More information coming soon!


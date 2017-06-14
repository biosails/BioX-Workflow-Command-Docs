# Quick Start - Create a New Workflow

BioX can create a template for a new workflow for you from scratch. Before you begin this process, it is probably best to sit down with a pen and paper and think about what are the discrete steps in your workflow. Each step will be a rule.

```
biox new -w my_new_workflow.yml --rules step1,step2,step3
```

Let's take a portion of the NYUAD Variant Calling pipeline. For the sake of brevity we will assume we already have alignment files.

So what are the steps?

1. Call variants using samtools mpileup - samtools\_mpileup
2. Index the vcf file using tabix - tabix\_index
3. Get some stats using bcftools stats - bcftools\_stats
4. Filter the variants using bcftools filter - bcftools\_filter

Now, lets draw a table of our inputs and outputs.

The indir is_ {$self-&gt;picard\_dir}_ and the outdir is _{$self-&gt;samtools\_dir}_. This is because the pipeline actually uses 2 different methods to call variants, and they go in separate directories. This way we don't confuse our support staff.

| Rule | Input | Output |
| :--- | :--- | :--- |
| samtools\_mpileup | {$sample}\_recalibrated.mdup.withrg.csorted.cleaned.aligned.bam | {$sample}\_aligned.sorted.vcf.gz |
| tabix\_index | {$sample}\_aligned.sorted.vcf.gz | {$sample}\_aligned.sorted.vcf.gz.tbi |
| bcftools\_stats | {$sample}\_aligned.sorted.vcf.gz, {$sample}\_aligned.sorted.vcf.gz.tbi | {$sample}\_aligned.sorted.vcf.stats |
| bcftools\_filter | {$sample}\_aligned.sorted.vcf.gz, {$sample}\_aligned.sorted.vcf.gz.tbi | {$sample}\_filteredQ10.alinged.sorted.vcf.gz\*\* |

\*\* Notice the long file names. They may seem stupidly long, and but it makes it very easy to come back and see the lifecycle of a single file just by looking at the names. For instance, with the bcftools, we don't just put 'filter', but instead 'filteredQ10' to account for the kind of filtering.

```
biox new -w samtools_variant_calling.yml --rules samtools_mpileup,tabix_index,bcftools_stats,bcftools_filter
```

The _samtools\_variant\_calling.yml_ workflow file initially looks like this:

```
---
global:
  - sample_rule: (Sample_.*)
  - indir: data/raw
  - outdir: data/processed
  - root_dir: data
  - find_sample_bydir: 1
  - by_sample_outdir: 1
rules:
  - samtools_mpileup:
      local:
        - INPUT: '{$self->root_dir}/some_input_rule1'
        - OUTPUT:
            - some_output_rule1
      process: |-
        INDIR: {$self->indir}
        INPUT: {$self->INPUT}
        outdir: {$self->outdir}
        OUTPUT: {$self->OUTPUT->[0]}
 - tabix_index
 ...
```

BioX gives you a quick skeleton of INPUTs and OUTPUTs, which you will need to edit on your own.

Generally it is easiest to define the directories in the global key.

```
---
global:
  ...
  - samtools_dir: "{$self->root_dir}/{$sample}/analysis/samtools"
  - picard_dir: "{$self->root_dir}/{$sample}/analysis/picard"
```

Then, add your indirs and outdirs to the rule.local key , and the process to the rule.process.

```
---
global:
  - sample_rule: (Sample_.*)
  - indir:     data/raw
  - outdir:    data/processed
  - root_dir:  data
  - find_sample_bydir: 1
  - by_sample_outdir: 1
  - samtools_dir: "{$self->root_dir}/{$sample}/analysis/samtools"
  - picard_dir: "{$self->root_dir}/{$sample}/analysis/picard"
rules:
  - samtools_mpileup:
      local:
        - indir: "{$self->picard_dir}"
        - outdir: "{$self->samtools_dir}"
        - INPUT: "{$self->picard_dir}/{$sample}_recalibrated.mdup.withrg.csorted.cleaned.aligned.bam"
        - SAM: "{$self->samtools_dir}/{$sample}_recalibrated.mdup.withrg.csorted.cleaned.aligned.bam"
        - OUTPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.gz"
      process: |-
        #TASK tags={$sample}
        rm -rf {$self->OUTPUT} && \
        ln -s {$self->INPUT} {$self->SAM} && \
        samtools index {$self->SAM} && \
        samtools mpileup -ugf {$self->reference} \
            {$self->INPUT} | bcftools call -vmO z -o \
            {$self->OUTPUT}
  - tabix_index:
      local:
        - indir: "{$self->samtools_dir}"
        - outdir: "{$self->samtools_dir}"
        - INPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.gz"
        - OUTPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.gz.tbi"
      process: |-
          #TASK tags={$sample}
          tabix -p vcf {$self->INPUT}
  - bcftools_stats:
      local:
        - indir: "{$self->samtools_dir}"
        - outdir: "{$self->samtools_dir}"
        - INPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.gz"
        - OUTPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.stats"
      process: |-
        #TASK tags={$sample}
        bcftools stats -F {$self->reference} -s - {$self->INPUT} \
            > {$self->OUTPUT}
  - bcftools_filter:
      local:
        - indir: "{$self->samtools_dir}"
        - outdir: "{$self->samtools_dir}"
        - INPUT: "{$self->samtools_dir}/{$sample}_aligned.sorted.vcf.gz"
        - OUTPUT: "{$self->samtools_dir}/{$sample}_filteredQ10.aligned.sorted.vcf.gz"
      process: |-
        #TASK tags={$sample}
        bcftools filter -O z \
            -o {$self->OUTPUT} \
            -s LOWQUAL -i'%QUAL>10' \
            {$self->INPUT}

```




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

The indir is_ "{$self-&gt;picard\_dir}"_ and the outdir is _"{$self-&gt;samtools\_dir}". _This is because the pipeline actually uses 2 different methods to call variants, and they go in separate directories. This way we don't confuse our support staff.

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




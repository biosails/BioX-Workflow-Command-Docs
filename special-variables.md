# Special Variables

BioX-Workflow-Command uses a series of special variables. Most of these have to do with files, but it is also possible to create dynamic lists, and to define HPC variables for use with HPC-Runner-Command.

For all of these examples we will use '/home/user/my\_analysis' as the current working directory.

The initial directory structure is :

```
data
└── raw
    ├── Sample_01
    ├── Sample_02
    └── Sample_03
```

The workflow is \_my\_workflow.yml, \_and we run biox as

```
biox run -w my_workflow.yml -o my_workflow.sh
```

## Indir / Outdir / and \*\_dir

_Indir/outdir_ and all variables that are named in the pattern _\*\_dir \(trimmomatic\_dir, analysis\_dir, my\_awesome\_dir, etc\)_ \_evaluates to an [absolute directory](https://www.linux.com/blog/absloute-path-vs-relative-path-linuxunix).

The global _indir_ tells biox where to find the samples.

```
    ---
    global:
        - indir: 'data/raw'
        - outdir: 'data/processed'
```

```
biox run -w my_workflow.yml -o my_file
```

BioX would evaluate the global _indir_ as '/home/user/my\_analysis/data/raw'. This behavior can be overridden if you set _coerce\_abs\_dir_ to 0.

There is also an _indir \_per local rule, which is also evaluated as a full path. \_Indir \_is a chained variable, meaning it gets its value from either the global, if it is the first rule, or the \_outdir_ of the previous rule, unless it is set.

```
---
global:
  - sample_rule: Sample_.*
  - indir: data/raw
  - outdir: data/processed
  - root_dir: data
  - find_sample_bydir: 1
  - by_sample_outdir: 1
rules:
  - rule1:
      process: |-
        indir: {$self->indir}
        outdir: {$self->outdir}
  - rule2:
      process: |-
        indir: {$self->indir}
        outdir: {$self->outdir}
```

For the sake of readability '/home/user/myanalysis' was ommited from the Rendered Variable for all but the first variable.

Running biox would give us

| Rule | Template Variable | Rendered Variable |
| :--- | :--- | :--- |
| rule1 | indir | /home/user/my\_analysis/data/raw/Sample\_01 |
| rule1 | outdir | data/processed/Sample\_01/rule1 |
| rule2 | indir | data/processed/Sample\_01/rule1 |
| rule2 | outdir | data/processed/Sample\_01/rule2 |

You can, of course, change this and make it whatever you like.

```
---
global:
  - sample_rule: Sample_.*
  - indir: data/raw
  - outdir: data/processed
  - trimmomatic_dir: data/processed/{$sample}/trimmomatic
  - analysis_dir: data/processed/{$sample}/analysis
  - find_sample_bydir: 1
  - by_sample_outdir: 1
rules:
  - rule1:
      local:
        - outdir: '{$self->trimmomatic_dir}'
      process: |-
        indir: {$self->indir}
        outdir: {$self->outdir}
  - rule2:
      local:
        - indir: '{$self->analysis_dir}'
      process: |-
        indir: {$self->indir}
        outdir: {$self->outdir}
```

| Rule | Template Variable | Output Variable |
| :--- | :--- | :--- |
| rule1 | indir | /home/user/my\_analysis/data/raw/Sample\_01 |
| rule1 | outdir | data/processed/Sample\_01/trimmomatic |
| rule2 | indir | data/processed/Sample\_01/analysis |
| rule2 | outdir | data/processed/Sample\_01/rule2 |

In this example we explictly set the _outdir _ of rule1 and the _indir_ of rule2.

## INPUT/OUTPUT

Input and output are like indir/outdir, but instead of directories they are evaluated as files. INPUT/OUTPUT can also take lists or objects as their values, unlike indir/outdir, which can only take scalar.

```
            local:
              - bowtie2_dir: "data/processed/{$sample}/bowtie2"
              - INPUT:
                - "{$self->trimmomatic_dir}/{$sample}_1PE.fastq.gz"
                - "{$self->trimmomatic_dir}/{$sample}_2PE.fastq.gz"
              - OUTPUT: "{$self->bowtie2_dir}/{$sample}_aligned.sam"
            process: |
                #TASK tags={$sample}
                -1 {$self->INPUT->[0]} \
                -2 {$self->INPUT->[1]} \
                -S {$self->OUTPUT}
```

The INPUT in this case is defined as a list, which we access as {$self-&gt;INPUT-&gt;\[0\]} and {$self-&gt;INPUT-&gt;\[1\]} \(because for computer a list always starts with 0\), but OUTPUT is a single variable and is accessed as {$self-&gt;OUTPUT}.

For the sake of readability  '/home/user/myanalysis' was ommited from the Rendered Variable

| Template Variable | Rendered Variable |
| :--- | :--- |
| {$self-&gt;INPUT-&gt;\[0\]} | data/processed/Sample\_01/trimmomatic/Sample\_01\_1PE.fastq.gz |
| {$self-&gt;INPUT-&gt;\[1\]} | data/processed/Sample\_01/trimmomatic/Sample\_01\_2PE.fastq.gz |
| {$self-&gt;OUTPUT} | data/processed/Sample\_01/bowtie2/Sample\_01\_align.sam |




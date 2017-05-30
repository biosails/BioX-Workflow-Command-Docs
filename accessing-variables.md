# Accessing Variables

BioX-Workflow-Command takes advantage of templating. Templating systems like to put variables into documents with funny symbols around them so the computer knows its a variable and not just some text.

Variables are **declared** in the _global/local_ keys.

```
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
            ...
```

And they are **accessed** in the_ process_ key.

```
    - trimmomatic:
        local:
          ...
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

Variables are always accessed as {$self-&gt;VARNAME}, except for sample, which is accessed as just {$sample}. When the [templating library ](https://metacpan.org/pod/Text::Template) sees '{' it knows that a variable and not just plain text is starting.


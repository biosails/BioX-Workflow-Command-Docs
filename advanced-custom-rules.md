# Advanced - Custom Rules

Most of the time a bioinformatics analysis has a list of samples that need to be iterated over, with one task per sample. Sometimes, this is not the case.



```
---
global:
    - indir: "data/raw"
    - outdir: "data/processed"
    - root: "data/processed"
rules:
    - remove_SAM:
        local:
                - override_process: 1
                - create_outdir: 0
                - HPC:
                   - deps: samtools_view
                   - walltime: '00:10:00'
                   - mem: '4GB'
        process: |
                #TASK tags={$sample}
                find {$self->root} -name "*.sam" | xargs -I {} rm -rf {}

```

Here we are using the bash find command to find all .sam files in the "data/processed" directory, and delete them. We do not need one task per sample, but one task for the entire rule. This could also be used for downstream analysis across samples where sample files need to be merged into a single vcf.


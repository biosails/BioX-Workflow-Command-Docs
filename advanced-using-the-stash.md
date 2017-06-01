# Advanced - Using the Stash

The stash is most often used in conjunction with custom rules. It is a way of manipulating the global object within a workflow, or making data persist between rules.

In this example we use chunks, which is just an arbitrary list defined in BioX, iterate over them, use them to categorize our samples, and stick them in the stash. We could access the variable {$self-&gt;stash-&gt;{allchunks}}_ _anywhere in the workflow after the combine\_chunks rule.

This is an example from the resequencing model organisms workflow.

```
    - combine_chunks:
        local:
                - indir: "{$self->picard_dir}"
                - outdir: "{$self->picard_dir}"
                # - INPUT: "{$self->picard_dir}/{$sample}_cleaned.aligned.{$self->chunk}.bam"
                - INPUT: ''
                - OUTPUT: "{$self->picard_dir}/{$sample}_cleaned.aligned.bam"
                - HPC:
                   - deps: picard_cleansam
                   - walltime: '15:00:00'
                   - mem: '28GB'
        process: |
                #TASK tags={$sample}
                {
                  my @all_chunks = ();
                  for(my $x=$self->chunks->{start}; $x<=$self->chunks->{end}; $x++) {
                    $self->chunk($x);
                    my $part = $self->picard_dir.'/'.$self->sample.'_cleaned.aligned.'.$self->chunk.'.bam';
                    push(@all_chunks, $part);
                  }
                  $self->stash->{all_chunks} = join(" \\\n", @all_chunks);
                  ($SILENTLY);
                }
                samtools merge  -f {$self->OUTPUT} \
                  {$self->stash->{all_chunks}}

```




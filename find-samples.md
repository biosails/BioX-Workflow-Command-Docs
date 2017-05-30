## Let's Find Some Samples

Possibly the most important part of your workflow is finding samples.

Samples, like variables, come in two flavors. They are files, or they are directories. The first example shows files, and the second directories.

The big difference is the flag \_find\_sample\_by\_dir \_is set to 1 when samples are directories.

### Samples are Files

Samples are files with the sample name and a .csv extension.

```
    /home/user/workflow/
        /data
            /raw
                sample1.csv
                sample2.csv
```

```
    ---
    global:
        - indir: /home/user/workflow/data/raw
        - outdir: /home/user/workflow/data/analysis
        - sample_rule: (sample.*).csv$
```

### Samples are Directories

This time the sample names come from a directory, with supplementary files inside.

```
    /home/user/workflow
         /data/raw
                  /sample1
                     billions_of_small_files
                     from_the_Sequencer
                  /sample2
                     billions_of_small_files
                     from_the_Sequencer
```

```
    ---
    global:
        - indir: /home/user/workflow/data/raw
        - outdir: /home/user/workflow/data/analysis
        - sample_rule: (sample.*)
        ###Samples are directories
        - find_sample_by_dir: 1
        - by_sample_outdir: 1
```




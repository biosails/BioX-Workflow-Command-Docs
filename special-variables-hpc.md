# Special Variables - HPC

If you take a look at any of they NYUAD Production Pipelines, you will see that there are HPC variables. These are for use with [HPC-Runner-Command](https://jerowe.gitbooks.io/hpc-runner-command-docs/content/ "HPC-Runner-Command"). BioX-Workflow is designed to create a bash script that can run on any server anywhere, but if you are running a very large workflow on an HPC Environment, or even want to multithread among jobs, you can use the HPC parameters to make things run more smoothly.---

```
global:
...
 - HPC:
       - account: 'gencore'
       - module: 'gencore gencore_dev gencore_variant_detection'
       - partition: 'serial'
       - commands_per_node: 1
       - cpus_per_task: 1
rules:
  - samtools_mpileup:
      local:
        - HPC:
     #  - deps: gatk_printreads
        - walltime: '20:00:00'
        - mem: '28GB'
```

In the script produced by `biox run`, you would see

```
### HPC Directives
#HPC jobname=samtools_mpileup
#HPC mem=28GB
#HPC partition=serial
#HPC cpus_per_task=1
#HPC walltime=20:00:00
#HPC account=gencore
#HPC commands_per_node=1
#HPC module=gencore gencore_dev gencore_variant_detection
```

Notice that the jobname is automatically the created from the rule name, but can be overridden.


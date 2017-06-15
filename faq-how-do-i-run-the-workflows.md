# FAQ - How do I run the workflows?

BioX is a  [BioSails](https://github.com/biosails)  software release. It is meant to be completely standalone, and the bash script can be run from any HPC environment, lab server, or workstation.

### Quick Answer

You can run the script as is `chmod 777 my_workflow.sh` , can be run with another piece of software, or can be run using [HPC-Runner-Command.](https://jerowe.gitbooks.io/hpc-runner-command-docs/) It currently supports running with SLURM, PBS, SGE, or on a workstation. If you have a different architecture you would like to use, please [Contact Us.](/contact-us.md)

```
#on HPC with SLURM (default)
hpcrunner.pl submit_jobs --infile my_workflow.sh
#on HPC with PBS
hpcrunner.pl submit_jobs --infile my_workflow.sh --hpc_plugins PBS
#on HPC with SGE
hpcrunner.pl submit_jobs --infile my_workflow.sh --hpc_plugins SGE
#On a single server
hpcrunner.pl single_node --infile my_workflow.sh

```

### Longer Answer

BioSails has 3 major concerns.

1. Manage software \(see [FAQ - Where can I get software? \)](/faq-where-can-i-get-software.md).
2. Creating workflows 
3. Executing workflows across multiple architectures.

Part 2 is covered by BioX, and part 3 is covered by [HPC-Runner-Command](https://jerowe.gitbooks.io/hpc-runner-command-docs/).




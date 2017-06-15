# FAQ - Where can I get software?

That is an excellent question!

### Quick Answer

We use Anaconda along with the conda package manager to download and install software. If you don't have anaconda installed, and your HPC environment also does not have anaconda installed, we have a simple bootstrap method that will get you started.

```
wget https://raw.githubusercontent.com/nyuad-cgsb/nyuad-hpc-module-configs/master/scripts/bootstrap_anaconda3.sh
chmod 777 bootstrap_anaconda3.sh
./bootstrap_anaconda3.sh -h
./bootstrap_anaconda3.sh -a "place/to/install/anaconda3" #Defaults to $HOME/.local/anaconda3
```

### Long Answer

We recommend using [Anaconda](https://www.continuum.io/downloads) and the wonderful package manager conda . There are many groups that contribute software to conda, including dedicated groups for bioinformatics software. Each one of these groups is commonly referred to as a channel. We use the conda-forge, defaults,  r and [bioconda](https://bioconda.github.io/). Nyuad-cgsb also has its own channel where it hosts environments, which are groups of packages for analysis. Ideally, each production workflow gets its own analysis module. 

You can get more information the NYUAD analysis modules [here](https://jerowe.gitbooks.io/nyuad-gencore-hpc-modules/content/). This will be updated in the near future to include more descriptive summaries.

### Long Answer - With Modules!

Like most HPC groups we use 'Environment Modules' in order to manage complex software stacks. There is a bootstrap method for deploying gencore modules using[ EasyBuild ](https://github.com/hpcugent/easybuild) available  [here](https://raw.githubusercontent.com/nyuad-cgsb/nyuad-hpc-module-configs/master/scripts/deploy_gencore_modules.sh). For more information on deploying gencore modules, using conda software, or creating power user environments using conda, please see the  [contact-us.md](/contact-us.md)  page. The process to generate these modules is completely open source and open to anyone, but sadly lacking documentation.






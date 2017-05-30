# Quick Start - Command Line Operations

## Get Help

```
# Global Help
biox --help
biox-workflow.pl --help

# Help Per Command
biox run --help
```

## Run a Workflow

```
#Previously biox-workflow.pl --workflow workflow.yaml
biox run -w workflow.yml #or --workflow
biox-workflow.pl run --workflow workflow.yml
```

## Run a Workflow with make like utilities

Using the option --auto\_deps will create \#HPC deps based on your INPUT/OUTPUTs - use this with caution. It will only work correctly if INPUT/OUTPUT is complete and accurate.

```
biox run --workflow workflow.yml --auto_deps
```

## Create a new workflow

This creates a new workflow with rules rule1, rule2, rule3, with a few variables to help get you started.

```
biox new -w workflow.yml --rules rule1,rule2,rule3
```

## Add a new rule to a workflow

Add new rules to an existing workflow.

```
biox add -w workflow.yml --rules rule4
```

## Check the status of files in your workflow

You must have defined INPUT/OUTPUTs to make use of this rule. If you do, biox will output a table with information about your files.

```
biox stats -w workflow.yml
```




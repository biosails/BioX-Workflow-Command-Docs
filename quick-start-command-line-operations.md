# Quick Start - Command Line Operations

## Get Help

```
# Global Help
biox --help
biox-workflow.pl --help

# Help Per Command
biox run --help
```

```
biox --help
biox run --help
biox add --help
biox new --help
biox stats --help
biox validate --help
```

## Run a Workflow

```
biox run -w workflow.yml #or --workflow
biox-workflow.pl run --workflow workflow.yml
```

## Run a Workflow with a subset of Samples

```
biox run -w workflow --samples Sample_01
biox run -w workflow --samples Sample_01,Sample_02
```

## Run a Workflow with a subset of Rules

Assuming we have rules: rule1, rule2, rule3.

#### Select rules

```
#run rules: rule1,rule2
biox run -w workflow.yml --select_rules rule1,rule2
```

#### Match Rules

```
#Run rules rule1,rule2,rule2
biox run -w workflow.yml --match_rules rule
```

#### Omit Rules

```
#Run rules rule2,rule3
biox run -w workflow.yml --omit_rules rule1
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
biox add -w workflow.yml --rules rule4,rule5
```

## Check the status of files in your workflow

You must have defined INPUT/OUTPUTs to make use of this rule. If you do, biox will output a table with information about your files.

```
biox stats -w workflow.yml
```




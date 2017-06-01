# Global vs Local Variables

Variables are defined in two ways, globally and locally.

## Global Variables

Global variables are defined at the top of your file can be accessed at any point in your workflow.

Here is an example of a common global variable declaration.

```
---
global:
  - sample_rule: Sample_.*
  - indir: data/raw
  - outdir: data/processed
  - find_sample_bydir: 1
  - by_sample_outdir: 1
  - root_dir: data

```

## Local Variables

Local variables are declared per rule, and can only be accessed with in a rule. A local variable can override a global variable, but will only override it in that rule.

```
rules:
  ##This start the beginning of a rule.
  - qc:
      ##This starts the variable declaration
      local:
        - root_dir: 'data/analysis'
        - INPUT: '{$self->root_dir}/some_input_qc'
        - OUTPUT:
            - some_output_qc
      ##This is what we actually want to output
      process: |-
        INDIR: {$self->indir}
        INPUT: {$self->INPUT}
        outdir: {$self->outdir}
        OUTPUT: {$self->OUTPUT->[0]}
```

Notice that _root\_dir_ has been declared in the global and local rule. For the qc rule_ root\_dir _would be evaluated as 'data/analysis' \(with the full path name, of course\), but throughout the workflow would be available as 'data'.


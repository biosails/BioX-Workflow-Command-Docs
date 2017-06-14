# Advanced - Complex Variables

Having a big list of variables in global will probably become unweildly after some time. Its nice to put variables in categories.

```
---
global:
...
  ## Comples dir declaration
  - some_dir:
      dir1: 'my_awesome_dir2'
      dir2: 'my_awesome_dir2'
  ## Complex object
  - gatk_args:
      library: 1
      pcrModel: 1
      goldenVcf: 0
      ploidy: 2
  ## Complex List    
  - references:
    - ensembl_reference: Homo_sapiens.GRCh38.dna.toplevel.fa
    - dbsnp: Homo_sapiens-dbSNP-GRCh38-00-All.vcf.gz
```

Keep in mind that if you see a '-' in front of a sub grouping, it is a part of a list, and if you don't its an object.

You would access the objects like so -

```
DIR1: {$self->some_dir->{dir1}}
/home/user/workflows/my_awesome_dir2
Ensembl Reference: {$self->references->[0]->{ensembl_reference}}
DBSnp Reference: {$self->references->[1]->{dbsnp}}
```

Don't forget the extra '{' around all but the top level name!!


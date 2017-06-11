# Why use BioX?

BioX aims to help researchers get their computational analysis up and running with as little pain as possible. It comes with a robust set of defaults to keep analyses consistent across projects, and to let researchers concentrate on the analysis and not writing continuous for loops to find samples.

BioX has several areas of focus.

1. Be transparent.
2. Assist in reproducible research.
3. Keep support staff from losing their minds.

### Transparency

The common denominator of all computation work is bash. The [samtools manpage, ](http://www.htslib.org/doc/samtools.html)[gatk tool documentation](https://software.broadinstitute.org/gatk/documentation/tooldocs/current/org_broadinstitute_gatk_engine_CommandLineGATK.php), [multiqc docs](http://multiqc.info/docs/),  etc are all accessible as command line programs through bash.

We are a bit obsessed with bash.

### Reproducible Research

Sharing a workflow with another group is an easy as sharing a your yaml workflow file. Rerunning an analysis is as easy as executing 'biox run' from your project directory. BioX runs your analysis from a single file, making it very easy to quickly get an idea of the steps of a workflow.

### Support your support staff

Anytime anyone tells me about a problem the first thing I ask for is the bash code that is executed. Anytime I ask for help the first thing I am asked for is the bash code.

Keeping your code base transparent and your directory structure consistent will go a very long way in order to make your analysis understood by people who are not you.

# Why not use BioX?

There are of course cases where you would not want to use BioX. If you are writing a large amount of custom code, and not using mostly command line applications, it is probably not desirable to use BioX. If you dislike the default settings, or generally prefer to use software that does not come with so many default parameters, BioX is not for you.


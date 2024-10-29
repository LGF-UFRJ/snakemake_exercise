# Snakemake exercise

## Introduction

Hello everyone,

This is a snakemake exercise. The aim is to apply everything that I showed you  
so far. Maybe its a bit hard, but try your best until next meeting, where I'll 
show you the way I did it.

Before anything, please copy all the contents of this folder to another folder
of your choice and work on your copy. Please do not change anything here so
others can copy everything and practice as well.

## The goal

The goal of this exercise is to write a snakemake workflow that process the 
reads present inside the `fastqs/` folder. Your workflow should be able to
properly trim the reads and map them to the reference genome.

## About the data

The data you are analyzing comes from mouse and there are two conditions. One
of the samples (the one with `BACH` in the name) is a knockout for the Bach1
gene and the other one (`CTRL_CTRL`) is the control sample. These are *paired* 
RNA-Seq reads, where the fastq files ending in "_1" are the the R1 and the ones 
ending in "_2" are the R2.

## Tips to make this work

If you want to make this exercise *extra hard*, skip this tips section.

Here are some tips/guidance on how to complete this exercise:

### 1. Create a samplesheet

Create a script that generates a samplesheet with an easier name for the 
sample, which replicate it is and the path to the fastq files. Don't make this 
part of your workflow, just simply run it and add the samplesheet file path to 
your config file. 

### 2. Make a rule that links the names you created to the fastq files

Now inside your workflow, create a rule that gets the samplesheet as input
and generate links to the fastq files inside the `links/` folder. This can be
done with a simple python/R/awk/bash script that reads the samplesheet and for
each line executes the `ln` command linking the path to fastq file with the name
you created for the sample, together with which replicate it is. Example:
`ln -s  fastqs/sgBACH1_sgCTRL1_1.fq  links/bach1_R1.fq`.

### 3. Run the trimming and mapping using wildcards

Now that you have the links to these samples with more convenient names, you can
use parts of the names as wildcards to facilitate running the other rules. 
Expand (using the `expand()` function) the outputs you want in the `input` 
section of the target rule `all`. For the trimming, remember that although the 
trimming run with both pairs and the outputs are per file and will generate
results for each file adding the suffix "_val_1.fq" for R1 and "_val_2.fq" for
R2. The template for the trimming command is the following:

```
trim_galore -o trim/ --paired {input.r1} {input.r2}
```

On the other hand, the mapping will have as input both pairs, but the output
will be per sample (e.g. `bach1_Aligned.out.bam`) and the output of the mapping
adds a "Aligned.out.bam" to the end of the output file. Here's a template to 
perform the mapping with `STAR`*:

```
STAR --genomeDir {params.index} \
--readFilesIn {input.r1} {input.r2} \
--outFileNamePrefix map/{params.sample}_ \
--outSAMtype BAM Unsorted \
--runThreadN {threads}
```

I made this command multi line for visualization purposes. The path to the STAR 
index for the mouse genome is already present in the config file 
`config/config.yaml`. 

To do this exercise I used the `rna-seq` environment, which has snakemake, trim
galore and STAR installed. So before running your workflow do:

```
mamba activate rna-seq
```

## Signing off

Try your best to finish this until next week. If you don't manage to do it,
don't worry at all. I'll show you how to do it in the next meeting and I hope 
everything will be more clear. I think my solution can also serve as a template
and make things easier from now on.

Best,
Tarcisio


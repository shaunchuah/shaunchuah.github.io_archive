---
title: How to Create Your Bioinformatics Pipeline with Nextflow
date: "2021-08-12"
template: "post"
draft: false
slug: "bioinformatics-pipeline-with-nextflow"
category: "Bioinformatics"
tags:
  - "Bioinformatics"
  - "Docker"
  - "Snakemake"
  - "Nextflow"
description: "Now that you know how to run bioinformatics software in Docker containers, it's time to connect them up."
socialImage: "/media/pipeline.jpg"
---

![Create your bioinformatics pipeline](/media/pipeline.jpg)

Now that you know how to run bioinformatics software in Docker containers, it's time to connect them up. If you've missed the last post the link is here: [Getting started with Docker for bioinformatics.](/posts/getting-started-with-docker-for-bioinformatics)

## Content Overview

1. What is a pipeline?
2. Nextflow vs Snakemake
3. Using Nextflow and Docker containers to create your pipeline
4. Summary

## What is a pipeline anyway?

The term 'pipeline' is thrown around a lot in bioinformatics. In simple terms, it refers to the programs that have to be run in a certain order to complete the analysis. Some of these programs take the outputs of earlier programs and process them in order to achieve a specific objective.

This can be as simple as a couple of programs or it can become a messy spider web. A basic example would be: raw fastq files from the sequencer --> passing it through an aligner against the human genome --> variant calling.

NF core have a nice set of pipelines [here](https://nf-co.re/pipelines) including much more complex ones.

## How to get started

There are two key frameworks that I will point you to: [nextflow](https://www.nextflow.io/) and [snakemake](https://snakemake.readthedocs.io/en/stable/]). These are the programs that will run your programs for you and orchestrate the input and output files.

## Nextflow vs Snakemake

Disclaimer: this is my personal opinion on this topic. Try both and make up your mind as to which suits your thought process better.

### 1. The programming language difference doesn't matter.

Snakemake is based on python whereas nextflow uses groovy (which is like python for java). Initially when I first came across these comparisons I immediately jumped to snakemake as I'm pretty comfortable with python.

Having tried both now, I would say that since you need to learn the pipeline syntax anyway, the actual difference between python and groovy is pretty minimal.

### 2. The biggest difference is the 'thinking direction' of the pipeline

Let me explain what I mean. In snakemake you have to 'think backwards' and work your way from the desired output towards the input. I find nextflow way more intuitive in that you think about your pipeline in sequence of how you run your programs.

**Here's how you have to think in snakemake:** I need output C which comes from program B and to get program B to run you need the output of program A.

**Compared to nextflow:** With my input files I run it in program A and then take it to program B. Output C will result.

### 3. Why I switched to Nextflow: The separation of how the pipeline is run from what the pipeline is

The key reason why I switched from snakemake to nextflow is 'the decoupling of pipeline execution' or in plain terms, the separation of where and how your pipeline is run and what the pipeline actually does. The series of steps is described in the main file while the execution can be controlled by the configuration. We will cover pipeline execution in a separate article.

## Nextflow Pipeline

It is now time to go into more detail on setting up your pipeline in nextflow. Here's an example pipeline which takes in Illumina paired reads, runs them through fastqc and bowtie2 and perform some simple statistics on the aligned file.

### Prerequisites

1. Install Nextflow
2. Install Docker

### 1. Set up and define your input and outputs

First we set up a couple of parameters that can be easily changed depending on whether you are running test files vs actual data. These parameters can be modified in your config profiles.

```sh
// main.nf file
#!/usr/bin/env nextflow

// PIPELINE PARAMETERS HERE

// Input Files
params.reads = "$baseDir/data/*/*_{R1,R2}_*.fastq.gz"

// Report Directory
params.outdir = 'reports'

// Reference Genomes
params.bowtie2_reference_index = "$baseDir/reference_db/bowtie2/bt2_index.tar.gz"
bowtie2_db_ch = Channel.value(file("${params.bowtie2_reference_index}"))
```

### 2. Create 'channels' and feed your input into them

In nextflow, there is this concept of 'channels'. The basic premise is simple - each channel represents one file that can only be consumed once (unless it's a 'value channel'). Think of channels as pipes that you can feed files into which can be used only once.

In this example, we generate the channels using the fromFilePairs method and create 2 channels - `fastqc_reads` and `reads_for_alignment`. \*side note this is using the standard nextflow language rather than their newer DSL2.

```sh
reads = Channel.fromFilePairs(params.reads)
reads.into { fastqc_reads; reads_for_alignment }
```

### 3. Now let's have a look at our first process and break it down

Here is the process block - this process is named `fastqc_run` but you can change this to whatever name suits.

```sh
process fastqc_run {
    publishDir "$params.outdir/fastqc/$sample_id/", mode: 'copy'
    container 'biocontainers/fastqc:v0.11.9_cv8'
    cpus 16

    input:
    tuple val(sample_id), file(reads_file) from fastqc_reads

    output:
    file "*_fastqc.{zip,html}"

    script:
    """
    fastqc $reads_file -o . --threads ${task.cpus}
    """
}
```

In general, there are 4 components to each process:

1. A configuration block
2. Process inputs
3. Process outputs
4. Script that runs the desired program

Let's break down the above process codeblock into the 4 components.

#### Configuration

```
  publishDir "$params.outdir/fastqc/$sample_id/", mode: 'copy'
  container 'biocontainers/fastqc:v0.11.9_cv8'
  cpus 16
```

`publishDir` This allows you to copy the output files of this process to a desired location for easy access. Note that by default it creates a shortcut link to the actual file location and you have to explicitly specify for it to copy and give you the actual file.

`container` **This is where the real magic happens.** If you specify a publicly available Docker container, Nextflow will seamlessly pull the container in, run the script and generate the output that you're looking for. \*side note on the config you will need to specify docker enabled as true.

`cpus` This specifies the amount of CPUs you wish to run for this process. You can also specify memory options and will help in autoscaling your cloud computation needs. In general this option is useful if you are using VMs of varying size and works quite well with Google Cloud.

#### Inputs

```
  input:
  tuple val(sample_id), file(reads_file) from fastqc_reads
```

The `Channel.fromFilePairs` method generates a tuple `[wildcard value, [the pair of files for processing]]`. Read [here](https://www.nextflow.io/docs/latest/channel.html#fromfilepairs) for full documentation.

So when we call it into the input, we assign the variable name `sample_id` to the wildcard value and call the files `reads_file`. As a side note, if you need to access the first file you can call `reads_file[0]` to do so and the second file is `reads_file[1]`.

#### Outputs

```
  output:
  file "*_fastqc.{zip,html}"
```

Here we are telling nextflow to expect a zip file and a html file from the output of fastqc. Since we declared `publishDir` above, nextflow will then copy these files to the report directory.

#### Script

Finally, let's tell nextflow how to run fastqc!

```
  script:
  """
  fastqc $reads_file -o . --threads ${task.cpus}
  """
```

A couple of comments. `-o .` tells fastqc to place the output in the current working directory - this will be located in nextflow's work directory. `--threads ${task.cpus}` is how we parallelise fastqc to take advantage of the available cpus for this process.

### 4. Here is how the pipeline continues

The code below is hopefully pretty straightforward. The output from bowtie2 is split into two channels `aligned_ch` and `stats_ch`. `stat_ch` is sent to samtools to run flagstat and `aligned_ch` continues on for onward processing in the next steps of the pipeline.

```
process bowtie2 {
    container 'biocontainers/bowtie2:v2.4.1_cv1'
    cpus 16

    input:
    tuple val(sample_id), file(reads_file) from reads_for_alignment
    file db from bowtie2_db_ch

    output:
    tuple val(sample_id), file('*.sam') into aligned_ch, stats_ch

    script:
    """
    tar -xvf $db
    bowtie2 -t -p ${task.cpus} -x bowtie2/GRCh38_bowtie2 -1 ${reads_file[0]} -2 ${reads_file[1]} -S ${sample_id}.sam
    """
}

process samtools_flagstat {
    publishDir "$params.outdir/samtools_flagstat/"
    container 'biocontainers/samtools:v1.9-4-deb_cv1'
    cpus 16
    tag "$sample_id"

    input:
    tuple val(sample_id), file(sam_file) from stats_ch

    output:
    path "${sample_id}_flagstat.txt"

    script:
    """
    samtools flagstat -@ ${task.cpus} $sam_file > ${sample_id}_flagstat.txt
    """
}
```

## Summary

The above is a simple introduction to using nextflow to pull in containers and orchestrate a pipeline. It is well worth investing some time to automate your pipeline - write your code once and use it forever!

More importantly, this is one step closer to creating your cloud genomics supercomputer. We will cover this next.

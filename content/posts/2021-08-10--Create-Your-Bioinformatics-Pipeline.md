---
title: Create Your Bioinformatics Pipeline
date: "2021-08-10"
template: "post"
draft: false
slug: "create-your-bioinformatics-pipeline"
category: "Bioinformatics"
tags:
  - "Bioinformatics"
  - "Docker"
  - "Snakemake"
  - "Nextflow"
description: "Now you know how to run bioinformatics software in Docker containers, it's time to connect them up."
socialImage: "/media/pipeline.jpg"
---

![Create your bioinformatics pipeline](/media/pipeline.jpg)

Now you know how to run bioinformatics software in Docker containers, it's time to connect them up. If you've missed the last post the link is here: [Getting started with Docker for bioinformatics.](/posts/getting-started-with-docker-for-bioinformatics)

## What is a pipeline anyway?

The term 'pipeline' is thrown around a lot in bioinformatics. In simple terms, it refers to the programs that have to be run in order to complete the analysis. Some of these programs take the outputs of earlier programs and process them in order to achieve a specific objective.

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

The key reason why I switched from snakemake to nextflow is what is called 'the decoupling of pipeline execution' or in plain terms, the separation of where and how your pipeline is run and what the pipeline actually does. This

_Stay tuned for more articles where I will explore the technological shifts happening not just in tech but also health and biology_

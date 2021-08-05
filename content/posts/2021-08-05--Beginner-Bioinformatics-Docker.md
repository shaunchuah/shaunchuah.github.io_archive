---
title: Getting Started with Docker for Bioinformatics
date: "2021-08-05"
template: "post"
draft: false
slug: "getting-started-with-docker-for-bioinformatics"
category: "Bioinformatics"
tags:
  - "Bioinformatics"
  - "Docker"
description: "Stop wasting your time installing every single bioinformatic tool. Just call the container in from the cloud and go."
socialImage: "/media/containers.jpg"
---

![Docker for Bioinformatics](/media/containers.jpg)

Next generation sequencing is becoming much more accessible to researchers in 2021. As you stare at the freshly minted .fastq files, you're wondering - how do I go about analysing this?

After a stint on Google, you decide that you want to run bwa-mem/bowtie2 and then send the output into samtools. Next thing you know, you're trying to install half a dozen bioinformatic programs on your new ubuntu machine. You run into dependency hell or else conda seems to be stuck solving god knows what and this quickly eats up half your day.

**Stop wasting your time installing software - there is a secret to bypass all of this - Docker!**

![Docker logo](/media/docker2.png)


There are Docker containers for most bioinformatic tools. Instead of installing software, all you have to do is specify the Docker image and you're ready to run the program!

## Here's the secret

```sh
docker run -it -v '<path to your files>:/data' <container of your choice>
```

This immediately spins up the shell version of the bioinformatic tool and you can immediately run the commands needed to execute the program.

### Bowtie2 Example

Assuming your directory structure looks like this:

```
sequencing_data/
├─ reads/
│  ├─ sample_id/
│  │  ├─ read_1.fastq.gz
│  │  ├─ read_2.fastq.gz
├─ bowtie2/
│  ├─ GRCh38_bowtie2.fa
```

```sh
docker run -it -v '~/sequencing_data:/data' biocontainers/bowtie2:v2.4.1_cv1

bowtie2 -t -p 4 -x bowtie2/GRCh38_bowtie2 -1 reads/sample_id/read_1.fastq.gz -2 reads/sample_id/read_2.fastq.gz -S output.sam
```

## Here's the breakdown of the magic

**-it**: This opens an interactive shell into the running container

**-v 'path-you-want-to-access:/data'**: This is the key command that binds your computer's files into the container. For most biocontainers the target is /data but this may need tweaked depending on the container. This allows the program in the container to access and compute the files on your local computer. Understanding this was the key bit that unlocked the power of Docker in my mind.

## How to find Docker containers

For most things, just google the tool you want and add Docker eg. 'bwa docker', 'metaphlan docker'

Here are a list of public container registries with amazing software:

- https://hub.docker.com/u/biobakery
- https://hub.docker.com/u/biocontainers
- https://hub.docker.com/u/staphb

Give this a try - 10-15 mins of experimenting will save you days of time and you will never have to manage dependencies anymore!

## What next?

Once you have got your head round using Docker for your bioinformatics, the next thing that comes to mind is to automate the containers locally on your computer so that you can easily run 100 read files at the touch of a button. I will cover this in a future article.


_Stay tuned for more articles where I will explore the technological shifts happening not just in tech but also health and biology_

---
title: Launching Nextflow Pipelines from the Cloud
date: "2022-04-28"
template: "post"
draft: false
slug: "launching-nextflow-pipelines-from-the-cloud"
category: "Bioinformatics"
tags:
  - "Bioinformatics"
  - "Azure"
  - "Nextflow"
  - "Cloud computing"
  - "DevOps"
description: "Let's orchestrate your cloud supercomputer."
socialImage: "/media/orchestra.jpg"
---

![Launching nextflow pipelines from the cloud](/media/orchestra.jpg)

Slightly overdue post - have been busy running other things but here's the promised guide on orchestrating a nextflow pipeline from a cloud server.

## Why bother setting up another server to manage your Nextflow pipeline?

1. **Avoid premature termination of the pipeline**. When you run a Nextflow pipeline from your local computer, your local computer is managing the tasks and communicating with Azure as jobs are completed. Depending on the complexity of your pipeline, this may be a long time (couple of days!). If your wi-fi router accidentally comes unplug or the connection is broken, the pipeline will terminate prematurely. While you could recover from this using `-resume`, you can avoid the risks of disconnection by using a cloud server, which is designed to run 24/7, to orchestrate this.

2. **Location independence**. You can start a pipeline from home (wfh being the consequence of covid) and perhaps go elsewhere and check in later.

3. **Free up your local computer for other things**. You do not have to keep your local computer running Nextflow constantly - just start the pipeline in the cloud and continue on with your usual day.

## Table of Contents

1. [Obtaining a cloud server](#1-obtaining-a-cloud-server)
2. [Logging in to the server](#2-logging-in-to-the-server)
3. [Installation](#3-installation)
4. [Check Nextflow works](#4-check-nextflow-works)
5. [Clone and run the pipeline](#5-clone-and-run-the-pipeline)
6. [Use screen to launch long-running pipelines](#6-use-screen-to-launch-long-running-pipelines)

## 1. Obtaining a cloud server

Plenty of hosting providers provide you with access to a VPS (Virtual Private Server) running Ubuntu 20.04. My recommendation is DigitalOcean - fantastic documentation, much cheaper compared to AWS/Azure/GCP, good performance and reliability in my experience. The $5/month server is what I personally use and is more than capable of handling Nextflow.

### Setting up an Ubuntu server on DigitalOcean

![DigitalOcean VPS Setup](/media/do_droplet_creation.png)

### Server authentication

For authentication, select SSH key to access your server from the terminal (mac/linux) or using PuTTY (windows):
![DigitalOcean SSH Setup](/media/do_droplet_ssh.png)

## 2. Logging in to the server

Once your server is set up, log in to it.

Mac/Linux:

```sh
ssh -i <private_key.pem> root@<server_ip>
```

For Windows I suggest using the PuTTy client. Depending on your choice of VPS and platform, you may wish to consult this guide: <https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-20-04>

## 3. Installation

Install Java and screen first.

```sh
sudo apt update
sudo apt install default-jre screen
```

We will install Nextflow in /nextflow. You can change this location if you wish.

```sh
cd /nextflow
curl -s https://get.nextflow.io | bash
```

We then need to add this directory to PATH. Doing this allows you to type `nextflow` directly into the terminal. Open .bashrc file to begin.

```sh
nano ~/.bashrc
```

Add the bottom, add the following line:

```sh
export PATH=“/nextflow:$PATH”

# this line is optional
export NXF_DEFAULT_DSL=1 
# I am using DSL1 as per the docs when I was setting up my pipeline.
# If you are using DSL1 you will need to add this to .bashrc.
```

Save and exit with `Ctrl+X` and `yes`. Next, we need to source the file.

```sh
source ~/.bashrc
```

## 4. Check Nextflow works

If everything is installed correctly, typing `nextflow` into the terminal should show the program help screen.

```sh
nextflow
```

## 5. Clone and run the pipeline

Now it's time to bring in your pipeline using the power of git.

```sh
cd ~
git clone <your_nextflow_pipeline_repository>
```

For example, I would put in

```sh
git clone https://github.com/shaunchuah/cfdna_nextflow/
```

and this will bring in my pipeline at `~/cfdna_nextflow`.

For my pipeline I would need to clone `sample_credentials.json` to `credentials.json` and fill it in with my Azure credentials.

```sh
cd ~/cfdna_nextflow
cp sample_credentials.json credentials.json
nano credentials.json
```

Now you can launch the pipeline!

```sh
nextflow pipeline.nf -resume -profile test
```

Now your cloud server is orchestrating the cloud cluster! But let's take this one step further...

## 6. Use screen to launch long-running pipelines

Reference: <https://www.howtogeek.com/662422/how-to-use-linuxs-screen-command/>

Here's what I do when launching pipelines that may take days to weeks to complete. Create a screen session:

```sh
screen
```

If this is the first time, there will be some text. Hit enter and it all disappears but you are now in a screen session. Try any command - eg. `nextflow`.

To detach from the screen session, hit `Ctrl+A` and `D`.

To list all screen sessions type `screen -ls`.

To reattach to the screen session type `screen -r`.

I launch my pipelines from within screen and hit `Ctrl+A` and `D` to detach. Now I can safely close my SSH connection to the server and the pipeline will remain running. Reconnect to the server and type `screen -r` and you will see that the pipeline remains running. This nifty trick will help avoid pipelines terminating because of flaky connections and give you true mobility to perform bioinformatics from anywhere with an internet connection.

It also means that in 2022, you no longer need powerful desktop computers to run bioinformatics anymore. All you need is a credit card and you can access vast computing power way beyond any single machine is capable of.

I hope you've found this series helpful. If this has helped you please let me know on Twitter!

_Bonus: If you want to take this even further, you could have a powerful cloud server to develop on using VSCode Remote Containers. Check out [GitHub Codespaces](https://github.com/features/codespaces) as an example of this._

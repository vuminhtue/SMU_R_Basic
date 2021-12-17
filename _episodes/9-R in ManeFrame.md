---
title: "R workflow in ManeFrame"
teaching: 10
exercises: 0
questions:
- "How to run R in ManeFrame"
objectives:
- "Learn how to run R & R Studio in ManeFrame"
- "Learn how to install package in R in ManeFrame"
keypoints:
- "R workflow"
- "ManeFrame"
---

## Run R in terminal via command line

Request a compute node if you do not have one (here I request for a node with 6 cpus and 16gb ram, running interactively):

```bash
$ srun -p htc -N 1 -c 6 --mem=16G --pty $SHELL
```

Load python module

```bash
$ module load r/3.6.2
```

Open R and run in Ineractive mode to install package:

```bash
$ R
> install.packages("doParallel")
```

## Run R in batch mode
Make sure that you loaded R but not in R environment. Assume that you have an R script named hello.R (see next session if you do not have one)
There are 2 ways to run an R script:

```bash
$ Rscript a.R
$ R CMD BATCH a.R
```

The latter method using **R CMD BATCH** will write the separate output file to your working directory

Now you are ready with the R pipeline running on ManeFrame

## Prepare a simple script (skip if you already have one)
Here I create a simple R script with following information and named it **hello.R**

```python
rm(list=ls())
setwd('/user/tuev')
print('welcome to R in ManeFrame')
print("hello from ManeFrame")
a <- 3
b <- 4
c <- a**b+b
print(c)
```

## Running interactive Rn while in a login node using srun
While it is not recommended to run any job in a login node, we encourage every user to run their own job in compute node.
If you are in a login node, you can run a job via this command:

```bash
$ module load r/3.6.2
$ srun -p htc --mem=6G R CMD BATCH hello.R
```

## Running interactive R from a compute node
First, request an interactive node:

```bash
$ srun -p htc --mem=6G --pty $SHELL
```

Then load R and run your script:

```bash
$ module load r/3.6.2
$ R CMD BATCH hello.R
```

## Running non-interactive Python while in a login node using sbatch
Still in a login node, using **sbatch --wrap** to wrap a command in an sbatch script, then submitted to the queue to run in batch mode (non interactive)

```bash
$ sbatch -p htc --mem=6G --wrap "module load r/3.6.2; time R CMD BATCH hello.R"
```

You will find the output in the same folder with the format of **slurm-$JobID.out**.


## Running non-interactive Python while in a login node via a batch script (most advance)
First create a batch script named _**myR_batch.sh**_

```bash
#!/bin/bash
#SBATCH -J R
#SBATCH -o R_%j.out
#SBATCH -p htc
#SBATCH --mem=6G

module purge
module load r/3.6.2

time R CMD BATCH hello.R
```

Then, while in a login node, submit a batch script 

```bash
$ sbatch myR_batch.sh
```

You can see the output as specified in the output file R_%j.out and hello.Rout


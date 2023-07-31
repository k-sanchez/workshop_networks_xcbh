---
layout: page
title:  "Standard tree inference"
categories: jekyll update
usemathjax: true
---

<script type="text/javascript" charset="utf-8" 
src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML,
https://vincenttam.github.io/javascripts/MathJaxLocal.js"></script>


We will use a dataset of Philippine Puddle Frogs (_Occidozyga laevis_ complex) from Chan et al ([2021](https://doi.org/10.1093/sysbio/syab034)). The dataset consists of genome-wide exons and introns obtained through sequence capture, however, we will only use a subset of XXX introns.

To estimate trees from these exons, we will rely on <span style="font-variant: small-caps;">RAxML</span>, a program for efficient tree inference using Maximum likelihood. We also will estimate branch supports through Bootstrap, and perform a partitioned analysis


# Download and install

Download the latest version from https://github.com/stamatak/standard-RAxML/archive/refs/heads/master.zip. Alternatively, if you use UNIX and have git, in the terminal type:

```sh
git clone https://github.com/stamatak/standard-RAxML.git
```

to download the repo. Uncompress the `.zip` and move to the newly created folder.

## Windows

Windows executables are already included. To run the software, open the command prompt (`cmd.exe`) and type the path to the executable

```powershell
.\raxmlHPC
```

## Unix

Requires to be compiled before use.

Install the standard version:

```sh
make -f Makefile.gcc # install the standard version
```

Install the multicore version:

```sh
rm *.o # remove previously compiled files 
make -f Makefile.PTHREADS.gcc
```
This will create an executable `raxmlHPC` (or `raxmlHPC-PTHREADS`, depending on the compiled version) that can be called:

```sh
./raxmlHPC
# The following error message must appear:
# Error, you must specify a model of substitution with the "-m" option
```

# Standard tree inference

We will estimate a tree of one intron based on the $\text{GTR} + \Gamma$ model of [nucleotide substitution](https://en.wikipedia.org/wiki/Substitution_model):

```sh
./raxmlHPC -s <input> -n stand -m GTRGAMMA -p <random number>
```

- `-s`: name of the sequence file (or path/name if the file is in a different folder)
- `-n`: name of the output files (here the files will have `.stand` appended to the end)
- `-m`: substitution model
- `-p`: random number seed to generate parsimony starting tree

Further command options are detailed in the software manual.

The maximum likelihood tree is printed in the `RAxML_bestTree.stand` file.

## Loop over the n introns



# Bootstrapping

```sh
./raxmlHPC -s <input> -n boot -m GTRGAMMA -f a -N 100 -p -x <random number> -x <random number>
```

- `-f`: Specify the algorithm. If nothing is specified (like our first run), it executes the standard hill climbing algorithm to perform the tree search by default (which is equivalent to `-f d`). The `a` option tells <span style="font-variant: small-caps;">RAxML</span> to conduct a rapid Bootstrap analysis and search for the best-scoring ML tree in a single run
- `-N`: number of bootstrap replicates
- `-x`: specify an integer number (random seed) and turn on rapid bootstrapping

The Bootstrap trees are written in `RAxML_bootstrap.boot` and the ML tree with support values is written in `RAxML_bipartitions.boot`.

---
layout: page
title:  "Standard tree inference"
categories: jekyll update
usemathjax: true
---

<script type="text/javascript" charset="utf-8" 
src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML,
https://vincenttam.github.io/javascripts/MathJaxLocal.js"></script>


We will use a dataset of Philippine puddle frogs (_Occidozyga laevis_ complex) from Chan et al. ([2021](https://doi.org/10.1093/sysbio/syab034)). It consists of genome-wide exons and introns obtained through sequence capture, however, we will only use a subset of XXX introns.

To estimate trees from these introns, we will rely on <span style="font-variant: small-caps;">RAxML</span>, a program for efficient tree inference based on maximum likelihood (ML). We will also estimate branch supports through Bootstrap

## Download and install

Download the latest version from [GitHub](https://github.com/stamatak/standard-RAxML/archive/refs/heads/master.zip). Alternatively, if you use UNIX (Linux or Mac) and have `git` installed, open the terminal and type:

```sh
git clone https://github.com/stamatak/standard-RAxML.git
```

to download the repository. Uncompress the `.zip` and move to the newly created folder.

### Windows

Windows executables are already included in the folder. To run the software, open the command prompt (`cmd.exe`) and type the path to the executable

```powershell
.\raxmlHPC
# The following error message must appear:
# Error, you must specify a model of substitution with the "-m" option
```

Be aware that Windows uses the backslash `\` as the path-component separator, while Unix uses the forward slash `/`.

### Unix

First, we need to compile the software before it can be used.

Install the standard version:

```sh
make -f Makefile.gcc # install the standard version
```

Install the multicore version:

```sh
rm *.o # remove previously compiled files if you installed a different version
make -f Makefile.PTHREADS.gcc
```
This will create an executable `raxmlHPC` (or `raxmlHPC-PTHREADS`, depending on the compiled version) that can be called:

```sh
./raxmlHPC
# The following error message must appear:
# Error, you must specify a model of substitution with the "-m" option
```

## Tree inference

We will estimate a tree of one intron based on the $\text{GTR} + \Gamma$ model of [nucleotide substitution](https://en.wikipedia.org/wiki/Substitution_model):

```sh
./raxmlHPC -s intron1.phylip -n stand -m GTRGAMMA -p 456745
```

- `-s`: name of the sequence file (or path/name if the file is in a different folder)
- `-n`: name of the output files (the files generated during the run will have `.stand` appended to the end)
- `-m`: substitution model
- `-p`: random number seed to generate parsimony starting tree (can be any integer)

Further command options are detailed in the software manual.

The maximum likelihood tree is printed in the `RAxML_bestTree.stand` file.


## Bootstrapping

The bootstrap is a statistical approach for assessing the accuracy of any estimation (continuous parameters or clades in a tree); it consists of analyzing replicates of the original data. Its use in phylogenetic inference was introduced by Felsenstein ([1985](https://doi.org/10.1111/j.1558-5646.1985.tb00420.x)) to assess "confidence" for each clade in a tree. More specifically, in each of $N$ cycles ($N$ = 100 or 1000), the algorithm samples sites with replacement from the original alignment until the original number of sites is reached. A tree is estimated from each replicate, and the proportion of times that each clade is inferred provides a measure for its support. The support values are usually placed in the maximum likelihood tree.

The following code allows to infer a tree and estimate branch supports in a single run:

```sh
./raxmlHPC -s intron1.phylip -n boot -m GTRGAMMA -f a -N 100 -p -x 563454
```

- `-f`: Specify the algorithm. If nothing is specified (like in our first run), by default it executes the standard hill climbing algorithm to perform the tree search (which is equivalent to `-f d`). The `a` option tells <span style="font-variant: small-caps;">RAxML</span> to conduct a rapid Bootstrap analysis and search for the best-scoring ML tree in a single run
- `-N`: number of bootstrap replicates
- `-x`: Specify an integer number (random seed) and turn on rapid bootstrapping

The Bootstrap trees (100 in the example) are written in `RAxML_bootstrap.boot` and the ML tree with support values is written in `RAxML_bipartitions.boot`.
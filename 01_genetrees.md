---
layout: page
title:  "Standard tree inference"
categories: jekyll update
usemathjax: true
---

<script type="text/javascript" charset="utf-8" 
src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML,
https://vincenttam.github.io/javascripts/MathJaxLocal.js"></script>


For gene tree reconstructions, we will use a dataset of Australasian monitor lizards (genus _Varanus_) from Pavón-Vázquez et al. ([2021](https://doi.org/10.1093/sysbio/syaa102)). It consists of 337 nuclear loci obtained through anchored hybrid enrchment, a technique for capturing orthologous regions of the genome.

To estimate trees from these loci, we will rely on <span style="font-variant: small-caps;">RAxML</span>, a program for efficient tree inference based on maximum likelihood (ML). We will also estimate node supports based on bootstrap calculations.

## Download and install the software

Download the latest version from [GitHub](https://github.com/stamatak/standard-RAxML/archive/refs/heads/master.zip). Alternatively, if you use UNIX (Linux or Mac) and have `git` installed, open the terminal and type:

```sh
git clone https://github.com/stamatak/standard-RAxML.git
```

to download the repository. Uncompress the `.zip` and move to the newly created folder.

### Installing raxml in Windows

Windows executables are already included in the folder. To run the software, open the command prompt (`cmd.exe`) and type the path to the executable

```powershell
# use the `cd` command to change the directory
cd \path\to\raxml\WindowsExecutables_v8.2.10
.\raxmlHPC
# The following error message must appear:
# Error, you must specify a model of substitution with the "-m" option
```

Be aware that Windows uses the backslash `\` as the path-component separator, while Unix uses the forward slash `/`.

### Compiling raxml in Unix (Mac or Linux)

First, we need to compile the software before it can be used.

Install the standard version:

```sh
make -f Makefile.gcc # install the standard version
```

Alternatively, you can install the multicore version that allows the use of multiple CPU processors:

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

## Runniong raxml for gene tree inference

We will estimate a tree of one locus based on the $\text{GTR} + \Gamma$ model of [nucleotide substitution](https://en.wikipedia.org/wiki/Substitution_model):

```sh
./raxmlHPC -s locus177.phylip -n 177.stand -m GTRGAMMA -p 456745
```

- `-s`: name of the sequence file (or path/name if the file is in a different folder)
- `-n`: name of the output files (the files generated during the run will have `.177.stand` appended to the end)
- `-m`: substitution model
- `-p`: random number seed to generate parsimony starting tree (can be any integer)

Further command options are detailed in the software manual.

The maximum likelihood tree is printed in the `RAxML_bestTree.1.stand` file. We can visualize the tree in <span style="font-variant: small-caps;">FigTree</span> (download from [here](https://github.com/rambaut/figtree/releases/tag/v1.4.4)) and, optionally, export in any image format.

>Note: If we want output files to be written in a specific folder, we have to execute <span style="font-variant: small-caps;">RAxML</span> in that folder.  
Suppose that I want output files in a folder called `output/`:
```sh
cd output/
path/to/raxmlHPC -s path/to/locus177.phylip -n 177.stand -m GTRGAMMA -p 456745
```
To simplify this command, you can add the <span style="font-variant: small-caps;">RAxML</span> executable to the path ([follow this guide](https://zwbetz.com/how-to-add-a-binary-to-your-path-on-macos-linux-windows/)). This allows to execute the program from any directory.

## Bootstrapping

The bootstrap is a statistical approach for assessing the accuracy of any estimation (continuous parameters or clades in a tree); it consists of analyzing replicates of the original data. Its use in phylogenetic inference was introduced by Felsenstein ([1985](https://doi.org/10.1111/j.1558-5646.1985.tb00420.x)) to assess "confidence" for each clade in a tree. More specifically, in each of $N$ cycles ($N$ = 100 or 1000), the algorithm samples sites with replacement from the original alignment until the original number of sites is reached. A tree is estimated from each replicate, and the proportion of times that each clade is inferred provides a measure for its support. The support values are usually depicted in the maximum likelihood tree.

The following code allows to infer a ML tree and estimate branch supports in a single run:

```sh
./raxmlHPC -s locus177.phylip -n 177.boot -m GTRGAMMA -f a -N 100 -p 2334 -x 563454
```

- `-f`: Specify one of the different algorithms available in <span style="font-variant: small-caps;">RAxML</span>. If nothing is specified (like in our first run), by default it executes the standard hill climbing algorithm to perform the tree search (which is equivalent to `-f d`). The `a` option tells <span style="font-variant: small-caps;">RAxML</span> to conduct a rapid Bootstrap analysis and search for the best-scoring ML tree in a single run
- `-N`: number of bootstrap replicates
- `-x`: Specify an integer number (random seed) and turn on rapid bootstrapping

The Bootstrap trees (100 according to the example) are written in `RAxML_bootstrap.177.boot` and the ML tree with support values is written in `RAxML_bipartitions.177.boot`.

To visualize this tree and the support values open the file in <span style="font-variant: small-caps;">FigTree</span>. On the left-hand side of the screen select: <button>Branch Labels</button> &rarr; <button>Display</button> &rarr; `label`.

Let's estimate a tree for a different locus:
```sh
./raxmlHPC -s locus256.phylip -n stand -m GTRGAMMA -p 66786
```

Visualize both trees (`RAxML_bipartitions.177.boot` and `RAxML_bipartitions.256.boot`). You can download FigTree program from [here](http://tree.bio.ed.ac.uk/software/figtree/). 
Some of the phylogenetic relationships are different, what could be the reason/s?

<p style="text-align:center;">
    <img src="./assets/MLtrees.svg" width="80%"/>
</p>

>*Varanus komodoensis* is the famous Komodo dragon.

## Automatizing gene tree inference using a loop
It is possible to automatically set a run for all 388 gene trees using the code for a loop. Note that all .phy in the dataset folder are named L_1.phy, L_2.phy ... L_388.phy. Thus, we can set a loop with an interator i taking values from 1 to 388 to call all the input .phy into raxml:
```sh
for i in {1..388}
do
../raxmlHPC -s L_$i.phy -n $i.boot -m GTRGAMMA -f a -N 100 -p 2334 -x 563454
done
```

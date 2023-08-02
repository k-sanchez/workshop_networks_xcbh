---
layout: page
title:  "Network estimation"
categories: jekyll update
usemathjax: true
---

<script type="text/javascript" charset="utf-8" 
src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML,
https://vincenttam.github.io/javascripts/MathJaxLocal.js"></script>


Phylogenetic networks are an extension of phylogenetic trees used to model gene flow events between species or populations (see the figure). Specifically, these  events are modelled by reticulation edges that summarizes gene flow that might have occurred over a period of time into a single instantaneous event. These edges have a parameter associated ($\varphi$) that represents the proportion of alleles transferred during the entire period of gene flow.

net model image
 
 A popular method to estimate phylogenetic networks is <span style="font-variant: small-caps;">PhyloNetworks</span> ([Solís-Lemus et al. 2017](https://doi.org/10.1093/molbev/msx235)). It is based on a pseudolikelihood function over concordance factors (CFs) of quartets of taxa (four taxa), wich increase computational tractability. CFs are calculated as the proportion of gene trees supporting the three possible splits in a given quartet:
 
![](./imgs/CFs.png)*CFs calculation*

Hence, it is required the estimation of gene trees to obtain the table of CFs required to estimate the network. Interestingly, an R function was developed to compute the table of CFs directly from a SNPs matrix ([Olave and Meyer 2020](https://doi.org/10.1093/sysbio/syaa005)), which we will be using here on the *Liolaemus* datset.

<span style="font-variant: small-caps;">PhyloNetworks</span> is a package of Julia, an interactive programming language (like R). To install Julia, go to [http://julialang.org/downloads/](http://julialang.org/downloads/)
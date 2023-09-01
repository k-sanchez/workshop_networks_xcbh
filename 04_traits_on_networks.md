---
layout: page
title:  "Trait evolution on networks"
categories: jekyll update
usemathjax: true
---

## Transgressive evolution of traits
Using PhyloNetworks, it is possible to test for a trait that is inherited from the hybridization event itself: transgressive evolution.

Here we will run a demostration with the phenotypic data of *Liolaemus* species. Let's load the data:

```julia
using PhyloNetworks, StatsModels, DataFrames, CSV, PhyloPlots

cd("directory/analysis")

# load the data
traits = DataFrame(CSV.File("liolaemus_phenotype.csv"); copycols = false)
net1 = readTopology("liolaemus_neth1.newick")
plot(net1, :R,showEdgeNumber = true);
```

The data frame contains morphometric data of different *Liolaemus* species. Now, we will use the network we already estimated before to test for transgressive evolution of the snout-vent length (svl).

```julia
## Transgressive evolution test ##
df_shift = regressorHybrid(net1)
dat = innerjoin(traits, df_shift, on = :tipNames)
	
fit_sh = phylolm(@formula(svl ~ shift_3), dat, net1;
	tipnames = :tipNames,  reml = false)
	

fit_null = phylolm(@formula(svl ~ 1), dat, net1;
	tipnames = :tipNames, reml = false)
```
Now we performe a likelihood ratio test for model selection

```julia
lrtest(fit_sh, fit_null)
```
 
This attribute failed to show a significant result (sorry no fancy result here!). You can perform the same tests for the remaining attributes (check the different column names)
in the file `lioaemus_phenotype.csv`.

## Ancestral state reconstructions

Given a network, it is also possible to estimate ancestral state of traits. It is very simple, just follow:
```julia
fitTrait1 = phylolm(@formula(svl ~ 1), traits, net1, tipnames = :tipNames)

# Ancestral reconstruction
ancTrait1Approx = ancestralStateReconstruction(fitTrait1)

# Plot of character values (current and ancestral) on the network
plot(net1, :R, nodeLabel = expectationsPlot(ancTrait1Approx));
```

>Trait analyzes based on networks requires that branch lengths be consistent with time, i.e. time-calibrated.
The network we used here was already calibrated following Bastide et al. ([2018](https://doi.org/10.1093/sysbio/syy033)). 



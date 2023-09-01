---
layout: page
title:  "Trait evolution on networks"
categories: jekyll update
usemathjax: true
---

## Transgressive evolution of traits
Using PhyloNetworks, it is possible to test for a trait that is inherited from the hybridization event itself: transgressive evolution.

Here we will run a demostration with the phenotypic data of Liolaemus species. Let's load the data:

```julia
using PhyloNetworks, StatsModels, DataFrames, CSV, PhyloPlots

cd("directory/analysis")

# load the data
traits = DataFrame(CSV.File("liolaemus_phenotype.csv"); copycols = false)
net1 = readTopology("liolaemus_neth1.newick")
plot(net1, :R,showEdgeNumber=true);
```

The data frame contains morphometric data of different Liolaemus species. Now, we will use the network we already estimated before to test for transgressive evolution of snout-vent length (svl).

```julia
## Transgressive evolution test ##
	df_shift = regressorHybrid(net1)
	dat = innerjoin(traits, df_shift, on=:tipNames)
	
fit_sh = phylolm(@formula(svl ~ shift_3), dat, net1;
	tipnames=:tipNames,  reml=false)
	

fit_null = phylolm(@formula(svl ~ 1), dat, net1;
	tipnames=:tipNames, reml=false)
```
Now we performe a likelihood ratio test for model selection

```julia
lrtest(fit_sh, fit_null)
```

Result is not significant (sorry no fancy result here!)



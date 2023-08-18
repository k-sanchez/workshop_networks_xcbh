---
layout: page
title:  "Trait evolution on networks"
categories: jekyll update
usemathjax: true
---

Corroborar script:

```julia
using CSV
using DataFrames
using PhyloNetworks
using RCall
using StatsBase
using StatsModels

cd("directory/analysis")

# load the data
net1 = readTopology("liolaemus_neth1.phy");
traits = CSV.read("liolaemus_phenotype.csv", DataFrame)

## Transgressive evolution ##

df_shift = regressorHybrid(net1)
# esta tabla muestra las ramas afectadas por la reticulacion (columnas shift_*),
# la columna tipNames especifica las terminales afectadas

# here you can include correlated spatial effects (alt, lat, long), or not
df = innerjoin(select(traits, :sp, r"svl"),
               select(df_shift, Not(:sum)), # excludes the 'sum' column
               on = :sp => :tipNames) # join our data with shift predictors

# Fit the no-shift model
fit_noshift = phylolm(@formula(svl ~ 1), df, net1, tipnames = :sp, y_mean_std = true, reml = false)

# Fit the shifted model
fit_sh3 = phylolm(@formula(svl ~ shift_3), df, net1, tipnames = :sp, y_mean_std = true, reml = false)

coeftable(fit_sh3) # 95% confidence interval for shift includes 0
DataFrame(AIC = [aic(fit_noshift), aic(fit_sh3)],
		  deltaAIC = [aic(fit_noshift) - aic(fit_noshift),
					  aic(fit_noshift) - aic(fit_sh3)])
# we could run a likelihood ratio test (lrtest) to compare the two models,
# this is only possible with ML optimization
lrtest(fit_noshift, fit_sh3)
```
---
layout: page
title:  "Species tree inference"
categories: jekyll update
usemathjax: true
---

Species tree estimation is mainly based on the multispecies coalescent model (MSC; citas). This model specifies the probabilities of different gene trees that are embedded in a species trees (expandir esto?).

Species tree inference methods can be broadly classified into summary and full-likelihood approaches. The first class reduce the information in the sequences to summary statistics, while the second perform estimations directly from the alignments. As a result, the summary-based approaches are faster than full-likelihood methods. Here we will use two summary-based methods (often termed "heuristic approaches")

## ASTRAL

<span style="font-variant: small-caps;">astral</span> belongs to a family of species tree methods known as two-step, because it does not use the sequences but estimated gene trees from those sequences.

Download from https://github.com/smirarab/ASTRAL/archive/refs/heads/master.zip, or

```sh
git clone https://github.com/smirarab/ASTRAL.git
```

To run the sofware:

```sh
java -jar astral.5.7.8.jar -i <gene_trees> -i <imap> -o <out_name>
```

## SVDquartets

`svdquartets` is an algorithm that computes species trees directly from SNP data. However, it is not a full-likelihood approache since its summarizes the data as pooled site-pattern counts. This algorithm is implemented in <span style="font-variant: small-caps;">PAUP*</span>.


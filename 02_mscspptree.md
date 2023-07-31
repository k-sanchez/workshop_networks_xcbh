---
layout: page
title:  "Species tree inference"
categories: jekyll update
usemathjax: true
---

Species tree estimation is mainly based on the multispecies coalescent model (MSC; citas). This model specifies the probabilities of different gene trees that are embedded in a species trees (expandir esto?).

Species tree inference methods can be broadly classified into summary and full-likelihood approaches. The first class reduce the information in the sequences to summary statistics, while the second peerform estimations directly from the alignments. As a result the summary-based approaches are much more fast. In this part, we will use two summary-based approaches

## [Astral]{.smallcaps}

ASTRAL belongs to a family of methods designed to estimate species trees based on the mulstispecies coalescent model (MSCM). It is a two-step approach because it does not use the sequences but estimated gene trees from those sequences. It differs form full-likelihood implementations of the MSCM that uses the information present in the sequences, but are more computationally intensive.

Download from https://github.com/smirarab/ASTRAL/archive/refs/heads/master.zip, or

```sh
git clone https://github.com/smirarab/ASTRAL.git
```

To run the sofware:

```sh
java -jar astral.5.7.8.jar -i <gene_trees> -i <imap> -o <out_name>
```


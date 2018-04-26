---
layout: page
title: Research
permalink: /research/
---

My main area of research is to develop statistical and machine learning methods for the study of insects, particularly vectors of disease.  Through their saliva, mosquitoes such as *[Anopheles gambiae](https://en.wikipedia.org/wiki/Anopheles_gambiae)* and other flies transmit the parasites that causes diseases such as [malaria](https://en.wikipedia.org/wiki/Malaria). Disease such as malaria, [dengue fever](https://en.wikipedia.org/wiki/Dengue_fever), and [Zika](https://en.wikipedia.org/wiki/Zika_fever) are major threats to public health; by studying these insects, we aim to understand mechanisms of immunity and insecticide resistance to aid in the fight to eradicate these diseases.  My primary focus is population genetics, or the study of how the genomes of these insects evolve and change in response to changing environmental conditions.

Adjusted Likelihood Ratio Test
==============================
Single-SNP association tests are a popular and powerful statistical technique for identifying genomics variants that are associated with differences in population structure.  Recently, I proposed an adjusted likelihood ratio test that handles unknown variants by using an uninformative (uniform) prior over all possible genotypes.  I demonstrated that this approach significantly reduces false positives when compared with more commonly-used techniques such as $$F_{ST}$$ and can uncover variation missed by other methods.


Selection Procesess Driving Differences between Populations
===========================================================
Most population genetics studies sort samples into populations and then perform association tests between the variants and population labels.  This approach has a few downsides. For many insect species, populations tend to overlap due to intermating such that defining discrete memberships can be difficult.  In addition, pairwise comparisions between populations can obscure the affects of selection processes affecting multiple populations.  We are working on methods to model the selection processes driving changes as latent variables and to find the changes in the genomics associated with those latent variables.


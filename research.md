---
layout: page
title: Research
permalink: /research/
---

My main area of research is developing statistical and machine learning methods for the study of insects, particularly vectors of disease.  Through their saliva, mosquitoes such as *[Anopheles gambiae](https://en.wikipedia.org/wiki/Anopheles_gambiae)* and other flies transmit the parasites that causes diseases such as [malaria](https://en.wikipedia.org/wiki/Malaria). Disease such as malaria, [dengue fever](https://en.wikipedia.org/wiki/Dengue_fever), and [Zika](https://en.wikipedia.org/wiki/Zika_fever) are major threats to public health; by studying these insects, we aim to understand mechanisms of immunity and insecticide resistance to aid in the fight to eradicate these diseases.  My primary focus is population genetics, or the study of how the genomes of these insects evolve and change in response to changing environmental conditions.

Adjusted Likelihood-Ratio Test
==============================
Single-SNP association tests are a popular and powerful statistical technique for identifying genomic variants that are associated with in population structure.  Recently, I proposed an adjusted likelihood-ratio test that handles unknown variants by using an uninformative (uniform) prior over all possible genotypes.  I demonstrated that this approach significantly reduces false positives when compared with more commonly-used techniques such as $$F_{ST}$$ and can uncover variation missed by other methods.

* RJ Nowling, SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](/publications/BICOB_2018.pdf). Proceedings of the 10th International Conference on Bioinformatics and Computational Biology (BICOB 2018), March 2018.
* RJ Nowling, SJ Emrich. [Stable Feature Ranking with Logistic Regression Ensembles](/publications/BIBM_2017.pdf). Proceedings of the 2017 IEEE International Conference on Bioinformatics and Biomedicine (IEEE BIBM 2017), October 2017.

Identifying Processes Driving Differences between Populations
=============================================================
Most population genetics studies sort samples into populations and then perform association tests between the variants and population labels.  This approach has a few downsides. For many insect species, populations tend to overlap due to intermating such that defining discrete memberships can be difficult.  In addition, pairwise comparisions between populations can obscure the affects of selection processes affecting multiple populations.  We are working on methods to model the selection processes driving changes as latent variables and to find the changes in the genomics associated with those latent variables.

* RJ Nowling and SJ Emrich. [Detecting Chromosomal Inversions from Dense SNPs by Combining PCA and Association Tests](/publications/ACMBCB_2018.pdf). Proceedings of the 9th ACM International Conference on Bioinformatics, Computational Biology and Health Informatics (ACM-BCB 2018), August 2018.
* RJ Nowling,  JL Abrudan, and SJ Emrich. [Population Genetics without the Population Labels](/publications/AGS_2018.pdf). Poster presentation at the 11th Annual Arthropod Genomics Symposium, June 2018, Urbana-Champaign, IL.

Transmembrane Receptors
=======================
Transmembrance receptors are proteins that sit in the membrane of the cell and are often activated or deactivated by the binding of a ligand to the extracellular side.  Examples of these receptors include [G Protein-coupled Receptors (GPCRs)](https://en.wikipedia.org/wiki/G_protein–coupled_receptor) which transfer signals by releasing [G Proteins](https://en.wikipedia.org/wiki/G_protein) inside the cell and [Ligand-gated ion channels](https://en.wikipedia.org/wiki/Ligand-gated_ion_channel) that act as valves for the transfer of ions like potassium and chloride into the cell. GPCRs are a common target of drugs including [selective serotonin reuptake inhibitors](https://en.wikipedia.org/wiki/Selective_serotonin_reuptake_inhibitor) (SSRIs, antidepression medication) and [beta blockers](https://en.wikipedia.org/wiki/Beta_blocker) (hypertension).

As part of a project to develop novel insecticides, I worked on developing a classifier for GPCR protein sequences to aid in identifying and annotating GPCRs in insect genomes.  I adapted several classifiers that had been trained primarily on GPCRs from humans and other model organisms using an ensemble-approach.

* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identification of novel arthropod vector G protein-coupled receptors](https://parasitesandvectors.biomedcentral.com/articles/10.1186/1756-3305-6-150). *Parasites & Vectors* 2013 6:150.
* RJ Nowling,  M Wadsworth, JL Abrudan, DA Shoue, B Abdul-Wahid, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identifying GPCRs in the Genome of the Sand Fly *P. papatasi* using Ensemble\*](/publications/AGS_2013.pdf). Poster presentation at the 7th Annual Arthropod Genomics Symposium, June 2013, Notre Dame, IN.
* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Evaluation and Development of GPCR Classifiers for Vectors](/publications/Eck_Institute_Retreat_2013.pdf). Poster presentation at the Second Annual Eck Institute for Global Health Research Retreat, January 2013, Notre Dame, IN.


---
layout: page
title: Research
permalink: /research/
---

My main area of research is developing statistical and machine learning methods for the study of insects, particularly vectors of disease.  Through their saliva, mosquitoes such as *[Anopheles gambiae](https://en.wikipedia.org/wiki/Anopheles_gambiae)* and other flies transmit the parasites that causes diseases such as [malaria](https://en.wikipedia.org/wiki/Malaria). Disease such as malaria, [dengue fever](https://en.wikipedia.org/wiki/Dengue_fever), and [Zika](https://en.wikipedia.org/wiki/Zika_fever) are major threats to public health; by studying these insects, we aim to understand mechanisms of immunity and insecticide resistance to aid in the fight to eradicate these diseases.  My primary focus is population genetics, or the study of how the genomes of these insects evolve and change in response to changing environmental conditions.

Unsupervised Methods for Population Genetics
============================================
Most population genetics studies sort samples into populations and then perform association tests between the variants and population labels.  This approach has a few downsides. For many insect species, populations tend to overlap due to intermating such that defining discrete memberships can be difficult.  In addition, pairwise comparisions between populations can obscure the effects of selection processes affecting multiple populations.  We are working on unsupervised methods to detect signatures of differentiation between populations.

* RJ Nowling and SJ Emrich. [Detecting Chromosomal Inversions from Dense SNPs by Combining PCA and Association Tests](/publications/ACMBCB_2018.pdf). *Proceedings of the 9th ACM International Conference on Bioinformatics, Computational Biology and Health Informatics* (ACM-BCB 2018), August 2018. [Slides from the Talk](/publications/RNowling_ACMBCB2018_slides.pdf)
* RJ Nowling,  JL Abrudan, and SJ Emrich. [Population Genetics without the Population Labels](/publications/AGS_2018.pdf). Poster presentation at the 11th Annual Arthropod Genomics Symposium, June 2018, Urbana-Champaign, IL.

Adjusted Likelihood-Ratio Test
==============================
Single-SNP association tests are a popular and powerful statistical technique for identifying genomic variants that are associated with in population structure.  Recently, I proposed an adjusted likelihood-ratio test that handles unknown variants by using an uninformative (uniform) prior over all possible genotypes.  I demonstrated that this approach significantly reduces false positives when compared with more commonly-used techniques such as $$F_{ST}$$ and can uncover variation missed by other methods.

* RJ Nowling, SJ Emrich. Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes. *Journal of Bioinformatics and Computational Biology* (JBCB), 16(5) 2018. **Invited Paper**
* RJ Nowling, SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](/publications/BICOB_2018.pdf). *Proceedings of the 10th International Conference on Bioinformatics and Computational Biology* (BICOB 2018), March 2018.
* RJ Nowling, SJ Emrich. [Stable Feature Ranking with Logistic Regression Ensembles](/publications/BIBM_2017.pdf). *Proceedings of the 2017 IEEE International Conference on Bioinformatics and Biomedicine* (IEEE BIBM 2017), October 2017.

Transmembrane Receptors
=======================
Transmembrance receptors are proteins that sit in the membrane of the cell and are often activated or deactivated by the binding of a ligand to the extracellular side.  Examples of these receptors include [G Protein-coupled Receptors (GPCRs)](https://en.wikipedia.org/wiki/G_protein–coupled_receptor) which transfer signals by releasing [G Proteins](https://en.wikipedia.org/wiki/G_protein) inside the cell and [Ligand-gated ion channels](https://en.wikipedia.org/wiki/Ligand-gated_ion_channel) that act as valves for the transfer of ions like potassium and chloride into the cell. GPCRs are a common target of drugs including [selective serotonin reuptake inhibitors](https://en.wikipedia.org/wiki/Selective_serotonin_reuptake_inhibitor) (SSRIs, antidepression medication) and [beta blockers](https://en.wikipedia.org/wiki/Beta_blocker) (hypertension).

As part of a project to develop novel insecticides, I worked on developing a classifier for GPCR protein sequences to aid in identifying and annotating GPCRs in insect genomes.  I adapted several classifiers that had been trained primarily on GPCRs from humans and other model organisms using an ensemble-approach.

* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identification of novel arthropod vector G protein-coupled receptors](https://parasitesandvectors.biomedcentral.com/articles/10.1186/1756-3305-6-150). *Parasites & Vectors* 2013 6:150.
* RJ Nowling,  M Wadsworth, JL Abrudan, DA Shoue, B Abdul-Wahid, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identifying GPCRs in the Genome of the Sand Fly *P. papatasi* using Ensemble\*](/publications/AGS_2013.pdf). Poster presentation at the 7th Annual Arthropod Genomics Symposium, June 2013, Notre Dame, IN.
* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Evaluation and Development of GPCR Classifiers for Vectors](/publications/Eck_Institute_Retreat_2013.pdf). Poster presentation at the Second Annual Eck Institute for Global Health Research Retreat, January 2013, Notre Dame, IN.

Biomedical Image Segmentation
=============================
Outside of my work in bioinformatics, I recently started collaborating with researchers at the [Medical College of Wisconsin](https://www.mcw.edu/) on applications of deep learning to biomedical image segmentation.  [U-Net](https://arxiv.org/abs/1505.04597) is a relatively new architecture that combines convolutional neural networks and variational autoencoders to perform segmentation.   We are currently evaluating the efficacy of the U-Net model on a range of segmentation tasks for Magnetic Resonance Imaging (MRI) data.

* RJ Nowling, J Bukowy, SD McGarry, AS Nencka, O Blasko, J Urbain, A Lowman, A Barrington, A Banerjee, KA Iczkowski, and Peter S. LaViolette. Classification Before Segmentation: Improved U-Net Prostate Segmentation." *Proceedings of the 2019 IEEE-EMBS International Conference on Biomedical and Health Informatics* (IEEE BHI 2019). Accepted for oral presentation.
* RJ Nowling, J Bukowy, SD McGarry, AS Nencka, J Urbain, A Lowman, M Hohenwalter, A Banjeree, K Iczkowski, and PS LaViolette. [Cascading Classifiers Improve Prostate Segmentation](/publications/ISMRM_2019_abstract.pdf). Poster presentation at the International Society for Magnetic Resonance in Medicine 27th Annual Meeting, May 2019, Montreal, Canada. 

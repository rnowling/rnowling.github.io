---
layout: page
title: Research
permalink: /research/
---

My main area of research is developing statistical and machine learning methods for the study of insects, particularly vectors of disease.  Through their saliva, mosquitoes such as *[Anopheles gambiae](https://en.wikipedia.org/wiki/Anopheles_gambiae)* and other flies transmit the parasites that causes diseases such as [malaria](https://en.wikipedia.org/wiki/Malaria). Disease such as malaria, [dengue fever](https://en.wikipedia.org/wiki/Dengue_fever), and [Zika](https://en.wikipedia.org/wiki/Zika_fever) are major threats to public health; by studying these insects, we aim to understand mechanisms of immunity and insecticide resistance to aid in the fight to eradicate these diseases.  My primary focus is population genetics, or the study of how the genomes of these insects evolve and change in response to changing environmental conditions.

Detecting Chromosomal Inversions
================================
Chromosomal inversions an important role in ecological adaptation by enabling the accumulation of beneficial alleles (Love, et. al. 2016; Fuller, et al. 2017) and reproductive isolation (Noor, et a. 2001). The 2La inversion in the Anopheles gambiae complex has been associated with thermal tolerance of larvae (Rocca, et al. 2009), enhanced desiccation resistance in adult mosquitoes (Gray, et al. 2009), and susceptibility to malaria (Riehle, et al. 2017). Additionally, inversions must be identified and accounted for to avoid bias in population inference and association testing (Seich al Basatena, et al. 2013).

We are working on methods for detecting inversions from population genomics data.  Methods commonly used in humans rely on aligning reads to a reference genome.  Such methods, even when using long reads, are not as effective in insect genomes.  Such genomes are often full of problematic repetitive elements and their assemblies may be fragmented.  We have found that PCA-based methods can be quite effective for insects.  Our eventual goal is to be able to detect inversions even when the reference genome is highly fragmented.

* M Aleck and RJ Nowling [Exploring Mechanisms of Molecular Evolution and Their Representations in PCA](/publications/IEEE_COMPSAC_2019.pdf). Poster presentation at the IEEE 43rd Annual Computer Software and Applications Conference (COMPSAC), July 2019, Milwaukee, WI.
* K Manke, SJ Emrich, and RJ Nowling. [Detecting and Localizing Inversions from SNPs](/publications/AGS_2019.pdf).  Poster presentation at the 12th Annual Arthropod Genomics Symposium, June 2019, Manhattan, KS.
* RJ Nowling and SJ Emrich. [Detecting Chromosomal Inversions from Dense SNPs by Combining PCA and Association Tests](https://dl.acm.org/citation.cfm?id=3233571). *Proceedings of the 9th ACM International Conference on Bioinformatics, Computational Biology and Health Informatics* (ACM-BCB 2018), August 2018. [Slides from the Talk](/publications/RNowling_ACMBCB2018_slides.pdf) [Paper](/publications/ACMBCB_2018.pdf)
* RJ Nowling,  JL Abrudan, and SJ Emrich. [Population Genetics without the Population Labels](/publications/AGS_2018.pdf). Poster presentation at the 11th Annual Arthropod Genomics Symposium, June 2018, Urbana-Champaign, IL.

Likelihood-Ratio Tests Adjusted for Missing Data
================================================
Single-SNP association tests are a popular and powerful statistical technique for identifying genomic variants that are associated with in population structure.  Recently, I proposed an adjusted likelihood-ratio test that handles unknown variants by using an uninformative (uniform) prior over all possible genotypes.  I demonstrated that this approach significantly reduces false positives when compared with more commonly-used techniques such as $$F_{ST}$$ and can uncover variation missed by other methods.

* RJ Nowling and SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](https://www.worldscientific.com/doi/10.1142/S0219720018400206). *Journal of Bioinformatics and Computational Biology* (JBCB), 16(5) 2018. **Invited Paper**
* RJ Nowling and SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](/publications/BICOB_2018.pdf). *Proceedings of the 10th International Conference on Bioinformatics and Computational Biology* (BICOB 2018), March 2018.
* RJ Nowling and SJ Emrich. [Stable Feature Ranking with Logistic Regression Ensembles](/publications/BIBM_2017.pdf). *Proceedings of the 2017 IEEE International Conference on Bioinformatics and Biomedicine* (IEEE BIBM 2017), October 2017.

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

* RJ Nowling, J Bukowy, SD McGarry, AS Nencka, O Blasko, J Urbain, A Lowman, A Barrington, A Banerjee, KA Iczkowski, and PS LaViolette. Classification Before Segmentation: Improved U-Net Prostate Segmentation." *Proceedings of the 2019 IEEE-EMBS International Conference on Biomedical and Health Informatics* (IEEE BHI 2019). Accepted for oral presentation.
* RJ Nowling, J Bukowy, SD McGarry, AS Nencka, J Urbain, A Lowman, M Hohenwalter, A Banjeree, K Iczkowski, and PS LaViolette. [Cascading Classifiers Improve Prostate Segmentation](/publications/ISMRM_2019_abstract.pdf). Poster presentation at the International Society for Magnetic Resonance in Medicine 27th Annual Meeting, May 2019, Montreal, Canada. 

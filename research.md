---
layout: page
title: Bio Research
permalink: /research/
---

Our research lies at the intersection of machine learning and genomics.  A genome is the totality of an organism's DNA and effectively the "instructions" or software that control the development of an organism.  One of the central goals of biology is to link changes in physical characteristics (phenotypes) of organisms to causative changes in the DNA.  Genomic data, much like compiled software, are not easy for humans to interpret directly.  Just as a decompiler can help us reverse engineer compiled software, machine learning can be used to identify, characterize, and annotate features of genomes and make analysis by humans easier.  We aim to aid biologists in uncovering new knowledge and insights into the function of genomes by developing new methods that draw on machine learning, data science, and big data techniques.

We focus primarily on insect genomes.   Insect genomes present unique and interesting challenges for computational biologists.  More importantly, many insects vector diseases or are important to food security (either as pollinators or pests).  Through their saliva, mosquitoes such as *[Anopheles gambiae](https://en.wikipedia.org/wiki/Anopheles_gambiae)* and *[Aedes aegypti](https://en.wikipedia.org/wiki/Aedes_aegypti)* transmit the parasites and viruses that cause diseases such as [malaria](https://en.wikipedia.org/wiki/Malaria), [dengue fever](https://en.wikipedia.org/wiki/Dengue_fever), and [Zika](https://en.wikipedia.org/wiki/Zika_fever) are major threats to public health.  By partnering with biologists who study these insects, we can ensure that my work solves relevant problems and useful.

This page contains a selected list of publications.  For a full list, please see my
[Google Scholar profile page](https://scholar.google.com/citations?user=a80X8MUAAAAJ&hl=en).

## Dynamics of Small, Biologically-Important Peptides
Recently, we've begun working on characterizing the dynamics of small, biologically-important peptides using molecular dynamics
simulations.  Small peptides can be involved in a variety of processes such as signaling (peptide hormones) by binding to
receptors, breaking down toxins like pesticides, and disabling biological toxins like neurotoxin proteins. Our work takes advantage
of MSOE's [Rosie GPU cluster](https://msoe.dev/#/), a relatively unique resource for a small university.

**Papers:**

* RJ Nowling. Microsecond Molecular Dynamics Simulations of an Alpha-Bungarotoxin Peptide Ligand. *Proceedings of the 2025 IEEE International Conference on Electro/Information Technology (IEEE EIT 2025)*. 2025.

## Sequencing for Regulatory Genomics
In addition to genes, genomes contain regulatory elements that are involved in the mechanical process of gene expression.  Regulatory elements can be divided into [*trans*](https://en.wikipedia.org/wiki/Trans-regulatory_element)- and [*cis*](https://en.wikipedia.org/wiki/Cis-regulatory_element)-acting categories.  So-called DNA enrichment assays such as ChIP-Seq, ATAC-Seq, STARR-Seq, and FAIRE-Seq allow us to identify and characterize the activity of *cis*-regulatory elements such as promoters and enhancers.

We collaborate with biologists on projects to process and analyze DNA
enrichment assay data to annotate and characterize *cis*-regulatory elements in particular organisms.

**Papers:**

* RJ Nowling, K Njoya, JG Peters, MM Riehle. [Prediction accuracy of regulatory elements from sequence varies by functional sequencing technique](https://www.frontiersin.org/articles/10.3389/fcimb.2023.1182567/full). *Frontiers in Cellular and Infection Microbiology.* 2023.
* I Holm, L Nardini, A Pain, E Bischoff, CE Anderson, S Zongo, WM Guelbeogo, N Sagnon, DM Gohl, RJ Nowling, KD Vernick, and MM Riehle. [Comprehensive Genomic Discovery of Non-Coding Transcriptional Enhancers in the African Malaria Vector Anopheles coluzzii](https://www.frontiersin.org/articles/10.3389/fgene.2021.785934/full). *Front. Genet.* 2022
* RJ Nowling, SK Behura, MS Halfon, SJ Emrich, and M Duman-Scheel. [PeakMatcher facilitates updated Aedes aegypti embryonic cis-regulatory element map](https://hereditasjournal.biomedcentral.com/articles/10.1186/s41065-021-00172-2). *Hereditas* 158, Article number: 7 (2021). **Arthropod Genomics thematic series**

**Posters:**
* CR Beal, JG Peters, and RJ Nowling. Sequence Model Evaluation Framework for STARR-Seq Peak Calling. Poster presentation at the 12th Annual ACM International Conference on Bioinformatics, Computational Biology, and Health Informatics, August 2021, Virtual.  [Abstract](/publications/acm_bcb_2021_abstract.pdf). [Poster](/publications/ACMBCB_2021.pdf)
* RJ Nowling, RR Geromel, and BS Halligan. Filtering STARR-Seq Peaks for Enhancers with Sequence Models. Poster presentation at the 11th Annual ACM International Conference on Bioinformatics, Computational Biology, and Health Informatics, September 2020, Virtual.  [Abstract](/publications/acm_bcb_2020_enhancer_ssl_abstract.pdf) [Poster](/publications/ACMBCB_2020_enhancer_ssl.pdf)
* RJ Nowling, CR Beal, SJ Emrich, SK Behura, MS Halfon, and M Duman-Scheel. PeakMatcher: Matching Peaks Across Genome Assemblies. Poster presentation at the 11th Annual ACM International Conference on Bioinformatics, Computational Biology, and Health Informatics, September 2020, Virtual.  [GitHub](https://github.com/rnowling/peak-matcher) [Abstract](/publications/acm_bcb_2020_peak_matcher_abstract.pdf) [Poster](/publications/ACMBCB_2020_peakmatcher.pdf)

## Detecting Chromosomal Inversions
Chromosomal inversions an important role in ecological adaptation by enabling the accumulation of beneficial alleles (Love, et. al. 2016; Fuller, et al. 2017) and reproductive isolation (Noor, et a. 2001). The 2La inversion in the *Anopheles gambiae* complex has been associated with thermal tolerance of larvae (Rocca, et al. 2009), enhanced desiccation resistance in adult mosquitoes (Gray, et al. 2009), and susceptibility to malaria (Riehle, et al. 2017). Additionally, inversions must be identified and accounted for to avoid bias in population inference and association testing (Seich al Basatena, et al. 2013).

We are working on methods for detecting inversions from population genomics data.  Methods commonly used in humans rely on aligning reads to a reference genome.  Such methods, even when using long reads, are not as effective in insect genomes.  Such genomes are often full of problematic repetitive elements and their assemblies may be fragmented.  We have found that PCA-based methods can be quite effective for insects.  Our eventual goal is to be able to detect inversions even when the reference genome is highly fragmented.  My lab maintains and develops the open-source software package [Asaph](https://github.com/rnowling/asaph) for SNP inversion detection methods.

**Papers:**

* RJ Nowling, SH Keyser, AR Moran, JG Peters, and D Leskiewicz. [Segmenting and Genotyping Large, Polymorphic Inversions](https://ieeexplore.ieee.org/abstract/document/10187331). *Proceedings of the 2023 IEEE International Conference on Electro/Information Technology (IEEE EIT 2023)*. 2023.
* RJ Nowling, F Fallas-Moya, A Sadovnik, SJ Emrich, M Aleck, D Leskiewicz, and JG Peters.  [Fast, low-memory detection and localization of large, polymorphic inversions from SNPs](https://peerj.com/articles/12831/). *PeerJ*. 2022.
* F Fallas-Moya, RJ Nowling, SJ Emrich, and A Sadovnik. [Automated Detection and Localization of Genome Inversions using Principal Component Analysis"](https://ieeexplore.ieee.org/document/9612782). *IEEE 3rd International Conference on BioInspired Processing (BIP)*. 2021.
* RJ Nowling, KR Manke, and SJ Emrich. [Detecting Inversions with PCA in the Presence of Population Structure](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0240429). *PLoS One*. 2020.
* RJ Nowling and SJ Emrich. [Detecting Chromosomal Inversions from Dense SNPs by Combining PCA and Association Tests](https://dl.acm.org/citation.cfm?id=3233571). *Proceedings of the 9th ACM International Conference on Bioinformatics, Computational Biology and Health Informatics* (ACM-BCB 2018), August 2018. [Slides from the Talk](/publications/RNowling_ACMBCB2018_slides.pdf) [Paper](/publications/ACMBCB_2018.pdf)

**Posters:**

* M Aleck and RJ Nowling. Evaluation of Clustering Algorithms for Inferring Inversion Genotypes. Poster presentation at the 14th Great Lakes Bioinformatics conference (GLBIO), May 2021, Virtual. [Abstract](/publications/glbio_2021_abstract.pdf) [Poster](/publications/glbio_2021_poster.pdf)
* M Aleck and RJ Nowling. [Exploring Mechanisms of Molecular Evolution and Their Representations in PCA](/publications/IEEE_COMPSAC_2019.pdf). Poster presentation at the IEEE 43rd Annual Computer Software and Applications Conference (COMPSAC), July 2019, Milwaukee, WI.
* KR Manke, SJ Emrich, and RJ Nowling. [Detecting and Localizing Inversions from SNPs](/publications/AGS_2019.pdf).  Poster presentation at the 12th Annual Arthropod Genomics Symposium, June 2019, Manhattan, KS.
* RJ Nowling, JL Abrudan, and SJ Emrich. [Population Genetics without the Population Labels](/publications/AGS_2018.pdf). Poster presentation at the 11th Annual Arthropod Genomics Symposium, June 2018, Urbana-Champaign, IL.

## Likelihood-Ratio Tests Adjusted for Missing Data
Single-SNP association tests are a popular and powerful statistical technique for identifying genomic variants that are associated with in population structure.  Recently, I proposed an adjusted likelihood-ratio test that handles unknown variants by using an uninformative (uniform) prior over all possible genotypes.  I demonstrated that this approach significantly reduces false positives when compared with more commonly-used techniques such as $$F_{ST}$$ and can uncover variation missed by other methods.

**Papers:**
* RJ Nowling and SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](https://www.worldscientific.com/doi/10.1142/S0219720018400206). *Journal of Bioinformatics and Computational Biology* (JBCB), 16(5) 2018. **Invited Paper**
* RJ Nowling and SJ Emrich. [Adjusted Likelihood-Ratio Test for Variants with Unknown Genotypes](/publications/BICOB_2018.pdf). *Proceedings of the 10th International Conference on Bioinformatics and Computational Biology* (BICOB 2018), March 2018.
* RJ Nowling and SJ Emrich. [Stable Feature Ranking with Logistic Regression Ensembles](/publications/BIBM_2017.pdf). *Proceedings of the 2017 IEEE International Conference on Bioinformatics and Biomedicine* (IEEE BIBM 2017), October 2017.

## Transmembrane Receptors
Transmembrance receptors are proteins that sit in the membrane of the cell and are often activated or deactivated by the binding of a ligand to the extracellular side.  Examples of these receptors include [G Protein-coupled Receptors (GPCRs)](https://en.wikipedia.org/wiki/G_protein–coupled_receptor) which transfer signals by releasing [G Proteins](https://en.wikipedia.org/wiki/G_protein) inside the cell and [Ligand-gated ion channels](https://en.wikipedia.org/wiki/Ligand-gated_ion_channel) that act as valves for the transfer of ions like potassium and chloride into the cell. GPCRs are a common target of drugs including [selective serotonin reuptake inhibitors](https://en.wikipedia.org/wiki/Selective_serotonin_reuptake_inhibitor) (SSRIs, antidepression medication) and [beta blockers](https://en.wikipedia.org/wiki/Beta_blocker) (hypertension).

As part of a project to develop novel insecticides, I worked on developing a classifier for GPCR protein sequences to aid in identifying and annotating GPCRs in insect genomes.  I adapted several classifiers that had been trained primarily on GPCRs from humans and other model organisms using an ensemble-approach.

**Papers:**
* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identification of novel arthropod vector G protein-coupled receptors](https://parasitesandvectors.biomedcentral.com/articles/10.1186/1756-3305-6-150). *Parasites & Vectors* 2013 6:150.

**Posters:**
* RJ Nowling,  M Wadsworth, JL Abrudan, DA Shoue, B Abdul-Wahid, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Identifying GPCRs in the Genome of the Sand Fly *P. papatasi* using Ensemble\*](/publications/AGS_2013.pdf). Poster presentation at the 7th Annual Arthropod Genomics Symposium, June 2013, Notre Dame, IN.
* RJ Nowling, JL Abrudan, DA Shoue, B Abdul-Wahid, M Wadsworth, G Stayback, FH Collins, MA McDowell, and JA Izaguirre. [Evaluation and Development of GPCR Classifiers for Vectors](/publications/Eck_Institute_Retreat_2013.pdf). Poster presentation at the Second Annual Eck Institute for Global Health Research Retreat, January 2013, Notre Dame, IN.

## Software

Our lab develops software tools for use in developing and distributing our methods.  Our goals including releasing all of our software under a liberal open-source license such as the [Apache License, v2.0](https://www.apache.org/licenses/LICENSE-2.0), easy to use, and accompanied by documentation.

### Asaph
Asaph is a software package for detecting, localizing, and genotyping large polymorphic inversions from SNP data.

GitHub: [https://github.com/nowling-lab/asaph](https://github.com/nowling-lab/asaph)


### PeakMatcher
PeakMatcher is a simple tool we developed to match peaks from DNA enrichment assays across genome assemblies to aid in validation.  As genome assemblies are improved through new sequencing methods, DNA enrichment assay data need to be re-analyzed using the new assemblies.  PeakMatcher provides scripts for comparing called peaks on the same genome (e.g., to validate pipeline parameters or compare agreement across different assays) or different genomes using the raw reads (e.g., to validate that peaks are not lost with the new genome assembly or identify new peaks for analysis).

GitHub: [https://github.com/nowling/peak-matcher](https://github.com/rnowling-lab/peak-matcher)

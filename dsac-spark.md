---
layout: page
title: "DSAC SPARK"
permalink: /dsac-srp/
published: true
hide_title: true
---

# DSAC Summer Program for Advancing Research Knowledge (SPARK)

The Diercks School of Advanced Computing is proud to announce a paid opportunity for students to
collaborate faculty on research projects over the summer.

## Program Features
Over 8 weeks, participants will engage in a faculty-directed research project.
Participants will be taught to conceptualize research questions, design, execute, and
interpret the results of experiments, and communicate their findings.
Along the way, students will develop expertise with AI technologies.

Participants will receive one-on-one mentoring from a DSAC faculty member.
Participants will be paid a $5,000 stipend.

## Eligibility and Requirements
To participate in the program:

* Participants must be a current student in a DSAC program.
* Participants must be available full-time for 8 weeks over the summer.  The specific weeks can
  be negotiated with the student's advisor.
* Participants can work partially or entirely remotely with the advisor's approval, however, they must
  reside and perform all work in the state of Wisconsin during the program.
* If needed, financial help with on-campus housing will be considered on a case-by-case basis.
* The number of chosen participants will be dependent upon faculty capacity and funding.

## Application and Selection Process
The application and selection process will proceed as follows:

1. Review the faculty projects below.
1. Fill out the [application form](https://forms.cloud.microsoft/r/FjaLSNCysD) by end of day, **Tuesday, March 31, 2026**.
1. A committee of faculty members will review applications and if needed, reach out with further questions.
1. Selected participants will be matched with a faculty member based on indicated project interest and faculty capacity.
1. Applicants will be notified of their admission decision.

## Available Projects

### Advancing Cancer Histopathology with Mid-Infrared Spectroscopic Imaging
#### Dr. Berisha

Cancer diagnosis traditionally relies on biopsy, chemical staining, and visual inspection by a trained pathologist. While this process is clinically effective, it is time-consuming, qualitative, and sensitive to variability in staining quality and human interpretation. Emerging imaging technologies offer the potential to transform this workflow by enabling rapid, quantitative, and label-free tissue characterization. One promising technique is hyperspectral photothermal mid-infrared spectroscopic imaging (HP-MIRSI), which captures molecular signatures from biological tissues without the need for dyes or stains.

HP-MIRSI is enabled by optical photothermal infrared (O-PTIR) imaging technology, which works by illuminating tissue with a mid-infrared laser that excites molecular vibrations associated with biochemical components such as proteins, lipids, and nucleic acids. A visible probe laser then detects the resulting photothermal response, enabling the measurement of infrared absorption with spatial resolution significantly higher than traditional infrared microscopy. The resulting hyperspectral datasets contain rich biochemical information that can be analyzed computationally to identify and distinguish tissue structures.

In our previous work, HP-MIRSI data from cervical cancer tissue samples were analyzed to distinguish between two primary tissue subtypes: epithelium and stroma. The results are supported by a large and statistically robust dataset consisting of tissue samples from 98 cervical cancer patients and more than 40 million hyperspectral data points.

The proposed project will extend this work from a binary classification problem to a multiclass tissue classification framework. In addition to epithelium and stroma, we aim to incorporate additional clinically relevant tissue categories such as necrosis, blood, and lymphocytes. Beyond cervical cancer, this project will also explore the application of hyperspectral mid-infrared imaging to other gynecological cancers, including ovarian cancer. Hyperspectral imaging datasets for ovarian cancer tissues have already been collected and will be incorporated into the analysis.

In this project, students will visualize, analyze, and characterize hyperspectral imaging datasets from clinical cancer tissues. They will apply data science and machine learning techniques to extract meaningful spectral features, develop classification models for multiple tissue types, and evaluate model performance using large biomedical datasets. Along the way, students will gain exposure to biomedical imaging, spectroscopy, and quantitative approaches to modern histopathology.

This work is conducted in collaboration with Dr. Reddy’s research laboratory at the University of Houston, which specializes in biomedical imaging and spectroscopic analysis of cancer tissues.
All hyperspectral imaging data used in this project will be provided by the same group, offering access to high-quality clinical datasets and providing students with the opportunity to work with real-world biomedical research data.

**Techniques:** hyperspectral imaging, machine/deep learning, data science, statistical analysis, image classification, dimensionality reduction, visualization.

### AI-Driven Detection of Cardiovascular Conditions from ECG Signals
#### Dr. Berisha
Cardiovascular disease remains among the leading causes of death worldwide. Early detection is critical, yet many diagnostic tools require specialized testing or are only used after symptoms appear. Electrocardiograms (ECGs) are widely available, inexpensive, and non-invasive, making them an ideal data source for developing scalable screening tools. However, many subtle patterns in ECG signals associated with disease risk are difficult for clinicians to detect using traditional analysis methods.

This project focuses on the development and application of machine/deep learning methods to analyze 12-lead ECG signals for the early detection of cardiovascular diseases. By leveraging large collections of clinical ECG data, AI models can learn complex patterns in cardiac electrical activity that may indicate elevated risk for conditions such as stroke, arrhythmias, and other cardiovascular abnormalities. These models aim to support clinicians by providing automated tools that can assist in screening, diagnosis, and risk stratification.

Students working on this project will explore how ECG signals are collected and processed, apply data science techniques to large biomedical time-series datasets, and develop machine/deep learning models capable of identifying clinically relevant patterns. The work also includes validating model performance across datasets collected from multiple clinical sites to ensure reliability and generalizability across diverse patient populations.

In addition to cardiovascular disease detection, prior research in this collaboration has explored the use of ECG-based machine learning models for identifying other medical conditions, including respiratory infections such as COVID-19 and influenza, pulmonary embolism, and congestive heart failure.

The long-term goal of this research is to develop scalable diagnostic technologies that can be deployed not only in hospitals and outpatient clinics, but also through wearable or mobile monitoring devices. Such systems could enable continuous health monitoring and earlier detection of life-threatening cardiovascular events.

This work is conducted in collaboration with Dr. Cohoon at the Medical College of Wisconsin and Froedert, and all datasets used in the project will be provided by the Clinical and Translational Institute of Southeast Wisconsin.

**Techniques:** machine/deep learning, biomedical signal processing, time-series analysis, statistical modeling, data pipelines, clinical data analysis

### Modeling Partially-Observable Environments with Bayesian Reasoning
#### Dr. Kedziora

In real world situations, managing partial observability is core to the success of the agent to formulate an optimal policy, and closely related to multi-agent decentralized execution settings.  Despite this, there is no consensus on the best way to model partial observability.  Some efforts have centered on leveraging the hidden state of a recurrent deep learning model to track agent beliefs.  Also, complicated information protocols have been developed to manage communication in multi-agent settings.  

Game theory has well-developed mathematical structure--based on Bayesian reasoning--for modeling partially observable environments.  This project will explore the use of Bayes rule to model agent beliefs.  It will aim to embed the use of Bayes rule within a larger algorithmic structure to empower agent learning.

**Techniques:** Reinforcement learning, Bayes Rule, the Harsanyi Doctrine, Deep learning

### Do Large Language Models (LLMs) encode world models?
#### Dr. Kedziora

Do Large Language Models (LLMs) encode world models?  A world model is often understood to be a set of statistical and/or causal relationships that link states of the world across time.  If LLMs naturally come to encode world models through their training process then they may be well set up to assess the consequences of different choices and so good vehicles for artificial general intelligence.  If not, then an alternative paradigm may be needed.  

This project will aim to develop a theoretical characterization of the conditions under which an LLM learns a world model through formalizing the relationship between next-token prediction and next-state prediction.  Initial work will leverage the reinforcement learning transfer learning literature to determine requirements for functional inter-task mappings.  Goals will also include formulating of benchmark tests to probe LLMs for world models.

**Techniques:** Transfer learning, stochastic processes, self-supervision

### Exploring the Dynamics of Anti-Venom Molecules
#### Dr. Nowling

When bitten by a snake or other venomous animal, a patient doesn't have much time to receive a dose of a life-saving anti-venom.
Yet, the current processes for producing anti-venoms are laborious and costly, leading to a limited supply.
We desperately need synthetic anti-venoms that can be produced easily and at low cost.
Before we can do that, however, we need to understand the mechanics of how anti-venoms work.
Molecular dynamics is a simulation technique for producing high-fidelity simulations of molecular motions at the atomic scale --
allowing us to see how molecules move in greater detail than experimental techniques.
MD simulations of an anti-venom for the $\alpha$-Bungarotoxin neurotoxin were generated and are now in need of analysis.
In this project, students will visualize, analyze, and characterize the 3D structures and dynamics of an anti-venom protein
using data science and mathematical modeling techniques while learning some biochemistry along the way.
This project is a great fit for you if you enjoy data science and applying it to new, interesting problems.

**Techniques:** data science, visualizations, statistics, time-series analysis, clustering, stochastic processes

### Inferring User Reasoning for Generalization
#### Dr. Yoder

Why do users provide the feedback they provide? In many settings, users provide very short forms of feedback: A thumbs-up or a thumbs-down. A swap for this instead of that. How can we learn from this feedback?

How can we derive generalizable knowledge from this feedback?
One technique is to instruct an LLM to construct an argument explaining the user’s feedback and put it into a database of explanations. When the machine needs to make a decision in the future, it can consult the related user arguments and use them to guide its reasoning on the new tasks.

In this project, you will build a system to learn from user input in this way and evaluate it in comparison to simpler techniques.

**Techniques:** LLMs, RAG, AI Evals

### Does Generalization Come from Random Initialization?
#### Dr. Yoder
How do neural networks that have the capacity to simply memorize the inputs instead find general structural patterns that enable them to make predictions on unseen data?

There are a couple of suggestions in the literature that point to the possibility that the general structures are already there, hidden in the random weights with which a network is initialized.

The lottery ticket hypothesis suggests that training with 10x more parameters than you need can improve performance because there are many “subnetworks” within a larger network, and these are more likely to randomly initialize to match your problem than just starting with a network 10x smaller in the first place.

The grokking phenomenon where networks initially perfectly memorize the training data with zero test performance and only much later learn to generalize to making predictions on the test set suggests that multiple layers need to interact to model generalizing phenomena while single layers can quickly imprint the training data.

But are the generalizing networks truly found, very faintly, in the initial weights of a network?

In this research project, you will explore this possibility by training networks on toy problems, tracing generalizing network parameters backwards through a network to the initial random weights. You will use statistical methods to test the hypothesis that the
weights were there in the initial weights. You will model graphs based on subspaces of each embedding used by your network.

**Techniques:** neural networks, toy problems, statistics, linear algebra

## Contacting Us
If you have questions, please reach out to Dr. Nowling at nowling@msoe.edu .

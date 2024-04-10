---
title: NGS Applications - Various Sequencing Types
date: 2024-04-10
description: "WGS, WES, RNA-seq, ChIP-seq, methyl-seq... new technologies are still being developed."
tags:
  - genomics
  - jargons
---

Whole genome sequencing (WGS)
----
  - Sequencing the entire genome (~3.1 billion bp) - what took the Human Genome Project 10 years and 3 billion dollars to achieve, now can be done overnight for a thousand bucks.
  
Whole exome sequencing (WES)
----
  - Sequencing all exons - all protein coding genes. Since exons are only ~2% of the genome (~60 million bp), it's a lot cheaper and faster. 
  
RNA-seq
----
- To find out which genes are turned on for cells. (as not all exons, or genes are turned on for a certain cell)
- Transcribed mRNA has a string of AAAAAs attached to it. The polyA tail can be captured by a string of Ts.
- The captured mature RNA can be reverse transcribed into <a name="index32"></a>complementary DNA (cDNA) by reverse transcriptase. The DNA is then sequenced.

ChIP-seq
----
- To find the DNA regions where transcription factor proteins bind to, which gives information of which genes are turned on/off.
- First let proteins bind to DNA. Fragment the DNA, then pull the proteins with antibody pulldown, with DNA fragments stuck to the proteins. Remove the protein, then sequence DNA.

Bisulfate sequencing (methyl-seq)
----
- To find places of DNA methylation.
- The methyl -CH3 group attaches to C in CpG islands. The methylation can be inherited by next generation daughter cells.
- Take two DNA samples. Treat one sample with bisulfate conversion, which turns all the Cs to Us, except the Cs that are methylated. 
- Sequence both sample and compare. The converted sample needs a special aligner since most Cs are turned to Us, and it won't align well with the normal reference.

GWAS
----
Though not a sequencing technique, GWAS is an important application of NGS.

It searches for association between genetic variants (genotypes) and traits (phenotypes) by comparing genomes in large populations.

Diseases are a common category for GWAS studies. GWAS can provide insights into the genetic basis of diseases and information about drug targets and therapeutic/treatment strategies.

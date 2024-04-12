---
title: Introduction to Genomics
date: 2023-03-06
description: "Course notes"
tags: 
  - courses
  - genomics
---

Course notes from the [Introduction to Genomics](https://www.coursera.org/learn/introduction-genomics) course from JHU, and [Applied Computational Genomics](https://github.com/quinlan-lab/applied-computational-genomics) course from Dr. Aaron Quinlan at U of Utah.

I have broken down the lengthy notes into smaller bits/ links.
The full notes can be found [here](https://gr-grey.github.io/proto1/courses/2023/03/introduction-to-genomics/)

DNA: the molecule holding our source code.
====

Our body's made of cells. It's estimated that 40 - 150 trillion cells forms the body (excluding bactria in our gut).
Different types of cells (e.g. muscle/liver/brain cells) form different organs/ parts of body, which carry out different functionalities -
the heart pumps out blood into the circulation system, the stomach process food, the brain sends all sorts of magical top down control signals...
These various departments assembled into the intricate machine that is our body.

Yet all these trillions of cells originated from a single cell - a fertilized egg. 
This cell is programmed to duplicate to produce more cells, and the cells are programmed to further differentiate to all types of cells.

There are many important questions to how the machine of the body was built and how each department carries out its functions:

- What are genes doing - what protein does a gene code? What's the protein's function?
- How are genes regulated by environments? Only 2% of genome codes for proteins, 95% of the genome is dedicated for regulation - when to turn a gene on or off (**epigenetics**).
- The regulation/ epigenetics might be the more important topic. Since it's the directions / user manual that codes for how each cell react to environmental changes. Different types of cells (brain/skin/kidney cells, etc.) have the same genome, it's the set of genes that are turned on/off that decides the function and the type of cell.
- When the cells malfunction and diseases occur, is caused by a bug in the code (gene alteration)? Or is it because the cells started to read the wrong set of directions (epigenetic, regulation of genes)?
- How are the genetic and epigenetic factors associated some of our traits, such as height, eye color, predisposition to diseases like Alzheimer?

By sequencing our DNA, we are hoping to answer questions like these by reading the source code and figuring out how our program works.

----
- [[DNA | DNA basics: AGCT, plus minus strands, 3' and 5' ends]]
- [[DNA 3D Structures | DNA structures: how the long double helix chain packs tightly with the help of proteins]]


How is DNA sequenced?
====

Sequencing by synthesis/duplication of DNA.
----
DNA can be duplicated very effectively.
When a DNA is synthesized, if we could "eavesdrop" on the order of nucleotides are being added - we would learn the sequence.

This is achieved by attaching one nucleotide at a time during synthesis,
and pause the reaction to read what nucleotide was just added, 
so when the entire DNA is synthesized, 
its whole sequence is read in the process.

- [[Polymerase Chain Reaction (PCR) | DNA duplication by PCR technique lays the foundation for sequencing]]

[[Next generation sequencing (NGS) methods]]
----
The above page discusses the smart tricks NGS uses to 
- "eavesdrop" on the synthesis process by adding a removable fluorescent cap at each step of DNA's growth
- massively parallelize the sequencing 

And discuss read length, quality scores and common errors of sequencing.

Central dogma: DNA, RNA and proteins
====

- **Central dogma** states that information flows from [[DNA]] to [[RNA]] (**transcription**), and from RNA to proteins (**translation**).
- A gene usually refers to the segments of DNA that make proteins, which is ~2% of the genome. 
- The central dogma not an absolute dogma. Information goes in the other direction, proteins coded by genes (transcription factors) can bind DNA and turn genes on/off. 
- The on/off settings can determine a cells functions and types - whether a cell is a neuron or skin cell.

- [[RNA | Basics of RNA, pre mRNA and mRNA]]
- [[Proteins | Basics of protein and codons]]

Genome and genes
====
Genome
---
  - Usually refers to the entire DNA string in nucleus. 
  - It's ~3 billion base pair long, contains sequence of a single strand DNA (positive strand, goes from 5' to 3') from 23 chromosomes.
  - Mitochondria has its own DNA (inherited from Mom); with length of ~1% of nucleus DNA.
  - Blood cells don't have a nucleus. Many liver cells are polypoid (more than 2 pairs of chromosomes).

Genes
----
A very small portion (~2%) of the 3 billion bp genome encodes protein. These sequence segments are called genes or protein coding genes.

- More on [[Gene|exons, introns, untranslated regions (UTR), and splicing]]

Intergenic DNA: junk DNA (not really)
----
  - The stretches of DNA sequences that exist between genes on a chromosome. 
  - These sequences (comprise of 95% of the genome) do not code for protein or RNA, and were once thought to be "junk DNA" (yeah, like evolution would allow such huge waste of energy to survive the cruel selection process). 
  - These non coding regions are dedicated to regulate genes - when to turn a gene on or off, like the enhancers or silencers discussed in the following session.

Stats & details of the human genome
----
- The first draft ("**build 1**") of reference genome was released in 2001. We are currently on **build 38**.
- In 2022, the first [**complete, gapless, Telomere-to-Telomere (T2T) Consortium**](https://www.science.org/doi/10.1126/science.abj6987) genome (3.055 billion bp) was released. The build 1 draft genome had many gaps (~80Mbp missing, with ~600 medically relevant genes in missing regions). After 2 decades of relentless work of filling the gaps, the genome was finally gapless (except for Y chromosome).
- Most of reference genome represents northern European male. There have been efforts to build more references with other ethnic origins. 
- Comparing the genomes from any two humans, their genomes differ on average about **one in every 700 bp** (~0.14%), which leads to ~ 4 million bp differences for any two individuals.
- Total number of genes (still under debate): one estimate is **~20,000** protein coding genes (10% more than a worm or fly), and **~200,000** coding transcripts (isoforms of a gene that encodes a distinct protein).
- Human share **98%** of DNA with chimpanzees. A more clarified statement is that when comparing human genes and chimp genes, 98% of them code for similar things - hormones, neurotransmitters, immune systems, and neither human nor chimp has genes for antlers, trunks, wings, fins, etc., similar claims are humans are 90% cats or 60% bananas.
- Humans have 2 sets of 23 chromosomes (23 pairs). 22 pairs of **autosomes** and 1 pair of sex chromosomes.
- The 22 autosomes are numbered according to their length: chromosome 1 is the longest (~250 Mb), and chromosome 21 is the shortest. Chromosome 22 was thought to be shorter than 21 at the time, when the length was determined by staining the chromosomes and look at them under microscopy, which is quite imprecise and chromosome 21 was actually slightly smaller than 22.

More on [[Genetic Mutations|genetic mutations]]: SNPs (single letter change), INDELs(insert deletion) and de novo mutations (DNM).

**Stats summary**

| Human Genome          | Stats                      |
| --------------------- | -------------------------- |
| Whole Genome          | 3.055 billion bp           |
| All Exons             | 60 Mb (2%)                 |
| Internal (1) exon     | hundreds bp (ave. 145b)    |
| Intron                | thousands bp (ave. 3.3Kb)  |
| Coding Seq (CDS)      | ave. 1.3 kb                |
| 5' UTR                | ave. 300 b                 |
| 3' UTR                | ave. 770 b                 |
| 1 gene                | ave. 27 kb                 |
| Genes                 | 20,000                     |
| Transcripts           | 200,000                    |
| SNP/INDEL             | 4 million (1/700b)         |
| De novo mutation      | ~35/gamete                 |
| GC content            | 35-60% for 100Kb, ave. 41% |
| Transposable elements | 45% of genome              |
| Mitochondria          | 30 Mb (1%)                 |

[[NGS Applications - Various Sequencing Types]] talks more aobut whole genome sequencing WGS, whole exome sequencing (WES), RNA-seq, ChIP-seq, methylseq and GWAS studies.

Gene regulations/epigenetics
====

For a gene to be "turned on", aka, making proteins it codes, a switch usually needs to be flipped on. These switches are often influenced by the subtle context of environment - drink some booze, and the genes coding the enzyme that break down alcohol will be churning on productions (up-regulated) to help you out.

Robert Sapolsky once said that genes are like recipes, that genes determine very little - it's the regulation that matters. Saying that a gene knows when to express is to say that a cake recipe knows when you're gonna make the cake.

More on [[Epigenetics|epigenetics]] 
- Transcription factors
- Regulatory regions: promoters, activators, enhancers, repressors and silencers
- Histones 
- CpG island, GC content and DNA methylation

Transposable elements: half of the genome are repeats
====

Some regions of the genome can pick up and move around in the genome, this idea seemed crazy when first brought up by Barbara McClintock[^1], people jokingly called them "jumping genes".
These elements turned out to be real, and were named **transposons** or **transposable elements**.

They move around, copy pasting themselves all over the genome. As a result, roughly half of the genome is made up of transposable elements.

[[Transposons|More on transposons, retrotransposons, transposable genes, and tandem/ interspersed repeats]].

[[A Bit of Cell Biology]]
===
More on eukaryotes, prokaryotes, cell division process and some jargons like haploid, diploid, karyotype and homo/hetero- zygous.

[^1]: Barbara McClintock is a legend. Elected to the National Academy of Science when she was 40 years old in 1840s. . Her proposal in the 50s that "genes jump around" was met with great mockery and backlash. She stood her ground and continued research on her own for decades afterward, disappearing from the academia world. Until molecular techniques in 1980s caught up and showed her right. Check out this exciting tale told by the awesome Robert Sapolsky [here](https://youtu.be/dFILgg9_hrU?t=1180).
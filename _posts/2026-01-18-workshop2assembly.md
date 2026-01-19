---
layout: distill
title: Ancient DNA assembly
date: 2026-01-18
description: Part 2 of the Museomics Workshop
tags: workshop museomics tutorial

authors:
  - name: Daniel Y. M. Nakamura
    affiliations:
      name: University of São Paulo

toc:
  - name: What is museomics?
  - name: Challenges and solutions
  - name: Preprocessing
    subsections:
      - name: Quality assessment
      - name: Trimming
      - name: Deduplicating
      - name: Decontaminating
      - name: Damage profile
  - name: Assembly
    subsections:
      - name: Indexing and linear mapping
      - name: Iterative mapping
      - name: Reference bias
  - name: Postprocessing
    subsections:
      - name: Blast
      - name: Phylogenetic analysis
---

This tutorial is Part 2 of the Museomics Workshop (CVZoo XIV 2025, University of São Paulo, Brazil). Check Part 1 [here](https://dnakamuraz.github.io/blog/2026/workshop1linux).

Before starting the tutorial, download the resources [here](https://github.com/dnakamuraz/dnakamuraz.github.io/tree/master/assets/museomics).

## What is museomics?

**Museomics** is the application of genomic and bioinformatic methods to museum specimens, such as skins, bones, teeth, feathers, shells, pinned insects, and herbarium samples. These specimens are typically from extinct or historically collected organisms and were not preserved with DNA analysis in mind. **Paleogenomics** is the study of very old biological material preserved under natural conditions, such as subfossils from caves (e.g., Neanderthals and Denisovans) and permafrost-preserved tissues (e.g., Ice Age megafauna). Archival DNA refers to DNA recovered from any archived biological material, including DNA from museum specimens (historical DNA; hDNA), typically ranging from ~50 to 200 years old, as well as much older biological material (ancient DNA; aDNA), which can range from thousands to millions of years old. However, in the literature, these terms are sometimes used interchangeably.

## Challenges and solutions

Working with ancient, historical, and archival DNA presents several well-known challenges. DNA molecules are typically **highly fragmented** and present in very low quantities, with **low endogenous content** due to the predominance of environmental and microbial DNA. In addition, **post-mortem damage**—especially cytosine deamination leading to C→T and G→A substitutions—introduces characteristic errors, and **contamination** from modern DNA (human, microbial, or laboratory sources) can overwhelm authentic sequences. 

The aforementioned challenges have driven the development of both wet-lab and computational solutions. In the laboratory, **dedicated hDNA facilities** with strict contamination control, optimized extraction protocols, and the use of **second-generation sequencing** (NGS) enable the recovery of short DNA fragments (reads). Enzymatic treatments such as **USER** (UDG + Endonuclease VIII) can partially or fully remove deaminated cytosines, reducing post-mortem mutation while preserving damage patterns when authentication is required. In silico, bioinformatic approaches explicitly model ancient DNA properties, including the use of **damage profiles** to identify and authenticate ancient molecules, **iterative mapping** strategies to improve alignment of short and divergent reads, and filtering steps to minimize contamination. Overall, these strategies have provided advances in museomics.

Below, you will learn how to preprocess, assemble, and postprocess hDNA reads.

## Preprocessing

### Quality assessment

### Trimming

### Deduplicating

### Decontaminating

### Damage profile

## Assembly

### Indexing and linear mapping

### Iterative mapping

### Reference bias

https://gtpb.github.io/CPANG22/

https://pangenome-hackathon-genotoul-bioinfo-11d6d4f47ac33734abfa2a1377.pages-forge.inrae.fr/pages/tutorial_pangenome_graph/

## Postprocessing

### Blast

### Phylogenetic analysis
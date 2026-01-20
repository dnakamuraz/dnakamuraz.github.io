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
  - name: Assembly
    subsections:
      - name: Indexing and linear mapping
      - name: Iterative mapping
      - name: Reference bias
  - name: Post-processing
    subsections:
      - name: Blast
      - name: Phylogenetic analysis
---

This tutorial is Part 2 of the Museomics Workshop (CVZoo XIV 2025, University of São Paulo, Brazil). All software used in this tutorial were previously installed during Part 1 of the Museomics Workshop: [check it here](https://dnakamuraz.github.io/blog/2026/workshop1linux)!

Before starting the tutorial, download the resources [here](https://drive.google.com/drive/folders/1FBC4-8E0TquQqtSvxvlzb8qbxzKSuuGg?usp=sharing).

## What is museomics?

**Museomics** is the application of genomic and bioinformatic methods to museum specimens, such as skins, bones, teeth, feathers, shells, pinned insects, and herbarium samples. These specimens are typically from extinct or historically collected organisms and were not preserved with DNA analysis in mind. **Paleogenomics** is the study of very old biological material preserved under natural conditions, such as subfossils from caves (e.g., Neanderthals and Denisovans) and permafrost-preserved tissues (e.g., Ice Age megafauna). Archival DNA refers to DNA recovered from any archived biological material, including DNA from museum specimens (historical DNA; hDNA), typically ranging from ~50 to 200 years old, as well as much older biological material (ancient DNA; aDNA), which can range from thousands to millions of years old. However, in the literature, these terms are sometimes used interchangeably. As a shorthand, we will use only the terms “museomics” and “hDNA” throughout this tutorial.

## Challenges and solutions

Working with hDNA presents several well-known challenges. DNA molecules are typically **highly fragmented** and present in very low quantities, with **low endogenous content** due to the predominance of environmental and microbial DNA. In addition, **post-mortem damage**—especially cytosine deamination leading to C→T and G→A substitutions—introduces characteristic errors, and **contamination** from modern DNA (human, microbial, or laboratory sources) can overwhelm authentic sequences. 

The aforementioned challenges have driven the development of both wet-lab and computational solutions. In the laboratory, **dedicated hDNA facilities** with strict contamination control, optimized extraction protocols, and the use of **second-generation sequencing** (NGS) enable the recovery of short DNA fragments (reads). Enzymatic treatments such as **USER** (UDG + Endonuclease VIII) can partially or fully remove deaminated cytosines and abasic sites, reducing post-mortem mutation. **Single-stranded libraries** can minimize the loss of endogenous content. In silico, bioinformatic approaches explicitly model ancient DNA properties, including the use of **damage profiles** to identify and authenticate ancient molecules, *in silico* contaminant deletion, **iterative mapping** strategies to improve alignment of short and divergent reads, and filtering steps to minimize contamination. Overall, these strategies have provided advances in museomics.

Below, you will learn how to preprocess, assemble, and post-process hDNA reads.

## Preprocessing

There are four FastQ files in `museomics/part2/1_raw_reads`. Two of them corresponding to *Dendropsophus rhea* MZUSP 14458 and two of them to *Dendropsophus tritaeniatus* MZUSP 73973. Both were subsampled from the original files.

Initially, we merge these single-end FastQ files that represent the same sample.

```bash
# Create a new directory
mkdir 2_merged
cd 2_merged

# Merge reads from multiple lanes but same sample
cat ../1_raw_reads/Drhea* > Drhea_merged.fastq
cat ../1_raw_reads/Dtritaeniatus* > Dtritaeniatus_merged.fastq

# Rename headers to avoid redundancy
conda activate seqkit
seqkit rename Drhea_merged.fastq > Drhea_renamed.fastq
seqkit rename Dtritaeniatus_merged.fastq > Dtritaeniatus_renamed.fastq
conda deactivate seqkit
```

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: FastQ files are classified into single-end or paired-end reads. Single-end reads are sequenced from only one end per fragment and usually generate a single file per sample (except in cases where multiple lanes of sequencing are performed for the same sample). Paired-end reads are sequenced from both ends (one strand is the read 1 and its complementary strand is the read 2) and usually generate two files per sample (R1 and R2). The choice between these two approaches depends on the research question. If a high-volume of data and lower costs are necessary, single-end reads suffice. Otherwise, identifying structural variants and alternative splicing likely require paired-end reads. 

</div>

### Quality assessment

FastQC is an essential quality control (QC) tool used in bioinformatics to assess the raw data generated by high-throughput sequencers (like Illumina). Developed by Simon Andrews at Babraham Bioinformatics, it provides a quick, visual overview of whether your sequencing data has potential problems, biases, or contamination before you proceed to downstream analysis.

Assuming you are in `museomics/part2/2_merged`, run:

```bash
conda activate fastqc
fastqc *fastq
conda deactivate
```

The output HTML files `Drhea_renamed_fastqc.html` and `Dtritaeniatus_renamed_fastqc.html`present plots that can be visualized in the navegator.

>
> **Exercise 1**
> Check the file `Drhea_renamed_fastqc.html`. Create hypotheses to explain the distributions of (1) per base sequence content, (2) sequence length, and (3) sequence duplication levels.
>
><details>
><summary>Show answer</summary>
>
> (1) The distribution of per base sequence content presents a high proportion of G relative to A, C and T at the ends of reads. A explanation is the high level of PolyG at the ends of reads. PolyG commonly indicate signal decay during the end of sequencing (and not a real G) because G is represented by no color in some platforms.<br>
> (2) The sequence length distribution reveals a bicaudal distribution. The first peak around 20-30 bp likely represents fragmented hDNA reads, whereas the the second peak around 98-100 bp likely represents modern DNA contaminants.<br>
> (3) The sequence duplication levels reveals that some sequences are duplicated. Possible explanations include PCR duplicates not removed and fragments from different regions of the genome randomly sharing the same sequence.
> 
></details>

### Trimming

Illumina libraries present adapters that must be trimmed before downstream analyses. Furthermore, hDNA fragments are often naturally very short (sometimes <50 bp). As such, finding unusually long reads (e.g., >200–300 bp in a highly degraded sample) is actually a major red flag. Conversely, while museomics depends on short fragments, there is a limit to how short a read can be while still being useful. Very short fragments (< 20 bp) can introduces massive noise into your data, making it impossible to accurately map reads (assemble hDNA) in next steps.

```bash
# Create a new directory
mkdir 3_cutadapt
cd 3_cutadapt

# Trim adapters and reads out of size range
conda activate cutadapt
cutadapt -O 4 -a AGATCGGAAGAGCACACGTC -m 21 -M 90 -o Drhea_cutadapt.fastq ../2_merged/Drhea_renamed.fastq
cutadapt -O 4 -a AGATCGGAAGAGCACACGTC -m 21 -M 90 -o Dtritaeniatus_cutadapt.fastq ../2_merged/Dtritaeniatus_renamed.fastq

# Assess quality of reads
conda activate fastqc
fastqc *fastq
conda deactivate
```

>
> **Exercise 2**
> Check the files `Drhea_cutadapt_fastqc.html` and `Dtritaeniatus_cutadapt_fastqc.html`. Compare both reports with those before trimming. Described what happened with the per base sequence quality, sequence distribution, overrepresented sequences, and adapter content.
>
><details>
><summary>Show answer</summary>
>
> The per base sequence quality improved, the sequence distribution is right-skewed with one peak between 20-30 bp, and adapters and other artifacts like PolyG were deleted.
> 
></details>

### Deduplicating

```bash
conda activate tally
tally -i 1_cutadapt_21-90bp/"$x"*.fastq.gz -o 2_tally_21-90bp/"$x"_tally_21-90bp.fastq --nozip --with-quality
gzip 2_tally_21-90bp/"$x"_tally_21-90bp.fastq
conda deactivate tally 
```

### Decontaminating

## Assembly

### Indexing and linear mapping

### Iterative mapping

### Reference bias

https://gtpb.github.io/CPANG22/

https://pangenome-hackathon-genotoul-bioinfo-11d6d4f47ac33734abfa2a1377.pages-forge.inrae.fr/pages/tutorial_pangenome_graph/

## Post-processing

### Blast

### Phylogenetic analysis
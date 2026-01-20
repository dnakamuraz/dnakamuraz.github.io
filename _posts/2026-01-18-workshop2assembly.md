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
      - name: Consensus calling
      - name: Blast
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

Assuming you are in `museomics/part2/2_merged`, run:

```bash
# Create a new directory
mkdir ../3_cutadapt
cd ../3_cutadapt

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
> Check the files `Drhea_cutadapt_fastqc.html` and `Dtritaeniatus_cutadapt_fastqc.html`. Compare both reports with those before trimming. Describe what happened with the per base sequence quality, sequence distribution, overrepresented sequences, and adapter content.
>
><details>
><summary>Show answer</summary>
>
> The per base sequence quality improved, the sequence distribution is right-skewed with one peak between 20-30 bp, and adapters and other artifacts like PolyG were deleted.
> 
></details>

### Deduplicating

Deleting PCR duplicates is necessary because hDNA libraries originate from a very small number of unique, degraded template molecules that require extensive PCR amplification to reach detectable levels. Consequently, many PCR duplicates are present, which, if not removed, falsely inflate sequencing coverage. Leaving these duplicates in your analysis can lead to significant errors such as false-positive SNP calls, compromising the accuracy of population genetic and phylogenetic inferences.

Assuming you are in `museomics/part2/3_cutadapt`, run:

```bash
# Create a new directory
mkdir ../4_tally
cd ../4_tally

# Delete PCR duplicates
conda activate tally
tally -i ../3_cutadapt/Drhea_cutadapt.fastq -o Drhea_tally.fastq --nozip --with-quality
tally -i ../3_cutadapt/Dtritaeniatus_cutadapt.fastq -o Dtritaeniatus_tally.fastq --nozip --with-quality

# Assess quality of reads
conda activate fastqc
fastqc *fastq
conda deactivate
```

>
> **Exercise 3**
> Check the files `Drhea_tally_fastqc.html` and `Dtritaeniatus_tally_fastqc.html`. Compare both reports with those before deduplicating. Describe what happened with the total number of sequences (available in Basic Statistics) and the sequence duplication levels.
>
><details>
><summary>Show answer</summary>
>
> The total number of sequences slighly reduced because duplicates were deleted.
> 
></details>

### Decontaminating

In museomics, deleting contaminants using FastQ Screen is a critical step for validating the authenticity of historical DNA and preventing the analysis of modern biological noise. Because DNA from museum specimens is often highly degraded, non-target DNA—originating from the museum environment, laboratory handling (human DNA), or even the reagents themselves—can easily outnumber the endogenous DNA of interest. FastQ Screen allows researchers to map their sequencing reads against a panel of diverse reference genomes (e.g., human, common bacteria, or potential contaminants like PhiX) to quantify and then filter out sequences that do not belong to the target species.

Assuming you are in `museomics/part2/4_tally`, run:

```bash
# Create a new directory
mkdir ../5_fastqscreen
cd ../5_fastqscreen
conda activate fastqscreen
```

FastqScreen is a script to map reads against a database of potential contaminant genomes and delete the mapped reads. The first step is creating the database of contaminants. It is located in `museomics/part2/FastqScreenGenomes.zip`. Unzip this directory and check the subdirectories containing indexed contaminant genomes. The file `fastq_screen.conf` should be edited in each analysis to specify the path for Bowtie2 aligner (L12), number of threads (cores) for parallel analyses (line 21), and contaminant databases (human in line 42, *E. coli* in line 49, vector in line 56, adapters in line 63, and *Paraburkholderia* in line 70). 

In this tutorial, indexing genomes or editing the configuration file are not necessary for FastqScreen analyses. 

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: Indexing refers to two different processes in molecular biology. <br>

First, in library preparation (wet lab), indexing is the process of adding a unique "ID tag" to your DNA fragments in multiplex libraries (i.e. containing multiple samples to be sequenced together). Without these tags, you would have millions of sequences but no way to know which read came from which specimen. After sequencing, the sample of each read is identified during a computational step called demultiplexing. <br>

Second, in read mapping (bioinformatics), you need to "index" your reference genome. This is very similar to the index at the back of a textbook. Tools like BWA, Bowtie2, or Samtools create a separate set of files (e.g. hash table) that split the reference genome into k-mers and their correspondent positions in the genome. Another tools for indexing is the Burrows-Wheeler Transform (BWT) that reorder the genome millions of times (so that similar characters are grouped together) and keep the last column. If you tried to align a read by searching the 3-billion-base human genome from start to finish for every single read, it would take years. With an index, the software can "jump" directly to the potential matching locations in milliseconds.

</div>

```bash
# Define the configuration file
config="/home/daniel/hDNA/FastqScreenGenomes/fastq_screen.conf"

# New contaminants might be indexed with the following command: bowtie2-build contaminant_name.fasta name_index
fastq-screen --nohits --aligner bowtie2 --conf $config ../4_tally/Drhea_tally.fastq
fastq-screen --nohits --aligner bowtie2 --conf $config ../4_tally/Dtritaeniatus_tally.fastq

# Assess quality of reads
conda activate fastqc
fastqc *fastq
conda deactivate
```

## Assembly

Sanger assembly is a small-scale, manual process where you stitch together a few long, high-quality reads (up to 900 bp) to confirm a single gene or plasmid, typically visualized through chromatogram peaks. In contrast, next-generation sequencing (NGS) handle millions of shorter reads (usually 50–150 bp) simultaneously (in second-generation) or a few long reads (10k-1M bp) with high error rates. NGS read mapping (alignment) acts like a "shortcut" by using an existing reference genome as a blueprint to organize these tiny fragments. NGS de novo assembly is the most complex approach, building a entirely new genome from scratch by finding overlaps between reads without any external guide—a task that is often impossible for degraded museum samples due to the lack of sufficient overlap between highly fragmented DNA pieces.

As such, in museomics, we use second-generation sequencing and short read mapping.

### Indexing and linear mapping

```bash
# Index each reference seed 
mkdir 4a_seeds; cp -r /home/daniel/hDNA/4a_seeds/fasta 4a_seeds/
cd 4a_seeds; mkdir bwa_index
CONDA_ENV_NAME="bwa"
conda activate "$CONDA_ENV_NAME"
for s in ${seeds[@]}; do
bwa index -p "$s" /home/daniel/hDNA/Hylodidae_2/4a_seeds/fasta/"$s".fasta
mkdir "$s"
cp "$s"* "$s"/
mv "$s" bwa_index/
done
cd ..

# For each species...
mkdir 4b_BWA_21-90bp; cd 4b_BWA_21-90bp
for x in ${roots[@]}; do
# For each gene, run BWA ALN using replicable seed 1024, missing prob of 0.01 under 0.02 error rate
for i in ${seeds[@]}; do
# Align reads (bwa aln index fastq.gz > sai)
CONDA_ENV_NAME="bwa"
conda activate "$CONDA_ENV_NAME"
bwa aln -l 1024 -n 0.01 -t 20 /home/daniel/hDNA/Hylodidae_2/4a_seeds/bwa_index/"$i"/"$i" \
/home/daniel/hDNA/Hylodidae_2/3_fastqscreen/"$x"*.tagged_filter.fastq.gz > "$x"_"$i".sai

# Convert .sai to .sam (bwa samse -f sam index sai fastq.gz)
bwa samse -f "$x"_"$i".sam /home/daniel/hDNA/Hylodidae_2/4a_seeds/bwa_index/"$i"/"$i" \
"$x"_"$i".sai \
/home/daniel/hDNA/Hylodidae_2/3_fastqscreen/"$x"*.tagged_filter.fastq.gz

CONDA_ENV_NAME="samtools"
conda activate "$CONDA_ENV_NAME"
# Convert .sam to .bam
samtools view --threads 20 -S -b "$x"_"$i".sam > "$x"_"$i"_mapANDunmap.bam
# Remove unmapped reads
samtools view --threads 20 -F 4 "$x"_"$i"_mapANDunmap.bam > "$x"_"$i"_map.bam

rm "$x"_"$i".sai
rm "$x"_"$i"_mapANDunmap.bam
rm "$x"_"$i".sam
```

### Iterative mapping

```bash
```

### Reference bias

Reference bias is an error where the mapped reads are reference-dependent. In museomics, this is particularly dangerous because hDNA is very short. If a read has too many differences from the reference, the aligner decides it’s "too messy" and throws it away or assigns it a low Mapping Quality (MapQ). As such, the final results will look "more like the reference" than the actual sequenced organism.

Although out of the scope of this short workshop, possible solutions for reference bias include:

- (1) running multiple linear mappings against different references, aligning output consensus sequences, and masking ambiguities with IUPAC (less conservative) or N (more conservative); 
- (2) running a multiple sequence alignment (MSA) of potential references, calling the consensus of the MSA and coding SNPs with IUPAC ambiguities, and mapping reads against this synthetic reference (suggested in [Oliva et al. 2021](https://academic.oup.com/bib/article/22/5/bbab076/6217726));
- (3) running multiple linear mappings against different references, selecting best mappings using mapping quality indexes, merging mappings via lift over procedure (developed by [Chen et al. 2021](https://link.springer.com/article/10.1186/s13059-020-02229-3))
- (4) building variation graph from different references and mapping reads against the graph (see tutorials [here](https://pangenome-hackathon-genotoul-bioinfo-11d6d4f47ac33734abfa2a1377.pages-forge.inrae.fr/pages/tutorial_pangenome_graph/) and [here](https://gtpb.github.io/CPANG22/)). 

## Post-processing

### Consensus calling

```bash
miraconvert
samtools
```

### Blast

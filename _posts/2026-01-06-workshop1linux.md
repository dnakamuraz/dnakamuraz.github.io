---
layout: distill
title: Introduction to Linux for bioinformatics
date: 2026-01-06
description: Part 1 of the Museomics Workshop
tags: workshop museomics tutorial

authors:
  - name: Daniel Y. M. Nakamura
    affiliations:
      name: University of São Paulo

toc:
  - name: What is Linux?
  - name: Using Linux on Windows
  - name: Basic commands
    subsections:
      - name: Navigating the filesystem
      - name: Managing files and using wildcards
      - name: Reading, writing, and selecting text
  - name: Installing software
    subsections:
      - name: Binaries
      - name: Environments
      - name: Other methods
---

This tutorial is Part 1 of the Museomics Workshop (CVZoo XIV 2025, University of São Paulo, Brazil). Because most programs used to assemble historical DNA sequences in museomics and to perform downstream phylogenetic analyses do not provide a graphical user interface (GUI), the goal of this tutorial is to introduce students to the Linux command line.

Before starting the tutorial, download the resources [here](https://drive.google.com/drive/folders/1FBC4-8E0TquQqtSvxvlzb8qbxzKSuuGg?usp=sharing). Unzip the files and go to the output directory (try `cd /mnt/c/Users/Aluno/Downloads/CVZoo*/CVZoo/`). 

Check Part 2 of the Museomics Workshop [here](https://dnakamuraz.github.io/blog/2026/workshop2assembly/).

## What is Linux? 

Linux was created in 1991 by Linus Torvalds (1969, Finland) as a free and open-source kernel that controls CPU, manages memory, handles files, and communicates with hardware. However, kernels are not operating systems (i.e. Linux is not like Windows and macOS). In parallel with Linux, the GNU Project was initiated by Richard Stallman (1953, USA) in 1983 to include command-line tools, compilers, libraries, and shells. As such, most systems commonly referred to as "Linux" should more properly be called "GNU/Linux".

Linux systems can be distributed in different packages (distros). Some popular distros are:

| Distribution | Pros | Cons |
|-------------|------|------|
| **Ubuntu** | Beginner-friendly; large community and documentation; excellent hardware support; widely used in servers, cloud, and bioinformatics | Heavier system; less conservative than Debian; some design choices are controversial |
| **Debian** | Extremely stable and reliable; conservative updates; large repositories; foundation of many other distros | Older software versions; hardware support may lag; less beginner-oriented |
| **Arch Linux** | Minimal and highly customizable; rolling release; excellent documentation (Arch Wiki); great for learning Linux internals | Steep learning curve; manual installation; higher risk of breakage; not beginner-friendly |
| **SteamOS** | Optimized for gaming; excellent performance on Steam Deck; showcases Linux on consumer devices | Not designed for general-purpose computing; limited customization; unsuitable for scientific workflows |
| **Zorin OS** | Very beginner-friendly; Windows/macOS-like interface; Ubuntu-based with good software support | Smaller community; limited flexibility; rarely used in research or servers |
| **Linux Mint** | User-friendly and stable; traditional desktop layout; lightweight; based on Ubuntu or Debian | Slower adoption of new technologies; desktop-focused; uncommon in servers or HPC |

When mobile distributions are taken into account, Android is the most popular Linux-based system. As such, most people are Linux users, even if they are unaware of it.

## Using Linux on Windows

Windows is the most popular operating system. However, most programs used in bioinformatics are only available for Linux and macOS, because both are UNIX-based (a family of operating systems designed for scientific computing in 1969-1971 at AT&T Bell Labs).

To use Linux commands in Windows, there are four options:

- Remote Linux: Connecting to a Linux server from a local Windows machine (cons: servers are not available to all users).
- Dual Boot: Installing Linux and Windows in the same computer (cons: requires storage partitioning).
- Virtual machine: Emulating Linux inside Windows (cons: slow, requires more RAM and CPU than a native Linux).
- Windows Subsystem for Linux (WSL): Using a compatibility layer (cons: a few tools may be incompatible).

Given that servers are unavailable, Dual Boot requires splitting the disk, and virtual machines are slow in most cases, the best solution is WSL. Most software is compatible with WSL. The first version of WSL was released in 2016 by Microsoft, but most tools were incompatible. Recently, WSL 2 was released in 2019, with native support for Docker, Conda, and modern Linux tools. Microsoft created WSL because Linux dominates servers, cloud, high-performance, and scientific computing. Using WSL, beginner scientists can continue using Windows but also using Linux tools (although I recommend that all scientists migrate to Linux).

To install WSL 2, Windows 10 or 11 are required:
1. Open PowerShell as Administrator.
2. Run the command `wsl --install`
3. Restart your computer.
4. Open Ubuntu, create a Linux username and password.
5. Inside Ubuntu, run:

```bash
sudo apt update
sudo apt upgrade
```

The Windows files are usually located at `/mnt/c/`.

## Basic commands

To run Linux from the command line, the user must open a **shell**, a program that sends the commands from the user to the operating system. In GNU/Linux, the most common shell is called **Bash** (Bourne Again Shell). 

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: If the Bash prompt starts with `alan@turing:~\$`, `alan` is the user name, `turing` is the name of the machine (hostname), `~` is the current directory, and `\$` indicates the type of user (`$` = normal user, `#` = root/admin).

</div>

In Bash, a standard input (*stdin* e.g. hDNA reads or cladistic matrix) is given to a command, which produces the standard output (*stdout* e.g. assembled contigs or phylogenetic trees). In addition to standard input and output, programs also produce a standard error (*stderr*), which is typically used for warnings and error messages.

In Bash, the commands are case-sensitive (e.g. `echo` is a valid command, whereas `Echo` or `ECHO` are not). Moreover, pressing the "Tab" key attempts to autocomplete commands and file names (helpful for lazy users). The semicolon `;` executes commands sequentially and independently, whereas the pipe `|` connects the output of one command directly to the input of the next.

### Navigating the filesystem

When working in Linux, especially in bioinformatics projects, analyses are organized into directories containing raw data, intermediate files, and results. Efficient navigation of the filesystem is therefore essential. 

```bash
pwd      # show current directory
ls .     # list files in the current directory
ls -lh   # list files with sizes
ls -a    # list all files (including hidden files)
ls -lhS  # list files sorted by size
ls -ltr  # list files sorted by modification time
cd ..    # go up one directory
cd ../.. # go up two directories
cd ~     # go to home directory
cd /     # go to the root directory
cd -     # return to the previous directory
file     # classify a file
du -sh   # size of current directory
```

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: The current directory is indicated by a single dot `.`, whereas the parent directory is indicated by two dots `..`. The home directory is indicated by `~` and the root by `/`.

</div>

>
> **Exercise 1**
>
> In a single line, which command sequentially (1) list files with sizes in the current directory, (2) list all files in the parent directory, (3) go to the parent directory, and (4) show the path to the current directory?
> 
> A) `ls -lh .; ls -a ..; cd ..; pwd`
> 
> B) `ls -lh ..; ls -a .; cd ..; pwd ..`
> 
> C) `ls -lh . ls -a .. cd .. pwd`
> 
> D) `ls -lh .| ls -a .. | cd .. | pwd`
>
><details>
><summary>See the answer</summary>
>
>A
>
></details>
>

> **Exercise 2**
> Consider that you are in museomics/part1/ex2:
>
> ```text
> museomics/
> └── part1/
>     ├── ex2/
>     │   ├── sample1.fq
>     │   ├── sample2.fq
>     │   └── consensus.fas
>     └── ex3/       
>```
>
> Which single-line command prints the current directory, lists all files in the current directory, identifies the file type of sample1.fq, shows the total disk usage of the directory.
>  
> A) `pwd; ls -a; file sample1.fq; du -sh`
>
> B) `pwd; ls -a; file ../sample1.fq; du -sh data`
>
> C) `pwd | ls | file sample1.fq | du -sh ..`
>
> D) `pwd; ls; file raw/sample1.fastq; du -sh .`
>
><details>
><summary>See the answer</summary>
>
>A
>
></details>

### Managing files and using wildcards

Once you can navigate the filesystem, the next essential skill is creating, copying, moving, renaming, and deleting files and directories. These operations are fundamental in bioinformatics, where workflows typically involve organizing raw data, intermediate files, and results into structured directories.

```bash
cp file .         # duplicate files to the current directory
cp -r directory . # copy a directory to the current directory
mkdir new_dir     # make a new directory called new_dir
mkdir -p new/dir  # create parent directories if necessary
mv ../file .      # move files
rm file           # remove files
rm -r directory   # remove directories
rm -ri directory  # remove directory asking for confirmation
```
<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: Wildcards are used to match groups of files or text efficiently. The asterisk `*` matches any number of characters (including none; e.g. `*.fastq` matches all FASTQ files, and `sample*` matches all files starting with “sample”). The question mark `?` matches exactly one character (e.g. `sample?.fasta` matches sample1.fasta but not sample10.fasta). Square brackets `[]` define character classes, matching one character from a set or range (e.g., `sample[1-3].fastq` matches sample1.fastq, sample2.fastq, and sample3.fastq). Curly braces `{}` enable brace expansion, generating multiple strings rather than matching patterns (e.g., `sample{A,B}.fasta` expands to sampleA.fasta and sampleB.fasta). 

</div>

>
> **Exercise 3**
> You are in museomics/part1/ex3, a directory containing:
>
> ```text
> consensus.fas
> sample1.fq
> sample2.fq
> notes.txt
>```
>
> Write a single command line that: (1) Creates a directory called `raw`, (2) moves all FASTQ (= fq) files into `raw`. 
>
><details>
><summary>See the answer</summary>
>
>mkdir raw; mv *.fq raw/
>
></details>

>
> **Exercise 4**
> You are working in the directory museomics/part1/ex4, which contains the following files: 
> 
>```text
> consensus.fas
> sample1_R1.fq
> sample1_R2.fq
> sample2_R1.fq
> sample2_R2.fq
> sample3_R1.fq
> sample3_R2.fq
> control_R1.fq
> control_R2.fq
> notes.txt
>```
>
> In a single command, (1) create the directories analysis/reads and analysis/reads2 if it does not exist, (2) copy the FASTQ files from samples 1 and 3 (both R1 and R2) into analysis/reads, and (3) copy all files R2 into analysis/reads2.
>
><details>
><summary>See the answer</summary>
>
>mkdir -p analysis/reads; mkdir analysis/reads2; cp sample[13]_R*.fastq analysis/reads/; cp *R2.fq analysis/reads2/
>
></details>

### Reading, writing, and selecting text

Bioinformatics workflows rely on text files, including FASTA, FASTQ, SAM/BAM (text/binary), VCF, and comma- and tab-delimited tables. Linux provides powerful command-line tools to view these large text files.

```bash
cat file              # print the file
echo hello            # print strings
less file             # view files safely
less -N file          # view files with line numbers
head file             # print the first 10 lines
head -n 20 file       # print the first 20 lines
paste file1 file2     # merge two files
paste - -             # merge each two lines from stdin
tail file             # print the last 10 lines
tail -n 20 file       # print the last 20 lines
wc file               # count lines, words, and characters
wc -l file            # count lines
wc -w file            # count words
wc -c file            # count bytes
wc -m file            # count characters
zcat file             # print gzipped files in linux
gzcat file            # print gzipped files in macOS
```

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: Avoid using `cat` and `zcat` for large files due to memory limitation. Instead, use `less` or `zcat | head`.

</div>

In addition to text visualization, Linux provides commands to manipulate files.

```bash
grep                   # search for patterns
grep -i                # search for patterns ignoring case
grep -v                # inverted search (excluding patterns)
grep "ˆ"               # match beginning of line 
grep "$"               # match end of line
cut                    # extract specific columns from tables
cut -d                 # specify delimiter (default: TAB)
sed 's//g'             # substitute 's/pattern/new_pattern/g'
sed -i 's//g'          # substitute within the file (irreversible)
sed '1~3 y/actg/ACTG/' # substitute actg with ACTG in lines 1 + every 3 lines (only works in GNU/Linux, not macOS)
sort                   # sort lines alphabetically or numerically
sort -r                # sort using reverse order
sort -k                # sort by column
sort -t                # specify delimiter
sort -u                # unique lines
tr acgt ACGT           # translate each character in set1 to each character in set2
uniq                   # deduplicate lines (requires sorted input)
nano                   # beginner-friendly text editor
vi                     # advanced text editor
```

>
> **Exercise 5**
> You are working in the directory museomics/part1/ex5, which contains the FASTA file called `genes.fasta`. The first three sequences are:
>```text
> > COI_Homo_sapiens
> ATGCTAGCTAGCTAGC
> > COI_Mus_musculus
> ATGCTAGCTAGCTAGC
> > cytb_homo_sapiens
> ATGCGGATCGATCGTA
> ```
> There are dozens of sequences, so counting or manipulating them manually is too laborious. Which commands should we use to (1) list only the FASTA headers, (2) count how many sequences are in the file, and (3) count how many sequences contain the word Homo, ignoring case. 
>
><details>
><summary>Show answer</summary>
>
>(1) grep "^>" genes.fasta <br>
>(2) grep "^>" genes.fasta | wc -l <br>
>(3) grep -i "homo" genes.fasta | wc -l
>
></details>

>
> **Exercise 6**
> You are working in the directory museomics/part1/ex6, which contains the FASTQ file called `reads.fq.zip`. The first four reads are:
> ```text
> @read003
> atGCTAGCtAGC
> +
> IIIIIIIIIIII
> @read002_LOWQUAL
> atgctagnnnn
> +
> !!!!!IIIII!!
> @read003
> atGCTAGCtAGC
> +
> IIIIIIIIIIII
> @read004_LOWQUAL
> ATGCGTAGCTAG
> +
> !!!!!!!IIIII
> ```
> (1) Remove all reads marked as LOWQUAL (remove the entire read, not only the header), (2) deduplicate sequences, and (3) convert all nucleotides to uppercase.
>
><details>
><summary>Show answer</summary>
>
>(1) cat reads.fq | paste - - - - | grep -v "LOWQUAL" <br>
>(2) cat reads.fq | paste - - - - | sort -u <br>
>(3) cat reads.fq | tr actgn ACTGN <br>
>
>Given that solution for the third task also replaces actg in the headers, a better solution (only works in Linux) is: <br>
>
>(3) cat reads.fq | sed '2~4 y/actgn/ACTGN/'
>
></details>

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: Each read comprises four lines. To merge the four lines into a single line (so that each line is located in a single line), use `cat reads.fq | paste - - - -`

</div>

## Installing software

Installing tools efficiently is key to running analyses reproducibly. This tutorial covers the main ways to install and manage software in Linux. The examples are the programs required in the Part 2 of the Museomics Workshop: gargamel, FASTQC, Cutadapt, Tally, FastqScreen, BWA, MITObim, samtools, bamtools, TNT, and PhyG.

### Binaries

Some software comes as a ready-to-use binary, meaning you don’t need to compile it. You only need to change the permissions of the binary and move it to the PATH.

Every file and directory in Linux has three types of permissions:
- (1) read (r), permission to view the file contents or list a directory;
- (2) write (w), permission to modify the file or directory;
- (3) execute (x), permission to run a program or enter a directory.

Every file has permissions specified for three types of users:
- Owner (u), the user who owns the file;
- Group (g), the users in the same group as the file;
- Others (o), everyone else.

When you type `ls -l` in Bash, the first string in each line indicates the permissions. For instance:

```bash
> ls -l
-rwxr-xr-- 1 alice bioinfo 2345 Jan 18 12:00 analysis.sh
``` 

The user `alice` owns the file `analysis.sh` of 2345 bytes, last modified on January 18th. The permissions of this file for the owner, group, and others are `rwx` (reading, writing and executing), `r-x` (only reading and executing), and `r--` (only reading), respectively. That is, users in the same group can only read and execute the file, whereas users that are not part of the group can only read it. If Alice wants to make this file executable to all users, she can use the command `chmod +x analysis.sh`. However, the program `analysis.sh` can only be used if the user is in the same directory where it is located (e.g. typing `./analysis.sh`) or if the user specifies the path to it. This is not convenient, so moving the file `analysis.sh` to the PATH (a list of directories that Linux searches when you type a command) will allow Linux to easily execute `analysis.sh`. 

```bash
> echo $PATH # list PATH directories
/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin
> mv analysis.sh /usr/local/bin/ # move the program to one of the PATH directories
```

TNT is the most popular program in phylogenetic analyses under parsimony and static homology (nucleotides aligned via a similarity function from MSA, assumes homologies a priori, based on phenetics). In contrast, PhyG performs phylogenetic analyses using dynamic homology (nucleotides aligned via tree-alignment, assumes that the best homology schemes are implied from the optimal tree). Both programs can be installed via binaries. 

#### TNT

1. Download TNT v. 1.6 for Linux [here](https://www.lillo.org.ar/phylogeny/tnt/tnt-linux.zip)
2. Unzip the directory, change the mode of the binary, and move it to the PATH: 
```bash
unzip tnt-linux.zip
chmod +x tnt
echo $PATH
mv tnt /usr/local/bin/
```

#### PhyG

1. Download PhyG v. 1.3 for Linux [here](https://github.com/amnh/PhyG/releases/download/v1.3.0/phyg-linux-x86)
2. Change the mode of the binary and move it to the PATH: 
```bash
chmod +x phyg-linux-x86
echo $PATH
mv phyg-linux-x86 /usr/local/bin/
```

### Environments

In addition to binaries, most programs in bioinformatics are available in Conda, which is an environment manager widely used in scientific computing to install software and manage dependencies in isolated environments. This is useful to avoid dependency conflicts. 

Two popular Conda distributions are Anaconda (a large Conda distribution of ca. 3 GB containing hundreds of packages for data science) and Miniconda (a cleaner Conda distribution of ca. 70 Mb containing only Python and a few libraries). Bioconda is not a Conda distribution, but a repository hosting most bioinformatics software.

```bash
# Install conda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh 

# Re-start shell
source ~/.bashrc 
conda --version
```

Below, we will install all programs necessary for [Part 2 of the Museomics Workshop](https://dnakamuraz.github.io/blog/2026/workshop2assembly/).

#### seqkit

seqkit will be installed in the conda environment called `seqkit`.

```bash
conda create -n seqkit bioconda::seqkit
conda activate seqkit
seqkit
conda deactivate
```

#### FastQC 

TNT and PhyG are not available in Bioconda. In contrast, FastQC v.0.12.1 is available and will be installed in the conda environment called `fastqc`. 

```bash
# Install possible dependencies of java for fastqc
sudo apt update
sudo apt install -y libxtst libxrender1 libxi6 libxrandr2 libxinerama1 libxcursor1

# Create an environment
conda create -n fastqc bioconda::fastqc
conda activate fastqc
fastqc -h
conda deactivate
```

#### Cutadapt

Cutadapt v.2.6 will be installed in the environment `cutadapt` with Python v.3.7.

```bash
conda create -n cutadapt python=3.7 bioconda::cutadapt=2.6
conda activate cutadapt
cutadapt -h
conda deactivate cutadapt
```

#### Tally

Tally (from Reaper v.16.098) will be installed in the environment `tally`.

```bash
conda create -n tally bioconda::reaper
conda activate tally
tally -h
conda deactivate
```

#### FastqScreen

FastqScreen v.0.15.3 will be installed in the environment `fastqscreen`. In my experience, recent versions can perform poorly in Linux, so use this specific version. 

```bash
conda create -n fastqscreen bioconda::fastq-screen=0.15.3
conda activate fastqscreen
fastq_screen -h
conda deactivate
```

Note that the environment is called `fastqscreen`, whereas the command is `fastq_screen`.

#### BWA

BWA v.0.7.17 will be installed in the environment `bwa`.

```bash
conda create -n bwa bioconda::bwa
conda activate bwa
bwa
conda deactivate
```

#### MITObim

MITObim v.1.9.1. will be installed in the environment `mitobim`. In addition to MITObim, Mira, and MiraConvert will be automatically installed. For macOS and Windows, use Docker (instructions [here](https://github.com/chrishah/MITObim); conda does not work, in my experience). For Linux (including Windows WSL), the following commands are currently working:

```bash
conda create -n mitobim bioconda::mitobim
conda activate mitobim
MITObim.pl -h
conda deactivate
```

#### Samtools and Bamtools

Samtools and bamtools will be installed in the environment `samtools`.

```bash
conda create -n samtools -c conda-forge -c bioconda samtools=1.23
conda activate samtools
conda install bioconda::bamtools
samtools
bamtools
conda deactivate
```

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: If you want to delete an environment, type `conda remove --name NAME --all`. If you are inside an environment and want to uninstall a specific package, type `conda remove NAME`. If you want to list all available environments, type `conda env list`. 

</div>

### Other methods

| Method | When to use | Pros | Cons |
|--------|------------|------|------|
| Precompiled binaries | Single tool, simple | Fast, no compilation | Manual updates, dependency issues |
| Conda / Bioconda | Multiple tools, environments | Dependency management, reproducible | Disk space, environment conflicts |
| Compilation | Latest version, HPC optimization | System optimized | Error-prone, requires build tools |
| Docker | Reproducibility, pipelines | Isolated, cross-platform | Requires Docker, may be slow |
| Modules | HPC / cluster | Easy switching, no installation | Only on supported systems |

Check the Part 2 of the Museomics Workshop [here](https://dnakamuraz.github.io/blog/2026/workshop2assembly/).
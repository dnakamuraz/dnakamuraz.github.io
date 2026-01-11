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
      - name: Reading and writing text
      - name: Manipulating text
  - name: Advanced commands
    subsections:
      - name: Scripts and permissions
      - name: Loops, conditions, variables
  - name: Installing software
    subsections:
      - name: Binaries
      - name: Environments
---

This tutorial is Part 1 of the Museomics Workshop (CVZoo XIV 2025, University of São Paulo, Brazil). Because most programs used to assemble historical DNA in museomics and to perform downstream phylogenetic analyses do not provide a graphical user interface (GUI), the goal of this tutorial is to introduce students to the Linux command line.

## What is Linux? 

Linux was created in 1991 by Linus Torvalds (1969, Finland) as a free and open-source kernel that controls CPU, manages memory, handles files, and communicates with hardware. However, kernels are not operating systems (i.e. Linux is not like Windows and macOS). In parallel with Linux, the GNU Project was iniciated by Richard Stallman in 1983 to include command-line tools, compilers, libraries, and shells. As such, most systems commonly referred to as "Linux" should be better called as "GNU/Linux". 

The Linux systems can be distributed in different packages (distros). Some popular distros are:

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

Windows is the most popular operating system. However, most programs used in bioinformatics are only available to Linux and macOS, because both are UNIX-based (a family of operating systems designed for scientific computing in 1969-1971 at AT&T Bell Labs).

To use Linux commands in Windows, there are four options:

- Remote Linux: Connecting to a Linux server from a local Windows machine (cons: servers are not available to all users).
- Dual Boot: Installing Linux and Windows in the same computer (cons: requires storage partitioning).
- Virtual machine: Emulating Linux inside Windows (cons: slow, requires more RAM and CPU than a native Linux).
- Windows Subsystem for Linux (WSL): Using a compatibility layer (cons: a few tools may be incompatible).

Given that servers are unavailable, Dual Boot requires splitting the disk, and virtual machines are slow in most cases, the best solution is WSL. Most softwares are compatible with WSL. The first version of WSL was released in 2016 by Microsoft, but most tools were incompatible. Recently, WSL 2 was released in 2019, with native support for Docker, Conda, and modern Linux tools. Microsoft created WSL because Linux dominates servers, cloud, high-performance, and scientific computing. Using WSL, begginer scientists can continue using Windows but also using Linux tools (albeit I recommend all scientists to migrate to Linux).

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

## Basic commands

To run Linux from the command line, the user must open a **shell**, a program that sends the commands from the user to the operating system. In GNU/Linux, the most common shell is called **Bash** (Bourne Again Shell). 

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: If the Bash prompt starts with `alan@turing:~\$`, `alan` is the user name, `turing` is the name of the machine (hostname), `~` is the current directory, and `\$` indicates the type of user (`$` = normal user, `#` = root/admin).

</div>

In Bash, a standard input (*stdin* e.g. hDNA reads or cladistic matrix) is given to a command, which produces the standard output (*stdout* e.g. assembled contigs or phylogenetic trees). In addition to standard input and output, programs also produce a standard error (*stderr*), which is typically used for warnings and error messages.

In Bash, the commands are case sensitive (e.g. `echo` is a valid command, whereas `Echo` or `ECHO` are not). Moreover, pressing the "Tab" key attempts to auto-complete commands and file names (helpful for lazy users). The semicolon `;` executes commands sequentially and independently, whereas the pipe `|` connects the output of one command directly to the input of the next.

### Navigating the filesystem

When working in Linux, especially in bioinformatics projects, analyses are organized into directories containing raw data, intermediate files, and results. Efficient navigation of the filesystem is therefore essential.

```bash
pwd      # show current directory
ls       # list files
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

> **Exercise 1**
>
> In a single line, which command sequentially (1) list files with sizes in the current directory, (2) list all files in the parent directory, (3) go to the parent directory, and (4) show current directory?
> 
> A) `ls -lh .; ls -a ..; cd ..; pwd`
> 
> B) `ls -lh ..; ls -a .; cd ..; pwd ..`
> 
> C) `ls -lh . ls -a .. cd .. pwd`
> 
> D) `ls -lh .| ls -a .. | cd .. | pwd`
> 
> <details>
> <summary>See the answer</summary>
> A)
> </details>

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
> <details>
> <summary>See the answer</summary>
> A)
> </details>

### Managing files and using wildcards

Once you can navigate the filesystem, the next essential skill is creating, copying, moving, renaming, and deleting files and directories. These operations are fundamental in bioinformatics, where workflows typically involve organizing raw data, intermediate files, and results into structured directories.

```bash
cp       # duplicate files
cp -r    # copy directories
mkdir    # make a directory
mkdir -p # create parent directories if necessary
mv       # move files
rm       # remove files
rm -r    # remove directories
rm -ri   # remove directory asking for confirmation
```
<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; margin-bottom: 16px; border-radius:8px; background:#E7F0FE">

Tip: Wildcards are used to match groups of files or text efficiently. The asterisk `*` matches any number of characters (including none; e.g. `*.fastq` matches all FASTQ files, and `sample*` matches all files starting with “sample”). The question mark `?` matches exactly one character (e.g. `sample?.fasta` matches sample1.fasta but not sample10.fasta). Square brackets `[]` define character classes, matching one character from a set or range (e.g., `sample[1-3].fastq` matches sample1.fastq, sample2.fastq, and sample3.fastq). Curly braces `{}` enable brace expansion, generating multiple strings rather than matching patterns (e.g., `sample{A,B}.fasta` expands to sampleA.fasta and sampleB.fasta). 

</div>

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
> <details>
> <summary>See the answer</summary>
> mkdir raw; mv *.fq raw/
> </details>

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
> In a single command, (1) create the directories analysis/reads and analysis/reads2 if it does not exist, (2) copy the FASTQ files from samples 1 and 2 (both R1 and R2) into analysis/reads, and (3) copy all files R2 into analysis/reads2.
>
> <details>
> <summary>See the answer</summary>
> mkdir -p analysis/reads; mkdir analysis/reads2; cp sample[12]_R*.fastq analysis/reads/; cp *R2.fq analysis/reads2/
> </details>

### Reading and writing text
### Manipulating text

## Advanced commands
### Scripts and permissions
### Loops, conditions, variables

## Installing software
### Binaries
### Environments
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
      - name: Managing files and directories
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
2. Run the command "wsl --install".
3. Restart your computer.
4. Open Ubuntu, create a Linux username and password.
5. Inside Ubuntu, run:

```bash
sudo apt update
sudo apt upgrade
```

## Basic commands

To run Linux in the command line, the user must open the shell, a program that sends the commands from the user to the system. In GNU/Linux, the shell is called Bash (Bourne Again Shell). In Bash, a standard input (*std_in* e.g. hDNA reads or cladistic matrix) is given to a command, which produces the standard output (*std_out* e.g. assembled contigs or phylogenetic trees).

<div style="border:2px solid rgba(76, 117, 175, 0.87); padding:12px; border-radius:8px; background:#E7F0FE">

Tip: If the Bash prompt starts with `alan@turing:~\$`, `alan` is the user name, `turing` is the name of the machine, `~` is the current directory, and `\$` indicates the type of user (`$` = normal user, `#` = root/admin).

</div>

The commands are case sensitive (e.g. `echo` is a function, whereas `Echo` or `ECHO` are not). Moreover, pressing "Tab" attempts to complete commands (helpful for lazy users). If the user wants to type multiple commands in a single line, a `;` must be provided between commands

### Navigating the filesystem

```bash
pwd    # show current directory
ls     # list files
ls -lh # list files with sizes
ls -a  # list all files (including hidden files)
cd ..  # go up one directory
cd ~   # go to home directory
```

> **Exercise 1**
>
> In a single line, which commands (1) list files with sizes in the current directory, (2) list all files in the parent directory, (3) go to the parent directory, and (4) show current directory?
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

### Managing files and directories
### Reading and writing text
### Manipulating text

## Advanced commands
### Scripts and permissions
### Loops, conditions, variables

## Installing software
### Binaries
### Environments
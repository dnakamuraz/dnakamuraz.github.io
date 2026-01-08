---
layout: distill
title: Introduction to Linux for bioinformatics
date: 2026-01-06
description: Part 1 of the Museomics Workshop
tags: workshop museomics tutorial

authors:
  - name: Daniel Nakamura
    affiliations:
      name: University of São Paulo

toc:
  - name: What is Linux?
  - name: Using Linux on Windows
  - name: Basic commands
    subsections:
      - name: Navegating
      - name: Reading
      - name: Writing
  - name: Advanced commands
    subsections:
      - name: Scripts
      - name: Changing mode
  - name: Installing software
---

This tutorial is Part 1 of the Museomics Workshop (CVZoo XIV 2025, University of São Paulo, Brazil). Because most programs used to assemble historical DNA in museomics and to perform downstream phylogenetic analyses do not provide a graphical user interface (GUI), the goal of this tutorial is to introduce students to the Linux command line.

## What is Linux?

<center>
<img src="/assets/img/post_2026-01-06_fig1.jpg" alt="drawing" style="width:700px;"/>
</center>

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



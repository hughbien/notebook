Bioinformatics For Dummies
==========================

Jean-Michel Claverie and Cedric Notredame walks us through the tools and
techniques of bioinformatics.  It assumes some biology knowledge and teaches
the computer tools.

Finding Out What Bioinformatics Can Do For You
==============================================

This chapter explained some biology basics such as nucleotides and amino acids.
It also glosses over sub-fields of bioinformatics:

* analyzing protein sequences (amino acids)
* analyzing DNA sequences (nucleotides)
* analyzing RNA sequences
* coding DNA to protein
* genomics

Some keywords and ideas are explained in this chapter that will be used
throughout the rest of the book.

Biological experiments were done **in vivo** or within a living organism.
Eventually they began experimenting **in vitro** or within an artificial
environment (Latin for in glass).  With bioinformatics, part of the experiment
can be done **in silico** or within silicon chips.  Wet lab work still needs
to be done for emperical evidence, but bioinformatics lets you narrow down the
hypothesis of experiments.

**Proteins** are the worker macromolecules of cells.  They're built from 
**amino acids**.  Each amino acid (sometimes called **residue**)  has a 1-letter
and 3-letter code that represents it.  There are 20 amino acids in nature.

Amino acids are joined by **peptide bonds** which form from an `NH2` and `COOH`
on each amino acid.  There will be an unused `NH2` and `COOH` on the first and
last amino acid of the chain.  These are called the `N-terminus` and `C-terminus`
of the protein chain.  We typically read amino acid sequences from N-terminus
to C-terminus.  Here's an example sequence:

    MAVLD
    Met-Ala-Val-Leu-Asp
    Methionine-Alanine-Valine-Leucine-Aspartic

Amino acid chains or proteins form 3D structures in our cells.  The structure
is dependent on the sequence (some residues are hydrophobic and want to stay
inside, others are hydrophillic and want to be outside).  The electrical charges
of the amino acids also affect the structure.  The function of a protein is 
a consequence of its 3D structure:

    SEQUENCE => STRUCTURE => FUNCTION

**DNA or deoxyribonucleic acid** is another macromolecule in our cells.  They're
made up of **nucleotides** which are represented by a set of four letters:

* A for Adenine
* C for Cytosine
* T for Thymine
* G for Guanine

DNA has "hooks" like amino acids known as the 5' phosphoryl and 3' hydroxyl.
We read DNA from the 5' to 3' positions.  DNA takes the shape of a double helix.
It has two complementary strands.  For more information about DNA, check out
my notes in *Molecular Biology for Dummies* or *Khan Academy: Biology*.

**RNA or ribonucleic acid** is transcribed from DNA and used to make proteins.
RNA differs from DNA by one nucleotide and is usually single stranded.  Just
switch out the `T` with a `U` for Uracil.  Single-stranded RNA typically forms
hairpin structures with itself made of loops (unpaired region) and stems
(paired regions).

Some keywords for nucleotides:

* base, base pair, nucleoside, and nucleotides all slightly differ in definition
  but usually indicate the length of a sequence (ie 5 base pairs in length)
* nt stands for nucleotide
* bp stands for base pair
* kb (kilo-base) stands for 1000 bp 
* Mb (mega-base) stands for 1 million bp

A DNA sequence can be translated to a protein sequence.  Each three nucleotides
(**triplet** or **codon**) will be translated into one amino acid.  There are
three different ways to translate them because of triplets (known as
**reading frames**).  Since there are two strands, there are six possible reading
frames for DNA.  An **open reading frame** is a sequence that is missing a
STOP codon.

We used to sequence DNA by concentrating on individual genes.  Now, we sequence
the entire **genome** of an organism.  Studying the genome as a whole is called
**genomics**.  We sequence and discover what genes do at the same time.

How Most People Use Bioinformatics
==================================

Using Nucleotide Sequence Databases
===================================

Using Protein and Specialized Sequence Databases
================================================

Working with a Single DNA Sequence
==================================

Working with a Single Protein Sequence
======================================

Similarity Searches on Sequence Databases
=========================================

Comparing Two Sequences
=======================

Building a Multiple Sequence Alignment
======================================

Editing and Publishing Alignments
=================================

Working with Protein 3D Structures
==================================

Working with RNA
================

Building Phylogenetic Trees
===========================

Twelve Commandments for Using Servers
=====================================

Useful Bioinformatics Resources
===============================

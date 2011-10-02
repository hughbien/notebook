Understanding Bioinformatics
============================

Textbook by Marketa Zvelebil and Jeremy O. Baum.  It's a primer on the theory
and application behind the tools of bioinformatics.

Dealing with Databases
======================

## Why are databases the backbone of bioinformatics research?

50 years ago the first protein sequence of bovine insulin was determined by
Frederick Sanger.  10 years later, scientists were collecting more sequences
into one database to aid in analysis of similar sequences.  Now there are
hundreds and thousands of documented nucleotide sequences and over a hundred
thousand protein sequences too.  Every day, new techniques like microarrays
and gene expression measurements need to be recorded.

With the increase in data in biomedical research, databases are crucial for
storing and retrieving biological information.  Scientists can draw better
conclusions using the wealth on information from multiple databases.

## What are flat file databases and why are they still used today?

Flat file databases are a simple form of a database where collections of data
are stored in a large single text file or collection of text files.  They're
flat in contrast to **relational databases**.

They're still used today for their simplicity, especially with text-handling
computer languages.  They're easy to read but difficult to do complex searches.

## What are the benefits of relational databases?

In relational databases, a record could have a link to one or more different
records using a shared field called a **database key**.  For example, a row
in the `persons` table could have a `person_id` which could also exist in the
`employees` table.  Having the same value for this field would link them.  The
author notes that relational databases are probably the most common.

Popular relational databases use the **Structured Query Language (SQL)** to
retrieve data.

The most obvious benefit is that they are efficient at storing data.  There's
no redundancy between records since you can use a key to join two or more
records.  Information in two tables can be combined.  SQL provides operations
for easy manipulation, analysis of the data, and complex searches.

## What other type of database structures exist?

XML (extensible markup language) is a language for marking up data using tags.
It provides a structured way for presenting data, making it easier for
programmers to define an easy-to-extract interface.

Distributed databases are split among multiple computers to deal with large
amounts of data.  A data warehouse will centralize all of the data for easy and
faster retrieval.

The author also mentions "object-oriented databases" where you don't need SQL
or any other special database programming language.  Instead you just use your
application's main language like Java or C++.  He also mentions it's usually
just a front to relational databases.  I think he's talking about ORMs.

## Why do databases contain both data and annotations of data?

The author refers to data as the key information in the database entry such as
the sequence, structure, or expression levels.  Data also includes information
to identification such as the name of the gene.  Any additional information
are annotations such as links to related entries in other databases,
interpretation of the data, and relevant research citations.

There's also a difference between primary and secondary data.  Primary data is
the raw result of experiments or the initial experiment interpretation.
Secondary data is derived from pre-existing data.  Because of this, secondary
data can be out-dated or inaccurate as we learn more.  Some examples of primary
data: gene expression array scans, protein sequences, results from gels.  Some
examples of secondary data: comparisons of multiple sequences.

Whereas data is the primary reason for using databases, the annotations are
helpful for scientists to discover more information about their subject.
Annotations can contain links to published papers, other known facts or
interpretations, and lists of highly relevant entries in other databases.

## What types of databases exist?

Every year the first issue of Nucleic Acids Research (NAR) includes new and
updated databases.  The URLs can be found on the NAR homepage.

There are funded centers dedicated to providing convenient access to the growing
number of databases:

* NCBI [http://www.ncbi.nlm.nih.gov]
* EBI [http://www.ebi.ac.uk]
* Sanger Institute [http://www.sanger.ac.uk]

Databases became specialize and form different types depending on the
information they hold.  People who start these projects start with a question
and model their database structure after the ideal answer.  Some of the types
of databases are:

* nucleotide sequence
* microarray/gene expression
* human genes/diseases
* proteomics
* pathways/interactions
* RNA sequence/structure
* human/vertebrate genomes
* protein sequences
* structural data

There are even different types of DNA.  Genomic DNA includes noncoding regions,
introns, and regulatory regions.  cDNA (reverse transcribed from mRNA) only
includes expressed DNA at the time of an experiment.

**Expressed sequence tags (ESTs)** is a partial cDNA sequence.  Libraries of
ESTs indicate the range of genes being expressed in a sample.

## Why is the quality of data important?

Analysis based on incorrect data will lead to erroneous conclusions.  It's
important that the data be correct.  Scientists will even include extra
information detailing what degree of uncertainty they have about the data.

## Why is checking the data and human curation necessary?

There is often issues with data redundancy - a single gene could show up under
two names.  Or two genes could have the same names.

Computer checks are usually good for the primary data, but the annotations
usually require human intervention.  Annotations can be in any form.  The
highest quality databases have human curation.

Many sites even require publication of research before they'll input the data.

Producing and Analyzing Sequence Alignments
===========================================

## How is homology determined by sequence alignment?

Evolutionary **homology** means two sequences are evolutionary related. 
Alignment is the task of locating equivalent regions of two or more sequences to
maximize their similarity.  The similarities in sequences can help construct
phylogenetic trees or trace evolutionary relationships between species or within
a family of proteins.

## Describe different uses of protein and DNA sequence alignments.

There are two types of data used in alignment: DNA sequences and protein
sequences.  DNA alignment use the four nucleotides ACTG.  Protein sequences
are made up of the 20 amino acids.

It's easier to detect homology when comparing protein sequences vs nucleic
acid sequences.  Since the alphabet for DNA is only four letters, the probability
of similarity is very high.  It's tough to differentiate similarity from
homology.  Also, the genetic code is redundant.  Two or more different codons
can code for the same amino acid.  Protein structure is very specialized for
its function so it evolves much slower.

Since protein structure is so important, often times sequences could have very
different amino acids but still function similarly.

Sequences that are very similar to each other but aren't evolutionary homologous
are said to be the result of **convergent evolution** (instead of divergent).

## What are scoring alignments?

Sequence alignment has to take into account many factors: differences in amino
acids could result in the same function, some mutations are accepted others are
not, effectiveness of gaps, etc...  Specialized scoring schemes have been
developed to generate the best possible alignments.  "Best" usually means best
for what you're looking for.  It could be evolutionary homologous, similarity,
or just similar function.

The quality of an alignment is measured by giving it a quantitative **score**. 
The best is referred to as the **optimal alignment**.  Slightly worse scores are
called **suboptimal alignments**.  No scoring scheme has been developed that
perfectly models the evolutionary process.

Some scoring alignments are:

* **Identity**: percent of identical matches vs total length of sequence.  It's
  naive/easy, doesn't take into account for gaps
* **dot-plot**: uses a matrix to compare two sequences
* **substitution matrices**: assign scores to aligned sequence positions.  The
  **PAM substitution matrices** use point frequencies derived from sets of
  closely related protein sequences.

90% of sequence pairs with identity >= 30% were pairs of structurally similar
proteins.  Only 10% of sequence pairs with Below 25% sequence identity were
structurally similar.  The region between 30% and 20% is the **twilight zone**
and less than 20% is the **midnight zone**.

An **accepted mutation** is a mutation that has been retained in the sequence
(aka the mutation hasn't killed off the organism).  PAM stands for Point
Accepted Mutations.  The PAM250 means that 250 mutations have been fixed on
average per 100 residues.

Another commonly used substitution matrix is the **BLOSUM matrix** which stands
for BLOck SUbstitution Matrix.  The PAM matrix indicate evolutionary distance
whereas the BLOSUM matrix refers to percentage identity.

## How do you make alignments between two sequences?

Some vocabulary first:

* **indels** are differences in sequences that could be the result of an
  insertion in one sequence or a deletion in another.  We don't know.
* **gaps** are unaccounted sequence portions which don't match
* **gap penalty** is the penalty amount due to a single gap (ignoring length)
* **gap extension penalty** is the penalty amount due to number of letters in gap

There are different types of alignments you can use:

* **global alignment** covers the entire length
* **local alignment** covers only parts of sequences, for example only one
  domain of a protein

For both types of alignments there are two methods:

* **pairwise alignment** of just two sequences (answer to this question)
* **multiple alignments** of more than two sequences

For two sequences to do a pairwise alignment, you'd use a Needleman-Wunsch-type
algorithm or Smith-Waterman.  Find the alignment with the best score.  Look
for the optimal solution and a few sub-optimal solutions.

## How do you make multiple alignments between many sequences?

Multiple alignments can be used to find interesting patterns characteristic of
specific protein families, build phylogenetic trees, detect homology between
new sequences and existing families, or to help predict secondary/tertiary
structures of new sequences.

It can be constructed with a few different techniques:

* **cluster analysis** first does pairwise alignments, then performs an analysis
  on pairwise data to generate a hierarchy of sequences in order of similarity.
  The hierarchy is a phylogenetic tree called the **guide tree**.  The alignments
  are slowly combined until all sequences are aligned.
* **divide-and-conquer method** sequences are cut several times to reduce length,
  aligned, then concatenated into a multiple alignment

Some software packages to help with multiple alignments are ClustalW, DIALIGN,
AMAS, CINEMA, and ESPript.

## Compare local alignment techniques for finding limited areas of similarity.

FASTA is a fast database-search method based on matching short identical segments
at the expense of some sensitivity.  It speeds up search via hashing of the
search query (called **k-tuples** short stretches of k contiguous residues).
Partial matches are ignored due to the hashing.

BLAST (Basic Local Alignment Search Tool) or Wu-BLAST is based on finding very
similar short segments.  First, all suitable matches are located in each
database sequence.  Matches are then extended without including gaps and are
ranked.  Highest scoring sequences are subjected to dynamic programming to
obtain final alignment and scores.

PSI-BLAST enables profile-based database searches.  A profile or position
specific scoring matrix of set of sequences is constructed and replaces the
substitution matrix in the BLAST search.

SSEARCH is a rigorous alignment method based on the Smith-Waterman algorithm.
The Smith-Waterman algorithm compares segments of all possible lengths and
chooses the segment that optimizes the similarity measure.  It's slower but
much more accurate.

## Explain global alignment techniques for matching whole sequences.

It's possible to estimate the probability of two random sequences aligning
with a score greater than or equal to S, which is usually reported as the
**expectation value or E-value** in database search results.  The probability
follows the extreme-value distribution.  Closely related sequences are given
a very small E-value of 10^-20 or less.  An E-Value of 3 means you'll find
three sequence alignments in the database (suggesting it's not related).  The
smaller the E-value the better.

Different databases can be used to solve particular problems.  GenBank for DNA
sequences, SWISS-PROT for annotated protein sequences, TrEBML for translated
EMBL DNA-sequence, PDB for protein structures, dbEST for EST database.

## How do you search databases for homologous sequences?

Set a low threshold on the E-Value before doing your database search.  Use the
correct scoring algorithm for alignment (PAM) and set the level to be reasonable
as per the amount of evolution time between the two sequences.

## How do you look for patterns and motifs in a protein sequence?

A similarity between an unknown sequence and a sequence of known function may
be limited to a few critical residues.  You need a method of searching for
the occurrence of short sequence patterns (called **motifs**).  A motif is a
conserved element of a sequence alignment.

There are specialized databases dedicated to patterns in the genome and proteins.
It's mostly used with protein sequences instead of nucleotide sequences.  These
specialized databases have been constructed by hand or via a consensus method
(the most similar regions in are used to construct a pattern).

Some of these databases and tools are:

* BLOCKS database
* PROSITE database
* PHI-BLAST (Pattern Hit Initiated BLAST)
* PRINTS database holds fingerprints representing sets of conserved motifs
* Pfam database defines profiles of protein families

## How do you use patterns/motifs to locate proteins of similar functions?

During signal transduction (signals of environments transmitted to the inside
of the cell) components are chemically/transiently modified.  Usually they
include adding/removing phosphate groups.  Neural network methods can predict
serine, threonine, and tyrosine phosphorylation sites in eukaryotic proteins.
The NetPhos tool and PROSITE database can help with these.

Hydrophobic cluster analysis (HCA) compares the alpha helices to compare
possible similar functions of proteins.

Proteins often contain instrinsic sequence motifs that are involved in their
delivery to sites.  If you know the sequence motifs, you can figure out where
the protein will end up in the cell.

Pairwise Sequence Alignment and Database Searching
==================================================

## Compare and contrast different scoring schemes.

Alignment scores attempt to measure the likelihood of a common evolutionary
ancestor.

* Point Accepted Mutations (PAM) matrix were designed to trace evolutionary
  origins of proteins.  The higher the matrix number (PAM-1 vs PAM-250) the more
  mutations that have been accepted (further back in time).
* BLOSUM matrices were designed to find conserved regions of proteins.  A lower
  BLOSUM matrix can be used for evolutionary distance sequences (inverse of PAM).

## Summarize techniques for obtaining best-scoring alignments of given type.

Optimal global alignments are produced using efficient variations of the
Needleman-Wunsch algorithm.

## Describe ways to reduce the computational resources required.

* Don't calculate the whole matrix
* Use dynamic programming techniques
* Index using a suffix tree (used in BLAST)
* Index using hashing

## Speed up database searches using index techniques.

You can index using a suffix tree or hashing.  FASTA uses hashing and chaining.
BLAST uses indexing.

## Summarize techniques for aligning DNA and protein sequences together.

This requires some modifications to the BLAST and FASTA algorithms.  Two problems
arise:

1. A nucleotide sequence can be translated into protein in six different
   reading frames (three on each strand), so that there are six different
   potential protein sequences to be examined for each nucleotide sequence
2. Insertion of deletion errors can be present in the nucleotide sequence
   resulting in frameshift mutations

An **open reading frame** is a special reading frame that does not contain a
stop codon.

You can either translate the nucleotide sequence into amino acids or
back-translate the amino acid sequence.

## Identify sequences of low complexity.

Simple sequences include stretches of identical amino acids in proteins,
repeated short sequences, and longer DNA repeats.  About half of all database
searches contain at least one such region.

The program SEG uses compositional complexcity as a measure to determine regions
of simple sequences.

## Identify significant alignments on the basis of their score.

Alignment scores are based on an extreme-value distribution.  We can use
statistics and biological knowledge to determine if the alignment score is
good (E-Value).

## Summarize the techniques for alignments involving complete genome sequences.

Indexing becomes more important with the complete genome sequences, the computer
will need enough memory to hold the index.

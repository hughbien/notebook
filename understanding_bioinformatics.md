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

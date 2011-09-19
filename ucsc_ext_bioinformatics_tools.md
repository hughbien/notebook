UCSC Extension: Bioinformatics Tools, Databases, and Methods
============================================================

My notes for UCSC Extension's Bioinformatics Tools course taught by Lee Kozar.
This course is an introduction to the public domain tools and databases like
GenBank, PBD, and BLAST.

The notes are in a question/answer format.  They go better with the slides from
lectures.

Bioinformatics Databases
========================

## What database do you search for info about introns in a gene?

You can use the GenBank database managed by NCBI [http://www.ncbi.nlm.nih.gov].
Search in the Genome database, the GenBank format includes introns.

## What database do you search for genetic diseases caused by a gene?

OMIM or the Online Mendelian Inheritance in Man database can be used to search
for information about genetic diseases [http://www.ncbi.nlm.nih.gov/omim].

## What database do you search for gene therapy information with a gene?

NCBI's Genetics & Medicine database would be a good place to search.  Also,
PubMed would be useful.

## What database do you search for info about polymorphic variation in a gene?

There is currently do specialized database for polymorphic variants.

## Can you use SRS to search sequence databases?

Yes, the Sequence Retrieval System was developed for that purpose.

## Can you use MeSH to search sequence databases?

No, Medical Subject Headings are strictly for medical literature purposes.

## Why are there so many bioinformatics databases?

Because each organization wants to own their own data.  They each start with
a question in mind and they develop a database/format specifically to answer
that question.

## If you have a sequence in GenBank format, why would you convert it to FASTA?

Not all softwares accept the GenBank format as input.  You may have to convert
it to FASTA to work with it.

## Can a FASTA file hold more than one sequence?

Yes, it can hold as many sequences as you like.  The format uses a `>` character
as a delimiter.  Here's an example:

    >filename00    One line descriptive text
    GATACCATATACCATAGGATTCATTTA
    >filename01    One line of text
    TACTACCTTACGATCCAGGTAACTACA

## How does a computer know when a sequence starts/ends in a GenBank file?

The keyword `ORIGIN` denotes the start of a sequence and a double forward slash
`//` denotes the end of a sequence.

## Can a GenBank file hold more than one sequence?

Yes it can.  Each sequence can have its own annotations like LOCUS and
DEFINITION also.

## Can a sequence only file hold more than one sequence?  Why/why not?

No it cannot.  A sequence only file has no delimeters, just a single sequence.
Since `ACTG` are the only characters allowed, there would be no way to separate
the sequences.

## What is the EST database used for?

Expressed Sequence Tags databases hold partial cDNA sequences.  They're only
the expressed sequences.  They're the most interesting sequences in the genome,
since those are the ones being used.

## Why would you place "limits" on your search of the GenBank database?

To narrow down your search and find what you're looking for.  You can specify
filters like organism.

## What info is lost on conversion of GenBank sequence to FASTA?

A lot of the annotatations are lost because FASTA only contains the filename,
sequence, and one line of descriptive text.  Some of the annotations that are
lost include:

* Chromosome Region
* Gene Region
* What to assemble to get the mature protein

## Can that information be put back if you convert back to GenBank?

No, not unless you somehow coded for it in the single descriptive line of the
FASTA file.

## What information is lost when a SwissProt file is converted to FASTA format?

The SwissProt file contains many annotations that are lost including:

* Locus
* Accession ID
* Links to other research documents
* Links to other DBs
* Organism name

Literature Searching
====================

## Why is MeSH useful?

The Medical Subject Headings were developed to control vocabulary.  It solves
the problem of having no structured vocabulary for the large amounts of data
in our databases.

The keywords from MeSH makes it easier for someone to search the database and
find something relevant.

## What are the advantages and disadvantages of a controlled vocabulary?

The advantage is that it standardizes terms.  A scientist may have thought his
paper was about a certain gene, but a librarian will standardize the name so
someone searching for it will find all relevant documents.

It also limits the number of major points in an article to 3-4.  Searches could
turn up too many documents - because each document may mention a keyword only
once b/c it's slightly related.  By limiting it, keywords only match the document
if it's a major point of the article.

Unfortunately, the keywords you want to use might not be included in the
vocabulary yet.  It takes a while to enter MeSH.  Also, your article may have
more than 3-4 major points.

Straight from the lecture advantages:

* represent a subject concept, no synonyms needed
* find relevant articles that may not be mentioned in a title or abstract
* focus search, eliminate irrelevant records

Straight from the lecture disadvantages:

* scientific jargon not covered
* indexers may be inconsistent
* not every concept in an article can get thesaurus term

## What database do you search to find a gene given the discoverer's last name?

Use the SRS provided by EMBL
[http://srs.ebi.ac.uk/srsbin/cgi-bin/wgetz?-page+srsq2+-noSession].

One of the headers you can search by is the author's name of a paper.

## What searches can't be done at the PubMed/Medline database site?

PubMed/Medline are strictly medical databases.  If what you're searching for
is scientific but not related to the medical field, it won't be there.

## What is contained in the annotation part of the sequence?

Annotations include the accession number, locus, definition, keywords,
source organism, references, authors, links to other DBs, and more.

## What is more important, annotation or sequence?  Why?

The annotation is more important because it has the secondary data, such as
conclusions of experiments and links to other relevant material.  The annotations
give meaning to the data.

## What does "parsing" do to a database?

A computer "parses" data in order to understand it.  It's easier to parse data
when it's in a standardized format - such as the headers.  This makes it easy
to extract data from annotations.

During lecture, we also discussed NLP (Natural Language Processing).  NLP can
be applied to articles for computers to parse entire articles - not just the
header information.  This would make indexing extremely efficient as we could
automatically index articles based entirely on their content.

## Why are relational databases better than flat file databases?

It's easier to do complex searches with relational databases and they store
data more efficiently.

## What database/terms do you use to find human growth hormone protein sequence?

Use NCBI's protein database.  Search for "human growth hormone".  You can place
the entire search term in quotes to match the exact phrase.  Or you can make
your search more granular by searching for "growth hormone" AND "homo sapien".

## How does SRS differ from NCBI Search tools?

SRS allows you to search precisely using the headers.

## Why would you place "limits" on your search of the PubMed database?

To narrow your search and find exactly what you're looking for.

Statistics in Bioinformatics
============================

## Why is probability and statistics important for bioinformatics?
## How often would you find any dinucleotide sequence in a random sequence?
## How often would you find any tri-nucleotide sequence in a random sequence?
## Given a nucldeotide sequence with random distribution, what can you conclude?
## Given a non-random distribution, what does it tell you?
## When would you use a Hidden Markov Model to analyze a sequence?
## What is information theory used for in bioinformatics?
## How often do you expect a single nucleotide to appear in a random sequence?
## Is a sequence random if the four nucleotides appear in same frequency?
## What further tests would you do to make sure the sequence is random?

To answer this, come up with some sequences that had the same distribution
of nucleotides but was not random.

## How often would you find the cut site for HindIII in a random sequence?

To answer this, you need to find information about the HindIII cut site and its
length.

## What is a sliding window technique and why is it useful?
## Why is leucine the most common amino acid?
## What is a codon preference table?  When would you use one?
## Can the selection of a codon be used to regulate transcription rate?
## Can the selection of a codon be used to regulate gene expression?
## If you shuffle a sequence, will the single nucleotide frequencies change?
## You shuffle a sequence and di-nucleotide frequencies stay constant.  Why?
## How is a bioinformatics experiment designed?
## Where does the data from a bioinformatics experiment come from?
## How do you know if a bioinformatics experiment worked?
## What is Bayes Equation?  Why is it so useful?

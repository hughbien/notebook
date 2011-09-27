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

There is currently no specialized database for polymorphic variants.

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
to extract data from annotations.  We can parse a flat file database.

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

You can separate good results from bad results using statistical inference with
biological insight.  Using probability/statistics, you can ask yourself if an
outcome is reasonable.

## How often would you find any dinucleotide sequence in a random sequence?

The nucleotide alphabet is made up of four letters: A, T, G, C.  There are
sixteen different possibilities for any dinucleotide sequences:

    [ ] [ ]
     4 * 4 = 16

So you would expect to find any dinucleotide `1/16` times in a random sequence.

## How often would you find any tri-nucleotide sequence in a random sequence?

There are 64 different possibilities.

    [ ] [ ] [ ]
     4 * 4 * 4 = 64

So you'd epxect to find any tri-nucleotide sequence `1/64` times in a random
sequence.

## Given a nucldeotide sequence with random distribution, what can you conclude?

That there's nothing particular special about the sequence.  The actual average
distribution of the human genome is:

    A .295
    C .205
    G .205
    T .295

## Given a non-random distribution, what does it tell you?

That there's specific biology going on with this sequence.

## When would you use a Hidden Markov Model to analyze a sequence?

To determine if a sequence is the member of a certain family.

## What is information theory used for in bioinformatics?

Information theory deals with transmission of messages.  Sequence data can be
thought of as messages.  For example, a popular method of analyzing proteins
for secondary structure uses information theory (GOR Method).

## How often do you expect a single nucleotide to appear in a random sequence?

25% of the time because the nucleotide alphabet is made of four letters.

## Is a sequence random if the four nucleotides appear in same frequency?

Yes, if they appear in about the same frequency.  The actual distribution is
a little different.  A's and T's occur more often than C's and G's.

## What further tests would you do to make sure the sequence is random?

_To answer this, come up with some sequences that had the same distribution
of nucleotides but was not random._

## How often would you find the cut site for HindIII in a random sequence?

The recognition site for HindIII is:

    AAGCTT

That's 6 nucleotides in length.  The number of possible permutations are 

## What is a sliding window technique and why is it useful?

As you change the window size, the results will also change.  Patterns will start
appearing.  Small windows have noisy data, larger windows are smoother.

It's used to analyze the signal in data and to help find patterns.  It helps
filter out the noise.

## Why is leucine the most common amino acid?

Leucine has more codons that code for it than the other amino acids.  There are
20 amino acids, but 64 possibilities from triplet nucleotides.

Proteins that need to be expressed frequently will tend to have more amino acids
that are frequently expressed.

## What is a codon preference table?  When would you use one?

A codon frequency table includes the frequency that a codon occurs for a human
gene (and for other species and organelle).  You can use it to go backwards
from knowledge of a protein to figure out the DNA sequence, with a certain
probability.

## Can the selection of a codon be used to regulate transcription rate?
## Can the selection of a codon be used to regulate gene expression?
## If you shuffle a sequence, will the single nucleotide frequencies change?
## You shuffle a sequence and di-nucleotide frequencies stay constant.  Why?
## How is a bioinformatics experiment designed?

1. Go to the database, get training set
2. Collect data on probabilities of events
3. Calculate frequencies

## Where does the data from a bioinformatics experiment come from?

Online databases like SwissProt or GenBank.

## How do you know if a bioinformatics experiment worked?

Do a Bayesian analysis to make sure your results are "correct" to a certain
degree of belief.

## What is Bayes Equation?  Why is it so useful?

    P(b | a) = P(a | b) * P(b) / P(a)

This equation has four variables.  You usually have access to three, so you
can solve for the unknown probability.

Sequence Comparison
===================

## Why compare sequences?
## Why compare a sequence to itself, won't that just find a perfect match?
## Why penalize gaps?  How are gaps penalized?
## Why do you need a scoring function?
## What is a sequence comparison matrix used for?
## Why not use an identity matrix for proteins?
## How do you know if a sequence comparison is correct?
## What are sub-optimal alignments and why are they important?
## How do you choose what sequence comparison matrix to use?
## What is the goal of sequence comparison?  A good alignment shows what?
## What is the difference between similarity and homology?
## What is the difference between orthologs and paralogs?
## When would you use a global alignment and when would you use a local?
## What are the advantages and disadvantages of a dot matrix comparison?
## What do gaps along the diagonal of a dot matrix comparison represent?
## How would you reduce the noise in a dot matrix alignment?
## What do off diagonal lines represent in a dot matrix alignment?
## Why do we use the term INDEL to describe a gap in an alignment?
## What parameters in the LALIGN alignment program would you use?

_For the scenario of a computer giving you an alignment that had no gaps._

## What does a negative number in the PAM sequence comparison matrix tell you?
## Two sequences are found to be similar, are they also homologous?
## What is the best gap penalty to use?
## How does the software still manage to do a good job?

_You usually want an alignment program to align sequences based on evolutionary
events or align according to functional regions.  However, the software doesn't
know anything about the biology of the sequences being aligned.

## How are whole genome comparisons different than small sequence comparisons?
## Will a gene be on chromosome 1 of a mouse if it's on human chromosome 1?
## What is synteny?

Multiple Alignment
==================

## What can be learned from multiple alignments?  What do they answer?
## What further analyses can be done with the results of a multiple alignment?
## What is the most important step in the multiple alignment process?
## Why don't we use the method that guarantees best alignment?
## How does a progressive pairwise alignment work?
## What is a dendrogram?  How is it used?
## Why use a monospaced font when viewing/printing a multiple alignment?
## How can you tell whether a multiple alignment is correct?
## What are some databases of multiple alignments?
## How can multiple alignment databases be used?
## What alignment algorithm does Clustal use?
## How does the most recent version of Clustal differ from the original version?
## The best scored alignment might not be the correct alignment.  Why?
## How is T-Coffee an improvement over other methods of multiple alignments?
## When wouldn't you want to do a multiple alignment?
## How would you analyze a protein with domains X,Y,Z against protein with Z,X?
## What is a consensus sequence?
## What is a sequence logo?
## Can you use ClustalX to align only two sequences?  Is ClustalX the best way?
## Should you use only one comparison matrix for the following scenario?

_You're aligning many sequences and some are evolutionary close, others are
evolutionary distant._

## How does Clustal resolve the problem of evolutionary closeness and distance?
## How can you search a database using a multiple alignment?  Name some methods.
## What is the best method for searching a database using a multiple alignment?

Database Searching
==================

## What questions can be answered using database searching?
## How is sequence comparison different from database searching?
## Explain the tradeoff between speed and sensitivity in database searches.
## Why would you want to give up sensitivity to achieve speed?
## Can sequence comparison techniques be used to do database searches?
## Why does BLAST do a faster database search than other methods?
## When would you want to use a BLOSUM 80 comparison matrix?
## When would you use the BLASTX program?
## Which of DNA or protein sequences do you use for evolutionary distant matches?
## You're searching if a specific sequence is in the DB, do parameters matter?
## What are the limitations to BLAST?
## How does the window size improve search speed or sensitivity?
## What is an expectation value?
## What is the best expectation value you can get?
## Is an E value of 6e^-12 good or bad?
## What is a Z-score?
## Will the expectation value be the same for two searches that's months apart?
## Why can you infer homology with a DB search, but not with sequence comparison?
## What is a false positive?  What is a false negative?
## What does the TFASTA program do?
## Is translating DNA or backtranslating a protein better for searching?  Why?
## You backtranslate protein and do a DNA search, why no good match?
## GenBank has only one strand for each sequence, how do you search the opposite?
## Does GenBank use PAM or BLOSUM for searching DNA sequences?
## What is the value of searching a DB and looking at different reading frames?
## What are frame-shift mutations?
## Can you use BLAST to do a sequence comparison with only two sequences?
## Why filter sequences?  When would filtering not be a good idea?

Database Searching #2
=====================

## How does PSI-BLAST work?  Why is it better than BLAST?
## Does PSI-BLAST work with DNA?
## Can you use BLAST to compare two sequences?
## Can you send multiple sequences to BLAST at one time?
## What is better to use as a query sequence, DNA or protein?
## Give 3 examples for not translating DNA to protein before doing a DB search.
## Why are pattern searches more sensitive than searching with a single sequence?
## Why is a consensus sequence not very good to represent a multiple alignment?
## Why break up long protein or nucleotide sequences before doing a DB search?
## What comparison matrix would you use to find evolutionary distance sequences?
## There's a new gene in humans, why find the same gene in a simpler organism?
## What does the following mean?

_You discovered an ORF and do a DB search and find nothing statistically
significant._

## What's the advantage of searching using different reading frames?
## Why is filtering a sequence useful?
## What does filtering do?

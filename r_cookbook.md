R Cookbook
==========

The R Cookbook by Paul Teetor includes recipes for common and not-so-common
tasks.  Teetor takes us through the basics of R, shows us some solutions to
common tasks you'll see in any language, then he shows us specific tasks that
R was built to handle.

Getting Started and Getting Help
================================

This chapter explains how to download, install, and run R.  It's pretty
straightforward like any other software package.  On OS X, I used homebrew
<http://mxcl.github.com/homebrew/>:

    brew install R

Or you can download directly from the R website <http://www.r-project.org/>.
R works from the command line:

    % R
    R Version 2.13.2 (2011-90-30)
    ...
    > q()

Use `R --quiet` or `alias R="/usr/bin/R --quiet` in your zshrc for a less
verbose startup.

A good IDE to use is RStudio <http://rstudio.org/>.  To download more packages,
you'll want to use CRAN <http://cran.r-project.org/>, aka the Comprehensive R
Archive Network.  In R, you can start the documentation or get help:

    > help.start()
    > help(functionname)        # or ?functionname
    > RSiteSearch("key phrase")
    > help(package="tseries")
    > help.search("pattern")    # or ??pattern
    > args(functionname)
    > example(functionname)

Some Basics
===========

R is known for its REPL.  Just enter in expressions and R will implicitly
call the `print` function.

    > pi
    [1] 3.141593
    > print(pi)
    [1] 3.141593

The assignment operator is `<-`.  There's also `<<-` which is a forced global
assignment.  R also supports `=` and `->`.  The `ls` function will display
variables in your workspace:

    > ls()
    [1] "f" "x" "y"
    > ls.str()
    f: function (n, p)
    x: num 10
    y: num 50

Delete variables with `rm()`.  This works with single variables, varargs, or
with the named parameter `list`.  Vectors can be created with `c()`.

    > rm(x)
    > rm(x, y, z)
    > rm(list=c(x, y, z))

Vectors can only contain the same type.  R will implicitly cast to the lowest
common denominator.  Types are called "modes".

    > mode(3.1415)
    [1] "numeric"
    > mode("foo")
    [1] "character"
    > c(3.1415, "foo")
    [1] "3.1415" "foo"

Some basic statistic functions to use on vectors or data frames:

* `mean(x)`
* `median(x)`
* `sd(x)`
* `var(x)`
* `cor(x, y)`
* `cov(x, y)`

Most of these have a `na.rm` option to remove `NA` values from vectors.

    > mean(c(0,1,1,2,3,NA), na.rm=TRUE)
    [1] 1.4

R supports sequences with a shortcut for iterating by 1:

    > seq(from=1, to=5, by=2)
    [1] 1 3 5
    > seq(from=1, to=5, length.out=3)
    [1] 1 3 5
    > 1:5
    [1] 1 2 3 4 5
    > rep(1, times=5)
    [1] 1 1 1 1 1

Access elements using the `[]` operator.  The first index is `1`, not `0` like
other languages.  You can use sequences or vectors to access elements.

    > fib <- c(0,1,1,2,3,5,8,13,21,34)
    > fib[1]  # first index is 1, not 0
    [1] 0
    > fib[1:3]
    [1] 0 1 1
    > fib[c(1,2,4,8)]
    [1] 0 1 2 13

Use negative indexes to access the "inverse" - as in everything but that
negative index.

    > fib[-1]
    [1] 1 1 2 3 5 8 13 21 34
    > fib[-(1:3)]
    [1] 3 5 8 13 21 34

You can even use a vector of boolean values to select specific elements.

    > fib[fib < 5]
    [1] 1 1 2 3
    > fib[fib %% 2 == 0]
    [1] 0 2 8 34

Comparison operators work with scalars as well as vectors.  The available
comparison operators are `==`, `!=`, `<`, `>`, `<=`, `>=`.  When used when
vectors, they return a vector of per-element-results.

    > 3 == pi
    [1] FALSE
    > v <- c(3, pi, 4)
    > v < pi
    [1] TRUE FALSE FALSE
    > v < c(pi, pi, 5)
    [1] TRUE FALSE TRUE

Vector arithmetic works just like scalars.  If an operation is performed with a
vector and scalar, the scalar becomes a vector with repeat elements.

    > v <- c(11,12,13,14,15)
    > w <- c(1,2,3,4,5)
    > v + w
    [1] 12 14 16 18 20
    > v - w
    [1] 10 10 10 10 10

Let's define a function for the coefficient of variation:

    > cv <- function(x) {
      sd(x) / mean(x)
    }
    > cv(1:10)
    [1] 0.5504819

R has implicit return values - whatever the last expression is.  You can also
use `return(expr)`.  See `help(Control)` for more details.

Navigating the Software
=======================

Some useful functions to work with the working directory are:

* `getwd()` gets the current working directory
* `setwd()` sets the working directory

You can save your workspace with `save.image()`.  This holds all of your R
variables and functions.  It's usually written to a `.RData` file in the working
directory.  When R starts, it will initialize with any `.RData` files in the
working directory.

Use `history()` to view a recent history of your commands.  Or keep hitting the
up-arrow.  It shows the last 25 by default, you can ask for more `hist(Inf)`.

There's a special variable `.Last.value` which contains the last evaluated
expression in case you forgot to save it to a variable.

Here are some useful functions to work with packages and datasets.  Note that
the `help` function works with both.

* `search()` lists out the current packages you have loaded
* `library(packagename)` lets you load a specific package into memory.  Leave
  the parameters empty to get a list of all installed packages.
* `detach(package:packagename)` lets you unload a specific package
* `install.packages("packagename")` to install a package from CRAN
* `data(dataset, package="pkgname")` lets you access a dataset without loading
  the package
* `head(dataset)` and `tail(dataset)` to view start/end

You can save R scripts to an external file and execute its content from the
REPL with the `source` function.  It takes an optional `echo` parameter for
whether or not script lines should be echoed before executed:

    > source("hello.R", echo=TRUE)

You can also run the script without the REPL:

    % R CMD BATCH scriptfile outputfile
    % Rscript scriptfile arg1 arg2 arg3

Using `CMD BATCH` means you can't pass command line arguments and you can't
output directly to stdout.  Some useful options are:

* `--slave` which works like `--quiet`, suppressing echos
* `--no-restore` don't restore R workspace from .RData file
* `--no-save` don't save R workspace to .RData file
* `--no-init-file` don't initialize with .Rprofile or ~/.Rprofile

Inside your R script, command line arguments can be accessed from the
`commandArgs` function.

    #!/usr/bin/Rscript --slave
    argv <- commandArgs(TRUE)
    x <- as.numeric(argv[1])
    y <- as.numeric(argv[2])

Use `Sys.getenv('VAR')` and `Sys.setenv('VAR')` to get and set environment
variables on your system.  `R_HOME` is a special variable telling you where the
R files and config are stored.

`~/.Rprofile` can contain R code which will be run every time you start your
R environment.  It's useful for customizations.  Use `help(options)` to see
your available options.  You can also preload packages that you use often in
your .Rprofile file:

    suppressMessages(require(tseries))

Input and Output
================

The R Data Import/Export guide is available on CRAN at
<http://cran.r-project.org/doc/manuals/R-data.pdf>.  R was designed with the
Unix philosophy it mind, it does one thing well: statistics.  You may want to
use other tools to parse your data and import it into R.

You can do data entry directly into R.  For simple datasets, vectors will
suffice with the `c()` function.  You can create a data frame and edit it:

    > scores <- data.frame()
    > scores <- edit(score)

Floating point output prints up to 7 digits.  You may want more or less:

    > print(pi, digits=4)
    [1] 3.142
    > print(100*pi, digits=4)
    [1] 314.2

`cat` and `format` can be used to concatenate and format your numbers.  The
`format` function has a digits argument just like print.  `cat` lets you
redirect output to a file with its file argument.  Or just use `sink`.

    > cat("The answer is", answer, "\n", file="filename")
    > sink("filename")
    ...everything outputs to file...
    > sink()
    ...resume outputting to console...

`list.files()` shows content of working directory.  Use `recursive=T` to show
contents of subdirectories.  Use `all.files=T` to show hidden files.

Here are some useful functions to read/write data from files.  Be sure to use
`?` to read more about these functions, many of them include extra options.
Most of these work with URIs also.

* `read.fwf("filename", widths=(...))` to read fixed width files
* `read.table("filename", sep=" ")` to read tabular data
* `read.csv("filename")` to read CSVs
* `write.csv(dataset, file="filename")` to write CSVs
* `readHTMLTable` reads HTML tables, in the XML package
* `readLines` and `scan` to read individual lines/tokens and process them
  manually for complex structures

To work with MySQL databases, install the `RMySQL` package.  `dbConnect`
establishes a database connection, `dbGetQuery` to do a SELECT, and
`dbDisconnect` to terminate the connection.

    > library(RMySql)
    > con <- dbConnect(MySQL(), user="userid", password="pswd",
    +   host="hostname", client.flag=CLIENT_MULTI_RESULTS)

You can place these parameters in `~/.my.cnf` to avoid hard coding them into
R.  After establishing a connection, you can perform operations:

    > rows <- dbGetQuery(con, "SELECT * from SurveyResults WHERE City='Chicago'")
    > if (dbMoreResults(con)) dbNextResult(con)
    > print(rows)
    > dbDisconnect(con)
    [1] TRUE

Use the `save` and `load` functions to serialize raw R objects to a file.  The
format is binary, use `dput` and `dump` for ASCII format.

Data Structures
===============

Teetor suggests we study the R Language Definition or R in a Nutshell if we
want to know the technical aspects of R data structures.  His notes are more
informal.

Some key properties of vectors:

* they're homogeneous
* they can be indexed by position or multiple positions (returning a subvector)
* they can have names via `names(vector) <- c("name1", "name2", ...)`
* they can be accessed via names `vector["name1"]`

Lists on the other hand:

* can be heterogeneous
* can also be indexed by position, multiple positions, or names

Instead of types, R has the notion of modes.  A mode for a vector is just the
mode of any one of its elements.  Some modes are numeric, character, list, and
function.  The mode represents how R stores this object in memory.

Every object in R has a class.  While the number 3 is of numeric mode, its class
could be weight or distance or height or time.

    > d <- as.Date("2010-03-15")
    > mode(d)
    [1] "numeric"
    > class(d)
    [1] "Date"

In R, scalars and vectors are the same thing.  A scalar is just a vector
containing one element.

    > length(pi)
    [1] 1
    > pi[1]
    [1] 3.141593
    > pi[2]
    [1] NA

Matrices are vectors with dimensions.

    > A <- 1:6
    > dim(A)
    NULL
    > print(A)
    [1] 1 2 3 4 5 6
    > dim(A) <- c(2, 3)
    > print(A)
         [,1] [,2] [,3]
    [,1]    1    3    4
    [,2]    2    4    6

The vector became a 2x3 matrix after assigning dimensions.  Matrices can be
created from lists too (via the `list` function).  Matrices with more than 2
dimensions are called Arrays, for example `dim(A) <- c(2, 2, 2)`

A factor is a vector of enumerated values.  R uses compact representations for
storage of factors in memory.  Each unique value in a factor is called a
"level".  They're useful for categories and grouping.

A data frame is a tabular data structure with rows and columns implemented by
a list.  Its elements are vectors or factors - the columns of the data frame.
They must all have the same length.  The columns have names.

    > dataframe[i]   # where i is the column number
    > dataframe$name # where name is the column name
    > dataframe[i,j] # for particular cell
    > dataframe[i,]  # ith row
    > dataframe[,j]  # jth column

You probably shouldn't be adding single data items to a vector, as it's bad for
memory unless your data is very small.  If you need to though:

    > v <- c(1,2,3)
    > v[4] <- 4
    > append(v, c(5,6))
    [1] 1 2 3 4 5 6
    > append(v, 0, after=0)
    [1] 0 1 2 3 4 5 6

When you operate on vectors of different lengths, R uses the Recycling Rule.
It repeats the vector of smaller length to fit.

    c(1,2,3,4,5,6) + c(1,2,3) 
    # ...1,2,3 repeats and becomes...
    c(1,2,3,4,5,6) + c(1,2,3,1,2,3)

Turn a vector into a factor with the `factor` function.  It takes an optional
second parameter which contains the full list of possible values (if your first
parameter doesn't include all possible values).

    > f <- factor(c("Win", "Win", "Lose"), c("Win", "Tie", "Lose"))
    > f
    [1] Win Win Lose

The `stack` function is useful for combining multiple vectors.  It works with
lists.  Use the `list()` function to create a list.  Use double brackets to
extract single elements and single brackets to extract sublists.

    > years <- list(1960, 1964, 1976, 1994)
    > years[[1]]
    [1] 1960
    > years[c(1,2)]
    [[1]]
    [1] 1960

    [[2]]
    [1] 1964
    > years <- list(Kennedy=1960, Johnson=1964, Carter=1976, Clinton=1994)
    > years[["Kennedy"]]
    [1] 1960
    > years$Kennedy
    [1] 1960

    > years <- list()
    > years[c("Kennedy", "Johnson", "Carter", "Clinton")] <-
    +   c(1960, 1964, 1976, 1994)
    > years$Kennedy
    [1] 1960

Assign NULL to an element in a list to remove it.  Use the `unlist()` function
to flatten a list into a vector.  To remove all NULL elements, this expression
will remove them:

    > lst[sapply(lst, is.null)] <- NULL

More generically, it removes any elements using an expression:

    > lst[lst < 0] <- NULL

Besides using `dim()`, you can also use the `matrix()` function to create a
new matrix.

    > matrix(vec, rowdim, coldim)
    > matrix(vec, 2, 3)

Some common operations for matrices are:

* `t(A)` to return a matrix transpose of A
* `solve(A)` to return a matrix inverse of A
* `A %*% B` for matrix multiplication
* `diag(n)` for n by n diagonal (identity) matrix

By default, matrix column/row names are numeric indeces.  You can give useful
names instead with the `rownames()` and `colnames()` functions.

    > rownames(mat) <- c("rowname1", "rowname2", ...)
    > colnames(mat) <- c("colname1", "colname2", ...)

Create data frames using the `data.frame()` or `as.data.frame()` functions:

    > dfrm <- data.frame(v1, v2, v3, f1, f2)            # from vectors
    > dfrm <- data.frame(name1=v1, name2=v2, name3=v3)  # with headers
    > dfrm <- as.data.frame(list.of.vectors)            # from list

`rbind` and `cbind` are useful functions to combine row/column vectors into
a data frame.  You can use it to add a row to an existing data frame:

    > newRow <- data.frame(city="San Jose", state="CA")
    > suburbs <- rbind(suburbs, newRow)

If you do this often, you may want to preallocate the memory so your code runs
faster.  Here `n` is the number of rows:

    > suburbs <- data.frame(city=numeric(n), state=numeric(n))

`do.call` constructs and executes a function call from a name or function.  It's
like the splat operator in Ruby or `call` in JavaScript.

Accessing sub data frames and elements in a data frame works just like accessing
elements within lists.  Use `[]` to access a sub-data frame.  Use `[[]]` to
access an actual element.  Use `$` to access via column names.  The `subset()`
function is useful for building subsets of data frames.  `colnames()` and
`rownames()` work just like matrices for setting column/row names.  You can edit
a data frame with a spreadsheet-like interface:

    > temp <- edit(dfrm)
    > dfrm <- temp         # happy with changes
    > fix(dfrm)            # do it in one go, if you're brave

Use `na.omit` to remove rows containing NAs with data frames.  The `subset()`
function can be used to remove columns:

    > subset(dfrm, select=-badboy)  # removes badboy column

Use `with` and `attach` to access data frame contents more easily.  Use `detach`
when you're done.

Some useful functions for converting types:

* `as.character`
* `as.complex`
* `as.numeric` or `as.double`
* `as.integer`
* `as.logical`
* `as.data.frame`
* `as.list`
* `as.matrix`
* `as.vector`

Data Transformations
====================

This chapter is all about the `apply` family of functions: `apply`, `lapply`,
`sapply`, `tapply`, and `mapply`.  Teetor also talks about `by` and `split`.

An important R idiom is using factors to designate groups.  For example:

    > v <- c(40,2,83,28,58)
    > f <- factor(c("A","C","A","B","C"))

The vector and factor can be compared side-by-side, we'll see that group "A"
contains both 40 and 83.  This is called grouping factors.

    > groups <- split(x, f)
    > # ... or ....
    > groups <- unstack(data.frame(x, f))

Use the `lapply` and `sapply` functions to perform a function to each element
on a list.  `lapply` always returns a list while `sapply` returns a vector if
possible.

    > lst <- lapply(lst, fn)

The `apply` function works for matrices.  It can run a function on a row-by-row
basis and return the results in a vector.

    > results <- apply(mat, 1, fn)   # 1 for row-by-row, 2 for col-by-col

The `tapply` function works with groups.  For example, you can compute the sum
or average for each unique group.

    > tapply(vec, factors, fn)

The `by` function works with data frames and groups.  It applies a function to
rows of a data frame, grouping them by factors.  The `mapply` function works
on parallel vectors/lists.  It applies the function element-wise to each
vector:

    > mapply(fn, list1, list2, ..., listn)

Strings and Dates
=================

R has support for strings/dates, but Teetor admits that the tools are clumsy
compared to other string-oriented languages.  In the end, use the right tool
for the right job.  If it's more convenient to use R's built-in support,
use it.

The `Date` class is used to represent a calendar date without a clock time.
`POSIXct` represents datetime and is stored as seconds since epoch. 
`POSIXlt` stores its representation in year, month day, hour, minute, second
elements.  Use `as.Date`, `as.POSIXct`, and `as.POSIXlt` functions to create
each one.  Some useful packages are:

* chron - work with dates without time zones and daylight savings
* lubridate - new general purpose date package
* mondate - specialized for handling dates in units of months, days, years
* timeDate - for demanding accurate datetime handling

Use the `nchar()` function to get the length of a string.  Don't use the
`length()` function, which is meant for vectors/scalars.  Use `paste()` to
concatenate strings together:

    > paste("Everybody", "loves", "stats.", sep=" ")
    [1] "Everybody loves stats."

The `substr()` function lets you extract substrings.

    > substr("Statistics", 1, 4)  # start and end indeces
    [1] "Stat"

`strsplit` splits a string according to a pattern.  `sub(old, new, string)`
lets you substitute substrings.  `gsub` can be used for global substitution.
Read the help page for `regexp` to learn more about regular expressions.

Use `Sys.Date()` to get the current date.  Use `as.Date()` to convert a string
into a date object.  There's an optional `format` option to specify the string
format of the date.  See `help(stftime)` for more information.

Use `format()` or `as.character()` to convert a date object to a string.  If
you have individual year, month, and day - use the `ISOdate(y,m,d)` function.
The `julian` function specifies the number of days since epoch.  To extract
each portion of a date:

    > d <- as.Date("2010-03-15")
    > p <- as.POSIXlt(d)
    > p$mday
    [1] 15
    > p$mon
    [1] 2
    > p$year + 1900
    [1] 2010

There's also `$sec`, `$min`, `$hour`, `$wday`, `$yday`, and `$isdst`.

The `seq` function works with dates also to create a sequence of dates:

    > seq(from=startDate, to=endDate, by=1)
    > seq(from=startDate, by=1, length.out=7)
    > seq(from=startDate, by="month", length.out=12)
    > seq(from=startDate, by="3 months", length.out=4)

Probability
===========

R has short names for each probability distribution.  Functions use a prefixed
letter and the short name, for example the normal distribution is "norm" and
the functions are:

* dnorm for normal density
* pnorm for normal distribution function
* qnorm for normal quantile function
* rnorm to generate normal random variates

For the discrete distributions:

* binom for Binomial
* geom for Geometric
* hyper for Hypergeometric
* nbinom for Negative Binomial
* pois for Poisson

For the continuous distributions:

* beta for Beta
* cauchy for Cauchy
* chisq for Chi-squared
* exp for Exponential
* f for F
* gamma for Gamma
* lnorm for Log-normal
* logis for Logistic
* norm for Normal
* t for Student's t
* unif for Uniform
* weibull for Weibull
* wilcox for Wilcoxon

Use the `choose(n, k)` function to calculate the number of combinations.
Use `combn(items, k)` to generate all possible combinations as a matrix.

The `runif(n)` function is used to generate random uniform numbers between 0
and 1.  `n` is the number of numbers you want.  You can also generate random
variates from other distributions, usually just prefix the letter "r" to the
distribution.  For normal distributions, this is `rnorm(n)`.

You can set the seed with `set.seed()` to get the same set of random numbers
on each run.

If you want to sample a dataset randomly, use the `sample(vec, n)` function.
This will sample without replacement.  Use the `replace=T` option to allow
duplicate sampling.  If you only pass the vector without size, the function will
return a permutation of your vector.

For simple probability P(X = x), use the prefix "d" and the distribution R
name.  For example `dbinom` for the Binomial distribution.  For cumulative
probability P(X <= x), use the prefix "p" - for example `pbinom`.

Suppose we've got a binomial random variable X over 10 trials where each trial
has a probability success of 1/2.  To calculate the probability of observing
x = 7:

    > dbinom(7, size=10, prob=0.5)
    [1] 0.1171875

R calls this the "density function", others call it "probability mass function"
or "probability function".  Use the "distribution function" aka "cumulative
probability function" to determine probability of x <= 7:

    > pbinom(7, size=10, prob=0.5)
    [1] 0.9453125

The complement to the cumulative probability is the "survival function" P(X > x)
which just needs the argument `lower.tail=F`:

    > pbinom(7, size=10, prob=0.5, lower.tail=F)
    [1] 0.0546875

This works for continuous functions also.  Note that you can use vectors as
the first argument to compute multiple probabilities at once.

Given a probability, p, and a distribution, you want to determine the
corresponding quantile for p: the value, x, such that P(X <= x) = p.  A quantile
is sort of an inverse probability.  Every distribution has a prefixed "q"
function to calculate quantiles:

    > qnorm(0.05, mean=100, sd=15)  # the first arg is the probability
    [1] 75.3272
    > pnorm(75.3272, mean=100, sd=15)
    [1] 0.05000003

A common case for computing quantiles is to find the confidence intervals.
For example, we might want to know the 95% confidence interval (alpha=0.05)
of a standard normal variable.  That's the quantiles with probabilities of
alpha/2 and (1-alpha)/2 (0.025 and 0.975):

    > qnorm(c(0.025, 0.975))
    [1] -1.95994 1.959964

The `plot(x, y)` function is a simple plotting function.  You can use it to
look at probability distributions.  See `?plot` for more information about how
to label an axis.

    > x <- seq(from=-3, to=+3, length.out=100)
    > plot(x, dnorm(x))

Usually we add a shaded region to the plot for more information.  This is done
with the `polygon` function call after the `plot` call.

    x <- seq(from=-3, to=+3, length.out=100)
    y <- dnorm(x)
    plot(x, y, main="Standard Normal Distribution", type='l',
         ylab="Density", xlab="Quantile")
    abline(h=0)

    # The body of the polygon follows the density curve where 1 <= z <= 2
    region.x <- x[1 <= x & x <= 2]
    region.y <- y[1 <= x & x <= 2]

    # We add initial and final segments which drop down to the Y axis
    region.x <- c(region.x[1], region.x, tail(region.x,1))
    region.y <- c( 0, region.y, 0)

    polygon(region.x, region.y, density=10)

General Statistics
==================

This chapter is all about statistics with R.  It starts with an introduction
to the null hypotheses, alternative hypotheses, and p-values.  As a quick
reminder:

* the null hypotheses is the hypotheses for nothing happening
* the alternative hypotheses is if something did happen
* the p-value is the probability of a test statistic value as extreme or more
  extreme than the one we observed assuming the null hypotheses is true

If the p-value is too small, than we reject the null hypotheses.  If the p-value
is not small, we fail to reject the null hypotheses.  A common p-value is
`P < 0.05` to reject or a significance level of `alpha = 0.05`.  You may also
want to state confidence intervals instead of doing hypotheses tests.

Use the `summary(data)` function to get useful statistical summary of your data:

    > summary(vec)
      Min. 1st Qu. Median   Mean 3rd Qu.   Max.
    0.1803 1.1090  1.9520 2.1750 2.6920 7.3290

The 1st Qu and 3rd Qu are the first and third quartile.  The median/mean values
let you detect skew.  This works with vectors, matrices, and data frames.

The `mean` function accepts expressions to compute relative frequencies.  For
example, you can determine the relative frequency of positive numbers in a
vector:

    > x <- c(-1, 0, 1, 2, 3)
    > mean(x > 0)  # should be 3/5
    [1] 0.6

`table` is a useful function to tabulate factors.  For example, the initial and
outcome factors can be cross-tabulated:

    > table(initial, outcome)
            outcome
    initial Fail Pass
        Yes   13   24
         No   24   12
      Maybe   10   17

Using `table` and `summary` together can perform a chi-squared test given two
factors.  The output includes a p-value.  A p-value < 0.05 indicates the
variables are likely not independent and a p-value > 0.05 fails to provide
any such evidence.  Or you can use `chisq.test`.

    > summary(table(fac1, fac2))

Use the `quantile(vec, frac)` function to determine the quantile of any given
vector.  Omit the `frac` argument to get the quartiles.  You may alo use a
vector of fractions for the second argument.

Use the `scale(x)` function to convert a dataset to its corresponding z scores.
This is also known as normalizing the data.

The `t.test(x, mu=m)` function will let you know if the mean of a population
could reasonably be a value given a sample.  The output contains a p-value,
a low p-value indicates that the mean is unlikely to be m. 

Use `paired=T` with the `t.test` to compare the means of two samples.  For
example `t.test(x, y, paired=T)`.  A low p-value indicates they are likely
different.

The output of a t.test also includes a confidence interval.  Use the
`conf.level` argument to include other confidence intervals.

To calculate the confidence interval of a median instead of the mean, use
`wilcox.test`:

    > wilcox.test(x, conf.int=T)

If you have two populations with similar distribution and you want to know if
one population is shifted left/right compared to the other, use the
`wilcox.test(x, y, paired=T)`.  A p-value < 0.05 indicates the second population
is shifted left or right.

Use `prop.test(x, n, p)` to test a sample proportion.  For example, say the Cubs
won 11 out of the last 20 games (55%).  Should you be very confident that the
Cubs will win more than 1/2 of their remainder games this year?

    > prop.test(11, 20, 0.5, alternative="greater")
    1-sample proportions test with continuity correction
    data: 11 out of 20, null probability 0.5
    X-squared = 0.05, df = 1, p-value = 0.4115
    alternative hypothesis: true p is greater than 0.5
    95 percent confidence interval:
      0.3496150 1.0000000
    sample estimates:
       p
    0.55

The p-value is 0.4115, too large.  We cannot reject the null hypotheses.  This
function is also useful to form confidence intervals.  Just leave off the `p`
argument.

The `shapiro.test(x)` function tells us if `x` is normally distributed or not.
A p-value < 0.05 indicates that it is not likely normally distributed.

Use the package `tseries` and `runs.test` to determine if a sequence of binary
events is random or not.  A p-value < 0.05 means that the series is likely not
random.

    > library(tseries)
    > runs.test(as.factor(s))

Use `cor.test(x, y)` to determine if the correlation between two variables is
significant.  Use the `method="Spearment"` argument for non-normal populations.
A p-value < 0.05 indicates that it's likely significant.

The Kolmogorov-Smirnov test compares two samples to determine if they were
drawn from the same distribution.

    > ks.test(x, y)

A p-value < 0.05 indicates the two samples were drawn from different
distributions.

Graphics
========

R has high-level graphics functions which initializes new graphs and low-level
graphics function which adds something to an existing graph.  The high-level
functions are:

* plot - generic plotting function
* boxplot - creates a box pot
* hist - creates a histogram
* qqnorm - creates a quantile-quantile (Q-Q) plot
* curve - graph a function

The low-level function sare:

* points - add points
* lines - add lines
* abline - add straight line
* segments - add line segments
* polygon - add a closed polygon
* text - add some text

The do a scatterplot of paired observations, just pass it to the `plot(x,y)`
function.  If your data is in a data frame, pass it to `plot(dataframe)`.  See
`?plot` for more info about its optional arguments:

    > plot(X, main="Title", xlab="X Axis", ylab="Y Axis")
    > # ... or ...
    > plot(x, ann=F)  # without annotations
    > title(main="Title", xlab="X Axis", ylab="Y Axis")

Use the `grid()` function to add a grid to your plot.  Use the option `type="n"`
to initialize a plot without displaying data, then add the data later using
`points(x, y)`.

You can group scatter plots using factors with the `pch` argument.

    > plot(x, y, pch=as.integer(factor))

I think `pch` stands for point character.  It's just a vector of integers, each
integer representing a different character to use.

Use the `legend()` function to add a legend to your plot.  This is especially
useful when using `pch`.

Often times you'll want to plot the regression line of a scatter plot:

    > m <- lm(y ~ x)
    > plot(y ~ x)
    > abline(m)

Using the `plot()` function on a dataframe will create plots for each pair of
columns.  To turn your scatter plot into a line plot, use `type="l"`.  The
`lty` option can be used to change the line type ("solid", "dashed", "dotdash",
"dotted", "blank", "longdash", or "twodash").  Use the `lwd` option to control
the line width and `col` to control the color.

After creating a plot, you can add more data via the `lines` or `points`
functions.  Use `abline` to make horizontal/vertical lines.

You may also want to create a plot for each level in a factor.  This is called
a conditioning plot:

    > coplot(y ~ x | f)

Another plot you can do is bar plots.  Use the `barplot` function and pass in
a vector of data.  Bar plots can include various options for display:

    > barplot(heights,
    +   main="Mean Temp. by Month",
    +   names.arg=c("May", "Jun", "Jul", "Aug", "Sep"), + ylab="Temp (deg. F)")

The `gplots` library includes `barplot2`, which has options for confidence
intervals.

Use `boxplot(x)` to create a box plot:

* the thick line in the middle is the median
* surrounding box identifies the first/third quartiles, bottom is Q1 and top is
  Q3
* the "whiskers" above/below show range of data excluding outliers
* circles identify outliers.  Outliers are values further than 1.5 x IQR away
  from the box where IQR is the interquartile range or Q3 - Q1

Box plots work with factors: `boxplot(x ~ factors)`.

Use the `hist(x)` function to create a histogram.  The second argument to this
function is the number of bins.  You can add a density estimate with a call to
`lines(desntiy(x))`.  A neat way to create a histogram for discrete datasets:

    > plot(table(x), type="h")

Use `qqnorm(x)` to create a basic Q-Q plot.  Use `qqline(x)` to add a diagonal
line to it.  This is typically done to determine if your data is normally
distributed.

Use the `curve` function to graph the value of a function:

    > curve(sin, -3, +3)  # graph sine function from -3 to +3

The `par()` function lets you get and set graphics options.  For example, always
set the line width to 2 with `pwd(lwd=2)`.  Get the current line width option
with `pwd("lwd")`.

The global graphic option `ask` can be set to ask you before overriding plots.
This is useful if you're plotting a lot:

    > par(ask=T)

If you want to display plots side by side, use the `mfrow=(c(N,M))` graphics
option for an NxM matrix:

    > par(mfrow=(c(2,4)))

Or you can open additional graphics windows with `win.graph()`.

Use the `savePlot(filename="filename", type="type")` function to save your
current plot to a file.  Type can be one of "png" or "jpeg".  For batch scripts,
call `png(...)` or `jpeg(...)` before plotting.  then call `dev.off()` when
finished plotting.

Linear Regression and ANOVA
===========================

Useful Tricks
=============

Beyond Basic Numerics and Statistics
====================================

Time Series Analysis
====================

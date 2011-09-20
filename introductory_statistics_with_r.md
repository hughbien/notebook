Introductory Statistics with R
==============================

Peter Dalgaard walks us through an introduction of R targeting the
non-statistician.  The text/code have been updated to R version 2.6.2.

Go to [http://cran.r-project.org] to download the R language.  A good IDE to use
is RStudio and can be found at [http://rstudio.org].

Be sure to install thw `ISwR` package  which stands for Introductory Statistics
with R.  Just run `install.packages("ISwR")`.

Basics
======

From the R console, load the `ISwR` package.  Then try some commands:

    > library(ISwR)
    > 2 + 2
    > exp(-2)
    > rnorm(15)

Assignments use the `<-` operator and assign to **symbolic variables**.  Names
can be built from alphanumeric characters and the period symbol.  Names cannot
start with a digit or period.  Try not to use single letter names because they
might overwrite default functions or variables `c, q, t, C, D, F, I, T`.

    > x <- 2

**Vectors** can be created with the `c` function:

    > weight <- c(60, 72, 57, 90, 95, 72)
    > height <- c(1.75, 1.80, 1.65, 1.90, 1.74, 1.91)
    > bmi <- weight/height^2
    [1] 19.59 22.22 20.93 24.93 31.37 19.73

You can perform operations on vectors.  Usually the vectors have to be the same
length.  If a vector is shorter, it gets recycled.  In the example above, `2`
represents a vector of 1 (**scalar**) and gets recycled so all elements are
divided by 2.  Some other functions:

    > sum(weight)
    > xbar <- sum(weight)/length(weight)
    > stddev <- sqrt(sum((weight - xbar)^2)/(length(weight) - 1))
    > mean(weight)
    > sd(weight)

You can plot vectors or even customize the plotting character (`pch`).

    > plot(height, weight)
    > plot(height, weight, pch=2)

A person's expected BMI should be about 22.5 where `BMI = weight/height^2`.  The
`lines` function will add a line to the current plot.  The first argument is
a vector of x coordinates, the second is a vector of y coordinates.

    > hh <- c(1.65, 1.7, 1.75, 1.8, 1.85, 1.9)
    > lines(hh, 22.5 * hh^2)

Some of the basic aspects of the R language:

* **expressions** are entered and they all return a value (could be NULL)
* expressions work on **objects** which are anything that can be assigned a
  variable in R
* **functions** are commands like `plot(height, weight)` which have
  **actual arguments** and **formal arguments**.  Actual arguments are the ones
  that are used in a call, formal arguments get connected to the actual call
* functions can use named actual arguments or **keyword matching** like
  `plot(x=height,y=weight)`.

Use the `ls()` function to list objects in scope.  Use the `args()` function to
get the formal arguments of a function.  A triple ... indicates that the function
will accept varargs.

    > ls()
    > args(plot)
    function (x, y, ...)

Vectors can contain numbers, characters, boolean, or NA values.  You can
concatenate vectors or create named/value pairs.  Vectors of different types
will be coerced into the least "restrictive" type:

    > c('Huey', 'Dewey', 'Louie')
    > c("Huey", "Dewey", "Louie", "\"\n\"")
    > c(T, T, F, T)  # T == TRUE, F == FALSE
    > c(NA)
    > c(c(1,2,3), c(4,5,6), 7)
    > x <- c(red="Huey", blue="Dewey", green="Louie")
    > names(x)
    > x["red"]
    > c(FALSE, 3)
    [1] 0 3
    > c(pi, "abc")
    [1] "3.14159" "abc"

Long vectors can be created using the `seq` function.  The first argument is
the beginning, the last argument is the end.  The third optional argument is
the increment count.  If the step size is 1, you can use the `:` operator.  Use
the `rep` function to repeat values:

    > seq(4, 9)
    [1] 4 5 6 7 8 9
    > seq(4, 10, 2)
    [1] 4 6 8 10
    > 4:9
    [1] 4 5 6 7 8 9
    > rep(c(1,2), 3)
    [1] 1 2 1 2 1 2
    > rep(c(1,2), c(3,4))
    [1] 1 1 1 2 2 2 2

Matrices can be created using the `dim()` assignment function or the convenient
`matrix()` function.  You can set the headers with `rownames` and `colnames`.
You can transpose with the `t()` function.

    > x <- 1:12
    > dim(x) <- c(3,4)  # 3 rows, 4 columns
    > matrix(1:12, nrow=3, byrow=T)  # byrow means fill rows first
    > rownames(x) <- LETTERS[1:3]    # or letters, month.name, month.abb
    > t(x)

**Factors** are like enums attached to data in Java or C.  They let you
categorize values in experiments such as pain.  To create one, you'll need
numeric values for each **level** and a string description:

    > actualpain <- c(0, 3, 2, 2, 1)
    > fpain <- factor(actualpain, levels=0:3)  # you can also used `ordered`
    > levels(fpain) <- c("none", "mild", "medium", "severe")
    > fpain
    [1] none severe medium medium mild
    Levels: none mild medium severe
    > as.numeric(fpain)
    [1] 1 4 3 3 2
    > levels(fpain)
    [1] "none" "mild" "medium" "severe"

R has a `list` construct which is more like an object with fields in other
languages.  Use the `list()` function to make one and the `$` operator to access
the fields in a list:

    > mylist <- list(before=1, after=c(2,3))
    > mylist
    $before
     [1] 1
    $after
     [1] 2 3
    > names(mylist)
    [1] "before" "after"
    > mylist$before
    > mylist$after
    > ls(mylist)

**Data frames** are made up of vectors of the same length.  The individual
components can be accessed with the `$` operator:

    > frame <- data.frame(height, weight)
    > frame$height
    > frame$weight
    > frame[1][1]  # height at row 1
    > frame[1,]    # all measurements for row 1
    > frame[1:2,]  # first two rows
    > frame[frame$height > 21,]
    > head(frame)  # first six rows
    > tail(frame)  # last six rows

There are a few ways to grab elements in a vector and assign values:

    > height[1]
    > height[1] <- 20
    > height[c(1,2,3)]     # grab multiple elements
    > height[1,2,3]        # grab from matrix
    > height[1:3]
    > height[height > 19 & height < 21]

If `NA` occurs in the indexing vector, then R will return `NA`s.  A useful
function is the `is.na()` function since `x==NA` will return `NA`.

It's natural to store grouped data in a data frame with one vector and have
parallel factors in another.  With R, you can manually extract the data or use
the `split` function.

    > sature <- factor(c(0, 1, 1, 0), levels=0:1)
    > levels(sature) <- c("lean", "obese")
    > expend <- c(7.5, 9.2, 6.4, 8.8)
    > energy <- data.frame(expend, sature)
    > energy
      expend  sature
      1  7.5  lean
      2  9.2  obese
      3  6.4  obese
      4  8.8  lean
    > exp.lean <- energy$expend[energy$sature=="lean"]
    > exp.obese <- energy$expend[energy$sature=="obese"]
    > l <- split(energy$expend, energy$sature)  # l$expend, l$sature

The `fapply` and `sapply` functions let you apply a function to a vector and
collect the results.  `sapply` will try to simplify the results if possible:

    > fapply(vector, fn, na.rm=T)
    > sapply(vector, fn, na.rm=T)

Sorting is done with the `sort` function.  There's also an `order` function which
returns a corresponding vector of indexes.  This vector can be used to sort
other vectors in parallel:

    > sort(vector)
    > order(vector)
    [1] 4 1 3 2

The R Environment
=================

Here are some common functions when dealing with your workspace:

    > ls()           # lists all variables
    > ls(list)       # lists variables in `list`
    > rm(var)        # removes a variable from workspace
    > rm(list=ls())  # removes all variables in workspace
    > save.image()   # saves current workspace to .RData file
    > load(".RData") # loads workspace
    > sink("myfile") # redirects stdout to a file
    > sink()         # re-establishes stdout to REPL

Some useful functions for working with scripts:

    > source("file")         # runs script
    > source("file", echo=T) # runs script and prints commands
    > savehistory("file")    # save REPL history to file
    > loadhistory("file")    # load REPL history
    > history()              # show current history

Some commands to get help or documentation:

    > help.start()       # opens the R manual
    > help(fn)           # print function's documentation
    > ?fn                # same as above
    > apropos("arg")     # finds functions starting with arg
    > help.search("arg") # uses fuzzy matching and deeper search

R packages are centralized on CRAN (Comprehensive R Archive Network) which
currently hosts over 1000 packages.  Installed libraries are just directories
of code on your filesystem.

    > library()                 # currently installed packages
    > install.packages("IsWR")  # installs IsWR package
    > library(IsWR)             # loads package into workspace
    > detach("package:IsWR")    # removes package from workspace
    > help(package=IsWR)        # docs for IsWR 

Packages use **lazy loading** since data sets can get large.  Some packages
still require explicit calls to `data`.  This function goes through data
directories of each package and loads anything present.  If files are present
with the `.tab` extension, it'll use `read.table`.  Other file extensions exist
for special treatment.  You can make R look for variables in a data frame using
the `attach`, `detach`, and `with` functions.

    > data(thuesen) # searches for file with basename thuesen
    > thuesen$blood.glucose
    > thuesen$short.velocity
    > attach(thuesen) # attaches thuesen in system's search path
    > search()
      ... "thuesen" ...
    > blood.glucose
    > short.velocity
    > detach(thuesen)
    > with(thuesen, plot(blood.glucose, short.velocity))

`subset` and `transform` are useful functions to operate on vectors and data
frames.  `subset` will filter out elements/rows, `transform` can be used to add
additional information.  `within` is a more flexible alternative.

    > thue2 <- subset(thuesen, blood.glucose < 7)
    > thue3 <- transform(thuesen, log.gluc=log(blood.glucose))
    > thue4 <- within(thuesen, {
    +   log.gluc <- log(blood.glucose)
    +   m <- mean(log.gluc)
    +   centered.log.gluc <- log.gluc - m
    +   rm(m)
    + })

Let's try plotting something and adding to the plot:

    > x <- runif(50, 0, 2)  # random uniform distribution
    > y <- runif(50, 0, 2)
    > plot(x, y, main="Main title", sub="subtitle", 
    +            xlab="x-label", ylab="y-label")
    > text(0.5, 0.5, "text at (0.6, 0.6)")  # adds text to point
    > abline(h=.6, v=.6)  # adds a horizontal and vertical line
    > # loop through and add margin text
    > for(side in 1:4) mtext(-1:4, side=side, at=.7, line=-1:4)
    > mtext(paste("side", 1:4), side=1:4, line=-1, font=2)

In the example below, we're building a plot from pieces:

    > # type="n" means draw no points, axes=F means draw no axes
    > plot(x, y, type="n", xlab="", ylab="", axes=F)
    > points(x, y)
    > axis(1)
    > axis(2, at=seq(0.2, 1.8, 0.2)  # alternative set of ticks
    > box()
    > title(main="Main title" sub="subtitle", xlab="x-label", ylab="y-label")

`par` is a powerful function that lets you control various settings of the plot.
The author says it's pretty confusing and you should just play around with it
to learn.

Let's plot a histogram and add an overlaying curve:

    > x <- rnorm(100)
    > hist(x, freq=F)         # plot by densities instead of absolute counts
    > curve(dnorm(x), add=T)  # add=T means add to existing plot

The top of the curve is cut off because the y-axis played no role for the curve.
To fix this, we can re-use the same y values for both plots and make the plot
big enough to hold both:

    > h <- hist(x, plot=F)
    > ylim <- range(0, h$density, dnorm(0))
    > hist(x, freq=F, ylim=ylim)
    > curve(dnorm(x), add=T)

You can write your own functions in R:

    functionname <- function(arg1, arg2, arg3=default, ...) {
       # body of function goes here
    }

R also has conditional and looping constructs:

    while (condition) expression
    repeat {
      if (condition) break
    }
    for (variable in vector) expression

In R, objects have a `class` attribute.  Some functions will change depending
on what this attribute is, like the `print` function.

    > print
    function (x, ...)
    UseMethod("print")  # using print on class htest will use print.htest

You can read in a space delimited file with `read.table`.  The `header` option
is a boolean flag for whether or not headers are present.  There's also a
`read.csv` function for CSVs and `read.delim` for tab delimited.  `read.delim`
allows you to specify your own delimiters.  The flip side to reading are the
functions `write.table` and `write.csv`.  Some notes:

* `sep` option to specify the field separator
* `na.strings` option to specify not available data (`"NA"` by default)
* `quote` option to specify which characters are used for quotes
* `comment.char` option to ignore certain lines in data
* `fill` and `flush` option to ignore errors of mismatch line lengths

R lets you edit data frames using a spreadsheet-like interface via `edit`:

    > aq <- edit(airquality)
    > fix(airquality)  # modifies airquality instead of returning new data
    > # start with a new spreadsheet
    > dd <- data.frame()
    > fix(dd)

Probability and Distributions
=============================

R can pick random samples for you:

    > sample(1:40, 5)  # pick five numbers randomly from 1 to 40
    > sample(40, 5)    # same as above
    > sample(c("H", "T"), 10, replace=T)                    # simulate coin
    > sample(c("H", "T"), 10, replace=T, prob=c(0.9, 0.1))  # unfair coin

You can use it to calculate permutations and combinations:

    > prod(40:36)   # 40 * 39 * 38 * 37 * 36
    > choose(40, 5) # choose 40 items into 5 slots

The following functions can be used for normal distributions:

* `dnorm` for density
* `pnorm` for probability
* `qnorm` for quantile
* `rnorm` for random

There are parallel functions for binomial distributions:

* `dbinom` for density
* `pbinom` for probability
* `qbinom` for quantile
* `rbinom` for random

The density for a continuous distribution is a measure of the relative
probability of "getting a value close to x".  It uses `dnorm`.  To plot a bell
curve:

    > x <- seq(-4, 4, 0.1)
    > plot(x, dnorm(x), type="l")     # l for line graph
    > curve(dnorm(x), from=-4, to=4)  # alternative plot

The type of plot "l" is a line graph.  Use "h" for a histogram.

The cumulative distribution function describes the probability of "hitting" x
or less in a given distribution.  It uses `pnorm` or `pbinom`.

Say a biochemical measure in healthy individuals is described by a normal
distribution with a mean of 132 and standard deviation of 13.  What is the
probability a patient has a value of 160?

    > 1 - pnorm(160, mean=132, sd=13)

`pnorm` returns the probability of getting a value smaller than its first arg
in a normal distribution.

The quantile function is the inverse of cumulative distribution function.
There is a probability p of getting a value less than or equal to the p-quantile.
It uses the `qnorm` and `qbinom` functions.

`rnorm` and `rbinom` are used to generate pseudo-random numbers.

Descriptive Statistics and Graphics
===================================

Here's how to obtain the mean, standard deviation, variance, median, and
quantile:

    > x <- rnorm(50)
    > mean(x)
    > sd(x)
    > var(x)
    > median(x)
    > quantile(x)  # defaults to every 25%
    > quantile(x, seq(0, 1, 0.1))  # every 10%

The `juul` data set from the `ISwR` package contains variables from a insulin
study.  Let's do some operations on it:

    > attach(juul)
    > mean(igf1)  # means insulin growth factor 1, returns NA
    > mean(igf1, na.rm=T)
    > sum(!is.na(igf1))  # length function has no na.rm argument
    > summary(igf11)
    > summary(juul)
    > detach(juul)

Let's set the correct types on our variables by setting some factors:

    > juul$sex <- factor(juul$sex, labels=c("M", "F"))
    > juul$menarche <- factor(juul$menarche, labels=c("No", "Yes"))
    > juul$tanner <- factor(juul$tanner,
                            labels=c("I","II","III","IV","V"))
    > attach(juul)
    > summary(juul)  # display changes for factor variables

A shortcut for the factor setting could have been:

    > juul <- transform(juul,
    +   sex=factor(sex,labels=c("M","F"))
    +   menarche=factor(menarche,labels=c("No","yes"))
    +   tanner=factor(tanner,labels=c("I","II","III","IV","V"))
    + )

A histogram gives a reasonable shape of a distribution with the `hist` function.
You can specify cutpoints with `breaks=n` where `n` is the number of bars.

    > mid.age <- c(2.5,7.5,13,16.5,17.5,19,22.5,44.5,70.5)
    > acc.count <- c(28,46,58,20,31,64,149,316,103)
    > age.acc <- rep(mid.age, acc.count)
    > brk <- c(0,5,10,16,17,18,20,25,60,80)
    > hist(age.acc, breaks=brk)

You can also plot the empirical cumulative distribution or change it to a qqplot
with a builtin function:

    > n <- length(x)
    > plot(sort(x), (1:n)/n, type="s", ylim=c(0,1))  # type s is step function
    > qqnorm(x)

A boxplot aka "box-and-whiskers plot" is a graphical summary of a distribution.
The box in the middle are "hinges" and median.  The lines ("whiskers") show the
largest or smallest observation that falls within a distance of 1.5 times the
box size from the nearest hinge.  Other "extreme" values are shown separately:

    > par(mfrow=c(1,2)) # specifies layout of plot
    > boxplot(IgM)
    > boxplot(log(IgM))
    > par(mfrow=c(1,1))

You can get summary statistics within groups, for example a table of means and
standard deviations.  Use the `tapply` function.

    > attach(red.cell.folate)
    > xbar <- tapply(folate, ventilation, mean)  # split according to ventilation
    > s <- tapply(folate, ventilation, sd)
    > n <- tapply(folate, ventilation, length)
    > cbind(mean=xbar, std.dev=s, n=n)

The function `aggregate` is just like `tapply` but works on the entire data
frame.  The function `by` takes an entire (sub-) data frame.

For grouped data, you may want to split it up before plotting.  For example,
let's split up two histograms:

    > attach(energy)
    > expend.lean <- expend[stature=="lean"]
    > expend.obese <- expend[stature=="obese"]
    > par(mfrow=c(2,1))
    > hist(expend.lean,breaks=10,xlim=c(5,13),ylim=c(0,4),col="white")
    > hist(expend.obese,breaks=10,xlim=c(5,13),ylim=c(0,4),col="grey")
    > par(mfrow=c(1,1))
    > boxplot(expend.lean, expend.obese)  # boxplot instead of histogram

A stripchart is useful to show variations:

    > opar <- par(mfrow=c(2,2), mex=0.8, mar=c(3,3,2,1)+.1)
    > stripchart(expend ~ stature)
    > stripchart(expend ~ stature, method="stack")
    > stripchart(expend ~ stature, method="jitter")
    > stripchart(expend ~ stature, method="jitter", jitter=.03)
    > par(opar)

ASCII tables are useful too and can be assembled via `matrix`.

    > caff.marital <- matrix(c(652,1537,598,242,36,46,38,21,218,327,106,67),
                             nrow=3, byrow=T)
    > colnames(caff.marital) <- c("0, "1-500", "151-300", ">300")
    > rownames(caff.marital) <- c("Married", "Prev.married", "Single")
    > names(dimnames(caff.marital)) <- c("marital", "consumption")
    > caff.marital

You can use the `as.data.frame` and `as.table` functions to convert between 
tables and data frames:

    > as.data.frame(as.table(caff.marital))
    > table(sex)
    > table(sex, menarche)
    > table(menarche, tanner)
    > xtab(~ tanner + sex, data=juul)  # same as table, except uses formula
    > ftable(coma + diab ~ dgn, data=stroke)  # creates a flat table
    > t(caff.marital)  # to transpose

For graphical displays of tables, you can try using a `barplot`, `dotchart`, or
`pie`:

    > total.caff < margin.table(caff.marital, 2)
    > barplot(total.caff, col="white")
    > par(mfrow=c(2,2))
    > barplot(caff.marital, col="white")
    > barplot(t(caff.marital), col="white")
    > barplot(t(caff.marital), col="white", beside=T)
    > barplot(prop.table(t(caff.marital),2), col="white", beside=T)
    > par(mfrow=c(1,1))

The `dotchart` looks like a horse race with the big dots representing a horse:

    > dotchart(t(caff.marital), lcolor="black")

The `pie` function is used to make piecharts:

    > opar <- par(mfrow=c(2,2), mex=0.8, mar=c(1,1,2,1))
    > slices <- c("white", "grey80", "grey50", "black")
    > pie(caff.marital["Married",], main="married", col=slices)
    > pie(caff.marital["Prev.married",],
          main="Previously married", col=slices)
    > pie(caff.marital["Single",], main="Single", col=slices)
    > par(opar)


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

Khan Academy: Statistics
========================

My notes form Khan Academy's Statistics videos.  Full videos can be viewed
at <http://www.khanacademy.org>.

Intro and The Average
=====================

In broad terms, statistics is getting your head around data.  There's a few
branches:

* descriptive - find indicative numbers without going over the entire data set
* inferential - using data to make conclusions like from a sample population

We're starting with descriptive statistics.  The average of a set of numbers
is also known as the **central tendency**.  In formal terms, the average could
be one of:

* arithmetic mean (most people are talking about this)
* median
* mode

To calculate the mean, divide the sum by the number of items in the set.

    avg({1, 1, 2, 3, 4}) = (1 + 1 + 2 + 3 + 4) / 5

To find the median, sort the numbers and find the number where there's an equal
amount of numbers to the left and right of it:

    1, 1, 2, 3, 4
         ---

What if it's an even number set?  Just take the middle two numbers and find
their arithmetic mean.

    1, 1, 2, 3, 4, 4
          ____

The mode is the number that occurs most frequently.  For our set, it's the
number 1.

Sample vs Population Mean
=========================

Let's say we wanted to know the average height of all men in America.  The
total population would be ~150 million men.  It would be almost impossible to
get everyone's height.

The best way to get a sense is to take the average of a sample.  Find random
people in random situations so it's not skewed (don't use your basketball team).

    {5, 6, 5.5, 5.75, 6.5}

The above set would be a sample of the population.  Instead of taking the mean
of the entire population, we can just use the mean of the sample.  In statistics,
the population mean is represented by the **greek letter mu**.  The sample mean
is represented by an **x with a line over it**.

The formulas are (unfortunately, working with ascii):

    mu = (Sigma i=1 to N) x (sub i) / N
    -x = (Sigma i=1 to n) x (sub i) / n

Big N means total population.  Small n means just a sample.  It's a convention.

Variance of a Population
========================

The central tendency numbers lose a lot of information.  We don't know if the
numbers in the set are spread or near.  That's what the measure of **dispersion**
is for.

    {2, 2, 3, 3}
    mu = (2 + 2 + 3 + 3)/4 = 2.5

    {0, 0, 5, 5}
    mu = (0 + 0 + 5 + 5)/4 = 2.5

The arithmetic mean for both populations are the same, but the distance of the
numbers in the bottom set are much further from the mean.  You can think of
it as being more dispersed.

Variance is usually represend by the **lowercase greek letter sigma squared**.

    sig^2 = (Sigma i=1 to N) (x (sub i) - mu)^2 / N

`x (sub i) - mu` is just the distance of the number from the mean.  Let's
find the variance for the first set:

    sig^2 = (.5^2 + .5^2 + .5^2 + .5^2) / 4
    sig^2 = .25

Let's find the variance for the second set:

    sig^2 = (2.5^2 + 2.5^2 + 2.5^2 + 2.5^2) / 4
    sig^2 = 6.25

Sample Variance
===============

Variance is represented by the lowercase sigma character.  Sample variance is
usually represented by the **lowercase 's' squared**:

    s^2 = (Sigma i=1 to n) (x (sub i) - -x)^2 / n

It uses lowercase 'n' instead of uppercase 'N'.  It also uses the x with a line
over it to represent the sample mean instead of population mean.

The sample variance will often underestimate the actual population variance.
There's another formula which is usually closer to the population variance and
is sometimes represented by the letter s or s (sub n-1):

    s^2 (sub n-1) = (Sigma i=1 to n) (x (sub i) - -x)^2 / (n - 1)

The difference is that it's divided by a slightly smaller number `n-1`.

Standard Deviation
==================

Standard deviation is the square root of the variance.  It's represented by
the **lowercase sigma** for populations and **lowercase 's'** for samples.

    sig = ((Sigma i=1 to N) (x (sub i) - mu)^2 / N) ^ (1/2)
    s = ((Sigma i=1 to n) (x (sub i) - -x)^2 / n) ^ (1/2)

Let's try calculating it.

    {1, 2, 3, 8, 7}
    mu = 21/5 = 4.2
    sig^2 = ((1-4.2)^2 + (2-4.2)^2 + (3-4.2)^2 + (8-4.2)^2 + (7-4.2)^2) / 5
    sig^2 = 38.8/5 = 7.76
    sig = 2.79

If the set was just a sample, we would divide by 4 (n - 1) instead of 5.  The
standard deviation was just the square root of the variance.

Alternate Variance Formulas
===========================

For the equations below, let SIG represent Sigma i = 1 to N

    sig^2 = SIG (x (sub i) - mu)^2 / N
    sig^2 = SIG (x^2 (sub i) - 2x (sub i) mu + mu^2) / N
    sig^2 = (SIG (x^2 (sub i)) - 2mu * SIG (x (sub i)) + mu^2 * SIG (1)) / N
    sig^2 = (SIG (x^2 (sub i)) - 2mu * SIG (x (sub i)) + mu^2*N) / N
    sig^2 = SIG(x^2 (sub i))/N - 2mu^2 + mu^2
    sig^2 = SIG(x^2 (sub i))/N - mu^2

This is getting close to the **raw score** method, which is a faster way of
calculating the variance.

    sig^2 = SIG(x^2 (sub i))/N - SIG(x (sub i))^2/N^2

The formula above may seem more complicated, but it can be faster because you
don't have to calculate the mean ahead of time.

Introduction to Random Variables
================================

**Random Variable** can take on multiple values like a variable, but it's not
something you usually solve for.  It's usually a capital letter (like capital
X).  It's really a function that maps from the world of random processes to an
actual number.

Let's say we want to quantify: is it going to rain tomorrow?

    X = { 1 if it rains tomorrow
        { 0 doesn't rain tomorrow

X is going to be random - we don't know if it's going to rain tomorrow.  It can
take on either value.

It could also be something more obvious:

    X = number facing up when I roll a fair dice
    X = { 1 if heads
        { 0 if tails

There are two types of random variables: **discrete** or **continuous**.
Discrete is like rolling a dice - you have a countable set of results 1-6.
A continuous variable can take on an infinite set of values - like the amount
of rain tomorrow.  It could be 1.1 inches, 1.2 inches, etc...

Let's use the random variable for number facing up when rolling a dice.  Here's
the probability distribution for it (unfortunately in ascii!):

         |
         |
         |
         |
     1/6 |   |   |   |   |   |   |   |
         |___|___|___|___|___|___|___|___
             1   2   3   4   5   6   7   

The probability distribution is just a bar graph with the results on the x-axis
and the probability on the y-axis.

If X is equal to the number facing up described by the probability function
above, what is `P(X=6)`?  It's just `1/6`.

Probability Density Functions
=============================

Let's give an example of continuous random variables.

    Y = exact amount of rain tomorrow

What's the probability that Y = 2?  Not 2.01, not 1.99, we need exactly 2 inches.
We don't even have tools to tell us if it's exactly 2 inches.

    P(Y = 2) = 0

Instead, for continuous random variables we can think in terms of "almost".

    P(|Y - 2| < .1)
    P(1.9 < Y < 2.1)

Sal draws a line graph and highlights the area underneath the curve between
1.9 and 2.1.  The x-axis represents inches of rain, the y-axis represents the
probability (0 to 1).  The are underneath the curve is the total probability.

    P(1.9 < Y < 2.1) = integral(1.9 to 2.1) f(x) dx

The area underneath the entire curve should be 1, since the graph represents
all possibilities:

    integral(0 to infinity) f(x) dx = 1

From now on, I'm going to abbreviate integral to $(a->b).

Binomial Distribution 1
=======================

Given a fair coin and five flips:

    X = number of heads after five flips

What's the probability that you get no heads?

    P(X = 0) = P(5 tails) = (1/2)^5 = 1/32

What's the probability of getting one heads?

    P(X = 1) = P(HTTTT + THTTT + ...) = 5/32

What's the probability of getting two heads?  There are five flips, two of them
can be chosen as head.  Ordering doesn't matter.

    P(X = 2) = (5*4/2) * 1/32 = 10/32

What's the probability of getting three heads?  We can use the same approach
as above.

    P(X = 3) = (5*4*3/3!) * 1/32 = 10/32

What's the probability of getting four heads?

    P(X = 4) = (5*4*3*2/4!) * 1/32 = 5/32

Finally, what's the probability of getting five heads?

    P(X = 5) = 1/32

Binomial Distribution 2
=======================

The probability distribution below isn't the scale.

    .31 |       |  |
        |       |  |
        |       |  |
    .17 |    |  |  |  |
        |    |  |  |  |
    .03 |_|__|__|__|__|__|_
          0  1  2  3  4  5

The graph above is a particular instance of a
**binomial probability distribution**.  As you get towards continuous random
variables, this graph would appear like the famous bell curve.

A binomial distribution is the distribution of the number of successes in a
sequence of N independent yes/no experiments.  It's also called the Bernoulli
experiment.

It's really important to understand that not all probability distributions
are this normal distribution (in a bell curve form).  Don't assume!

A binomial is just a pair like (n - x) or (n + x).  That's why the formula
for combinations are called binomial coefficients.  It's also why it's called
the binomial distribution.

For the notes below, I'll be using the notation C(n, k) as the combinations
method for binomial coefficients.

Side note, in algebra we learned the binomial theorem.  Here's an example:

    (x + y)^2
    x^2 + xy + y^2
    
The coefficient of each term (in the case above, it's always 1) is known as
the binomial coefficient:

    n = b + c
    a = x^b * y^c  <-- a is the binomial coefficient
    a = C(n,b) = C(n,c)

Binomial Distribution 3
=======================

Let's say we make 6 free throws and the free throw percentage is 30%.

    X = # of shots made

Let's figure out the probability for each outcome:

    P(X = 0) = .7^6
    P(X = 1) = 6 * .3 * .7^5    // multiply by 6 because there are 6 scenarios
    P(X = 2) = (6 * 5 / 2!) * .3^2 * .7^4
    P(X = 3) = (6 * 5 * 4 / 3!) * .3^3 * .7^3
    P(X = 4) = (6 * 5 * 4 * 3 / 4!) * .3^4 * .7^2
    P(X = 5) = (6 * 5 * 4 * 3 * 2 / 5!) * .3^5 * .7^1
    P(X = 6) = .3^6

Binomial Distribution 4
=======================

Sal input the formulas into excel to output a nice graph.  This ascii one
doesn't really do it justice, but here it is!

       |        |
       |     |  |
    .2 |     |  |
       |     |  |  |
       |  |  |  |  |
       |__|__|__|__|__|__:__.__
          0  1  2  3  4  5  6

The x-axis is number of shots made and the y-axis is the probability.

This probability distribution is based on a 30% free throw percentage.  If we
changed it to 50%, we get a nice symmetrical bell curve.  If you change the
free throw percentage to 80%, you'll see the peak shift to the right.

Expected Value E(X)
===================

Let's say our population was: 3, 3, 3, 4, 5.  The population mean would just
be the sum divided by how many numbers we have: 

    (3 + 3 + 3 + 4 + 5) / 5
    (3(3) + 1(4) + 1(5)) / 5
    1/5 ( 3*3 + 1*4 + 1*5 )
    3/5 * 3 + 1/5 * 4 + 1/5 * 5
    60% * 3 + 20% * 4 + 20% * 5

The last expression is different.  We don't know how many numbers there are,
but we're stating the frequencies of the numbers.  60% of the numbers are 3,
20% are 4, and 20% are 5.

Let's set a random variable to the number of heads from 6 tosses of a fair coin:

    X = # of heads from 6 tosses of coin

The population of results is actually infinite.  You can always toss the coin
again to get another result.  The random variable is just a specific result
from some experiments - it's just a sample of an endless population.

The expected value of a random variable is the same thing as the population
mean.  It works with infinite sets - you can't really calculate the average
of infinite sets.

Sal goes back to the spreadsheet he created in the last video and substitutes
a 50/50 percentage to imitate a fair coin toss:

    P(0H) = 1.563%
    P(1H) = 9.375%
    P(2H) = 23.438%
    P(3H) = 31.25%
    P(4H) = 23.438%
    P(5H) = 9.375%
    P(6H) = 1.563%

With this information, you can find the population mean aka the expected value.

    E(X) = 0*1.563% + 1*9.375% + 2*23.438% + 3*31.25% +
           4*23.438% + 5*9.375% + 6*1.563%

We're finding the arithmetic mean (or expected value) of an infinite set.

    E(X) = 3

That's kind of an expected outcome with a 50/50 chance.  If you flip a coin
six times, you'll expect to get three heads the most times.  The expected
value isn't always the most probable value - in this case it is.

Expected Value of Binomial Distribution
=======================================

Let's set some general values:

    X = # of successes with probability p after n trials
    E(X) = n * p

Let's use a more concrete example:

    X = # of baskets made after 10 shots where my shooting pct is 40%
    E(X) = 10 shots * 40% = 4 shots

For binomial distributions, you can view the expected value as the most likely
and probable outcome.

Let's prove that the general case is true for any binomial distributions:

    P(X = k) = C(n, k) P^k (1-P)^(n-k)

The expected value is just a probability weighted sum:

    E(X) = (Sigma k=0 to n) k * C(n, k) * p^k * (1-p)^(n-k)

The first simplification we can make is that when `k = 0`, the entire value is 0.
So we can leave out the initial `k = 0`.

    E(X) = (Sigma k=1 to n) k * n!/(k!(n-k)!) * p^k * (1-p)^(n-k)
    E(X) = (Sigma k=1 to n) n!/((k-1)!(n-k)!) * p^k * (1-p)^(n-k)
    E(X) = np * (Sigma k=1 to n) (n-1)!/((k-1)!(n-k)!) * p^(k-1) * (1-p)^(n-k)

Since our formula is so complicated, let's make some substitutions:

    a = k - 1
    b = n - 1
    b - a = n - k

Now our equation is a bit easier to read:

    E(X) = n * p * (Sigma k=a to b) b! / (a!(b-a)!) * p^a * (1-p)^(b-a)
    E(X) = n * p * (Sigma k=a to b) C(b,a) * p^a * (1-p)^(b-a)
    E(X) = n * p * 1
    E(X) = n * p

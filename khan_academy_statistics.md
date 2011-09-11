Khan Academy: Statistics
========================

My notes form Khan Academy's Statistics videos.  Full videos can be viewed
at <http://www.khanacademy.org>.

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

It's really important to understand that not all probability distributions
are this normal distribution (in a bell curve form).  Don't assume!

A binomial is just a pair like (n - x) or (n + x).  That's why the formula
for permutations are called binomial coefficients.  It's also why it's called
the binomial distribution.

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
changed it to 50%, we get a nice symmetrical bell cruve.  If you change the
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
but we're stating the frequences of the numbers.  60% of the numbers are 3,
20% are 4, and 20% are 5.

Let's set a random variable to the number of heads from 6 tosses of a fair coin:

    X = # of heads from 6 tosses of coin

The population of results is actually infinite.  You can always toss the coin
again to get another result.  The random variable is just a specific result
from some experiments - it's just a sample of an endless population.

The expected value of a random variable is the same thing as the population
mean.  It works with infinite sets - you can't really calculate the average
of inifinite sets.

Sal goes back to the spreadsheet he created in the last video and substitues
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
six times, you'll expect to get three heads the most times.  The expeceted
value isn't always the most probable value - in this case it is.

How to Solve It: Modern Heuristics
==================================

An Evolutionary Approach
========================

So far, algorithms found a single solution and used that as a stepping stone
to create a better one.  What would happen if our search algorithm worked with
a population of solutions instead?  The concept of competition among the
solutions is what's important, simulating evolution.

You'll have to come up with a way to combine solutions, like two parents
creating an offspring.  The offspring can be blend of the two parents - but
can also have a random mutation or crossover.

Each generation will compete to belong to the next.  Don't be constrained by
biology - a generation can even compete with its parents/ancestors.

This chapter gives evolutionary approaches to:

1. SAT - satisfiability problem
2. TSP - traveling salesman problem
3. NLP - nonlinear programming problem

A SAT problem looks like this:

    F(x) = (x1 OR x2) AND (x3 OR x4) ... AND (xn-1 OR xn)

And you have to find the vector `x(i), i=1..n` for which `F(x) = 1`.

A good initialization for this algorithm would be to randomly populate the
vector multiple times, perhaps with each value of `x` having a 50/50 chance
of being a 0 or 1.

We can't use `F(x)` as the **evaluation function**, because the evaluation
function would be binary.  Instead, we can break up the sub-expressions.
The more `1`'s we get, the better evaluation score.

Once we have the evaluation function, we need to consider how to select the
parents of the next generation.  You could select in proportion to each
individual's relative fitness, this is called **proportional selection** or
**roulette wheel selection**.  For example, we have 30 slots in the wheel and
we spin 30 times.  The odds to get a single selection is its proportion relative
to the total score.  So a `1` will have a `1/(sum of all scores)` chance on each
turn.  Some selections will show up more than once due to chance.

Another selection procedure is **tournament selection**.  Randomly pair off
parents, the contestant with the highest score wins.  Since it's randomly
paired, the best solution for each step may not even be picked at all.  Some
solutions may be picked twice.

Next, **random variation** needs to be included to create the next population.
We could have a 10% chance that the parent proceeds unchanged into the next
generation, otherwise it will be cut/spliced together with another parent.

The five steps become:

1. representation
2. initial population
3. evaluation function
4. selection procedure
5. random variation

The author walks us through the traveling salesman problem using an evolutionary
approach.  The cities are represented by numbers and the route by a vector of
those numbers.  Permutations represent different routes.

The evaluation function is simple for TSP - just use the length.  The longer the
length the worse the route.

Random variation is a bit more difficult.  The solutions are required to meet
the criteria that the salesman cannot visit the same city twice.  From two
parents, we could select the initial point from one and the remaining route from
the other.  We'd have to make sure cities aren't duplicated.  The author
suggests placing the parent in competition with their offspring to get the best
solution.

For NLP, the representation is pretty easy - just a vector of floating point
numbers.  Each number represents a parameter.  To generate offspring, you could
use the Gaussian random variable which requires a mean and variance.  The author
uses the standard 0 and 1.  The offspring usually looks like its parent, but
sometimes deviates.  The evaluation function is simply a mathematical function,
if the solution falls out of the highly probable space it gets a bad score.

In summary, the general algorithm for evolutionary approaches is:

    t <- 0
    initialize P(t)
    evaluate P(t)
    while (not termination-condition) do
      t <- t + 1
      select P(t) from P(t - 1)
      alter P(t)
      evaluate P(t)
    end

Designing Evolutionary Algorithms
=================================

When designing evolutionary algorithms, you'll have to utilize specific
knowledge about the problem space.

The first thing to consider is how to represent solutions with data structures
we can work with.  For example, the TSP we used a vector of numbers representing
cities.

Some data structures to use as representations:

* fixed-length vectors of symbols
* permutations
* finite state machines
* symbolic expressions

When determining an evaluation function, remember that the optimum solution(s)
should be given the optimum evaluation(s).  In practice, it's difficult to come
up with a perfect evaluation function.  A good feature is that small variations
should engender the resulting evaluation.

Selection methods can be stochastic or deterministic.  Deterministic selections
will always eliminate the same individuals given the same population, it's also
usually faster.  Usually, `mu` represents the number of parents and `lambda`
represents the number of offspring.  An example deterministic is choosing the
top-N of the generation.  An example of stochastic is random tournaments.

The straightforward approach for initialization is randomly fill given the
possible space.  The author recommends biasing the initialization towards the
direction of better solutions to save time (another reason why you need to have
deep knowledge of the problem space).  Use a grid pattern so random
initialization doesn't produce clusters.

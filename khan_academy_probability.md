Khan Academy: Probability
=========================

My notes form Khan Academy's Probability videos.  Full videos can be viewed
at <http://www.khanacademy.org>.

Basic Probability
=================

Let's say we have a "fair coin" - a coin that's equally likely to land on either
side.  If we flip it, what is the probability that it lands with heads facing
up?  You can write the function out like this:

    P(H) = ?

A basic probability equation that fits in this case is:

        # of possibilities that meet criteria
    P = -------------------------------------
          # of equally likely probabilities

For the coin it's:

    P = (1/2) = 50%

Another way to think about it is to run an **experiment** at a large scale.
Flipping the coin millions of times, what percentage of the results would be
heads?  The larger the scale, the closer you'll get to 50%.

Probability with Playing Cards and Venn Diagrams
================================================

The following examples use a deck of playing cards - assume there's no joker
card.  The deck of cards have properties:

* 4 suits: spades, clubs, diamonds, hearts
* Each suit has 13 types of cards: A, 2, 3, 4, 5, 6, 7, 8, 9, 10, J, Q, K
* 52 cards total

If you shuffle the deck and pick a card randomly, what's the probability that
the card is a jack?

            4 jacks in deck
    P(J) = ---------------- = 1/13
           52 possibilities

What's the probability that it's a heart?

           13 hearts in deck
    P(H) = ----------------- = 1/4
           52 probabilities

What's the probability that it's a jack of hearts?  Well, there's only one card
in the deck that satisfies the condition.

    P(J and H) = 1/52

What's the probability that it's a jack OR a heart?  Khan draws a venn diagram
so we can understand the question better.  There are 4 possible jacks and 13
possible hearts.  Of the 13 hearts, there is one overlap which is both a jack
and a heart.  You can't just add 4 + 13, you have to take the overlap into
account.

    P(J or H) = (4 + 13 - 1)/52 = 4/13

Addition Rule for Probability
=============================

Let's say there's a bag with:

* 8 green cubes
* 9 green spheres
* 5 yellow cubes
* 7 yellow spheres

Shake the bag and pull a random item out.  What's the probability that it's a
cube?

              13 possible cubes
    P(cube) = ----------------- = 13/29
              29 total possible

What's the probability of getting a yellow item?

    P(yellow) = (5 + 7) / 29 = 12/29

What's the probability of getting a yellow cube?

    P(cube and yellow) = 5/29

What's the probability of getting a yellow item or a cube?  There are 12 yellow
items and 13 cubes.  But we can't just add the two numbers because there's an
overlap 5 yellow cubes.

    P(cube or yellow) = (12 + 13 - 5) / 29 = 20/29

What's interesting is the relationship between all of these probabilities:

    P(cube or yellow) = P(cube) + P(yellow) - P(cube and yellow)
    20/29 = 13/29 + 12/29 - 5/29

We can generalize this rule:

    P(A or B) = P(A) + P(B) - P(A and B)

This will account for the overlap so you don't double count it.  This rule is
called the **addition rule for probability**.

When there is no overlap, P(A and B) = 0.  If this is the case, the sets are
said to be **mutually exclusive**.

Compound Probability of Independent Events
==========================================

We'll try finding the probability of a sequence of events.  If you flip a coin
twice, what's the probabily of getting heads twice?  You can think of all the
different possibilities:

    HH
    HT
    TH
    TT

There are four distinct equally likely outcomes.

    P(HH) = 1/4

These are independent events - what happens in the first flip does not
affect the second flip.  This is called the **gambler's fallacy**, where people
assume there are better odds getting a tail after many heads.

To figure out the probability of a sequence of independent events, just multiply
the proabilities of each individual event:

    P(HH) = P(H) * P(H) = 1/2 * 1/2 = 1/4

Getting at Least One Heads
==========================

If you flip a coin three times, what's the probability of getting at least one
head?

There are 8 possibilties, we can figure that out because there are 2
possibilities per flip:

    8 = 2 * 2 * 2

Instead of writing out all the possible combinations, reframe the question:

    P(at least one H) = P(not getting all tails)
    P(at least one H) = 1 - P(TTT)
    P(at least one H) = 1 - (1/2 * 1/2 * 1/2)
    P(at least one H) = 7/8

Frequency Probability and Unfair Coins
======================================

A quick review:

           # of events that satisfy A
    P(A) = --------------------------
           # of equally likely events

What about non-equally likely events?  We'll use the example of an unfair coin.
There's more than a 50% chance of getting heads.

    P(H) = 60% = 0.6 = 6/10
    P(T) = 40% = 0.4 = 4/10

What's the probability of getting heads on two flips?

    P(HH) = P(H) * P(H) = 0.6 * 0.6 = 0.36

What about T1, H2, and T3?

    P(T1 H2 T3) = 0.4 * 0.6 * 0.4 = 0.096

Getting Exactly Two Heads (Combinatorics)
=========================================

Flipping a fair coin four times, what's the probability of getting exactly
one heads?

    P(exactly 1 "heads")

There are four flips, with 2 possible results on each flip:

    _ _ _ _
    2*2*2*2 = 16 total possibilities

We could use the addition rule:

    P(exactly 1 "heads") = P(HTTT) + P(THTT) + P(TTHT) + P(TTTH) = 4/16

What's the probability of getting exactly two heads?

         _ _ _ _
    Flip 1 2 3 4

Thinking about it generally, we need two heads where order doesn't matter:

    Ha Hb T T
    Hb Ha T T

Ha is the first head and Hb is the second head.  The two events listed above
are identical for our purposes.

Ha has four places it can show up in.  If the first head occupies one place,
the second heads can only be in three different places.

    Ha  Hb
     4 * 3 = 12 scenarios

But Ha and Hb are interchangable, ordering doesn't matter.  So there are
actually only 6 different scenarios (12 divided by 2).

    P(exactly 2 "heads") = 6/16

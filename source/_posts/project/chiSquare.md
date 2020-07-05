---
title: Chi Square
date: 2020-07-05 20:56:13
tags: 
- R
categories: 
- Project
intro: "Using Chi Square in R"
comments: false
---
Calculating chi square
----------------------
$\frac{1}{2}$

```r
    load( "data/randomness.Rdata" )
    head(cards)

    ##      id choice_1 choice_2
    ## 1 subj1   spades    clubs
    ## 2 subj2 diamonds    clubs
    ## 3 subj3   hearts    clubs
    ## 4 subj4   spades    clubs
    ## 5 subj5   hearts   spades
    ## 6 subj6    clubs   hearts

    observed <- table(cards$choice_1)

    p <- c(clubs = .25, diamonds = .25, hearts = .25, spades = .25)
    p

    ##    clubs diamonds   hearts   spades 
    ##     0.25     0.25     0.25     0.25

    #since there is total 200 participants
    N <- 200
    expected <- N * p
    expected

    ##    clubs diamonds   hearts   spades 
    ##       50       50       50       50

```
Recall that formula for chi square statistics is

$$\sum\frac{(O_i - E_i)^2}{E_i}$$

where O is the observed frequency of each group and E is the expected
frequency.
```r
    sum((observed - expected)^2 / expected)

    ## [1] 8.44
```

we want the value of distribution of chi square at p &lt;= .05. Also, since we have 4 different groups, degrees of freedom is *k* − 1 which is
equals to 3.

    #hypothesis
    qchisq(p=.95, df = 3)

    ## [1] 7.814728

since our chi square statistic (8.44) is bigger than 7.81, we will reject the null hypothesis

If we wanted the exact P-value, we can do

    pchisq(q=8.44, df = 3, lower.tail=FALSE)

    ## [1] 0.03774185

Testing for independence
========================

    load("data/chapek9.Rdata")
    head(chapek9)

    ##   species choice
    ## 1   robot flower
    ## 2   human   data
    ## 3   human   data
    ## 4   human   data
    ## 5   robot   data
    ## 6   human flower

using xtabs function which works on data frame, we can break down the frequencies of each choice by different species

    frequencies <- xtabs(~ choice + species, data = chapek9)
    frequencies

    ##         species
    ## choice   robot human
    ##   puppy     13    15
    ##   flower    30    13
    ##   data      44    65

Null hypothesis: choice of items is independent of species.

Alternative hypothesis: choice of items is not independent of species

We can use the summary function to calculate the expected frequency.
Expected frequency is given by
*P*(*r**o**w*) \* *P*(*c**o**l**u**m**n*)

    summary(chapek9)

    ##   species      choice   
    ##  robot:87   puppy : 28  
    ##  human:93   flower: 43  
    ##             data  :109

    robotpuppy <- 28/180 * 87/180
    robotflower <- 43/180 * 87/180
    robotdata <- 109/180 * 87/180

    humanpuppy <- 28/180 * 93/180
    humanflower <- 43/180 * 93/180
    humandata <- 109/180 * 93/180

Similar as previous example, we can get the sum of the result of each cell using the sum function. First we place the probabilities into a vector first. then we will take this vector and compare it with the object “frequencies” we created. We can then use the sum function to calculate the chi square statistic

    p <- c(robotpuppy, robotflower, robotdata, humanpuppy, humanflower, humandata)

    expected <- 180 * p

    sum((frequencies - expected)^2/expected)

    ## [1] 10.72157

Recall that the degrees of freedom for test of independence is given by:
degrees of freedom = (number of rows − 1)(number of columns − 1)

    pchisq(q=10.72157, df=(3-1)*(2-1), lower.tail = FALSE)

    ## [1] 0.004697217

Since p &lt; .05, we can reject the null hypothesis that the items
chosens and species type is independent of one another.

Effect size
-----------

2 types of effect size phi statistic (used for 2x2 contingency table):
$$\\phi = \\sqrt\\frac{X^2}{N}$$
Using our first example where *X*<sup>2</sup> = 8.44 and N = 200,

    # phi coefficient
    sqrt(8.44/200)

    ## [1] 0.2054264

Cramer’s V (used for a larger contingency table):
$$\\text{Cramer's V} = \\sqrt\\frac{X^2}{N \* (k-1)}$$
, where *k* = *m**i**n*(number of rows, number of columns).

Using our second example where *X*<sup>2</sup> = 10.72 and *N* = 180:

    sqrt(10.72/180)

    ## [1] 0.2440401

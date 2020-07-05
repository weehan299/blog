---
title: Chi Square
date: 2020-07-05 20:56:13
tags: 
- R
- SpssToR
categories: 
- Project
intro: "Applying Chi Square statistic in R"
comments: false
---

These notes are posted here for my own easy navigation or for any one who is interested in learning R. Most of these examples are not my own, although i may inject some of my own code into it for more elaboration. The examples below are from the textbook "Learning Statistics with R" by [Danielle Navarro](https://djnavarro.net/) which i found to be a very helpful resource for those who wants to transition from SPSS to R. The codes are similar to the textbook with the exception when the author uses her own R package ([lsr](https://cran.r-project.org/web/packages/lsr/index.html)) to demonstrate the example, I would try to figure out the way to implement the test without using the package. 

The datasets that I referenced below can be found [here](https://learningstatisticswithr.com/data.zip)



## Basics of calculating chi square statistic

The dataset used here called "randomness" in the dataset folder. It shows each subjects choosing a shape out of 4 different types of shapes (hearts, club, spades, diamonds). This example will only be focusing on the first choice (choice\_1).

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
```

We can get a descriptive statistic of each of the item choice by converting choice_1 into a table. As we can see from below, 35 people chose clubs, 51 chose diamonds etc..
```r
    observed <- table(cards$choice_1)
    ##    clubs diamonds   hearts   spades 
    ##       35       51       64       50

```
What we want to know when doing chi square is whether people's choice of the shapes are actually random. If it was random, we would see that the number of people choosing each shape will be similar. 
**Null hypothesis**: There is no preference of either shape. So expected frequency should be .25 for each of the shapes. 

**Alternative hypothesis**: There is a preference for one of the shape. Thus expected frequency is not .25 for all the shapes

We will then test how likely is the results that we have gotten given that the null hypothesis is true. To set up the expected frequency for null hypothesis, we will create a vector containing the probability of choosing each shape ($P(\text{any shape}) = 0.25$). Since there are 200 participants in the study (can check from the dataset), we will multiply each element of the vector by 200. 
```r
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

$$\sum\frac{(O\_i - E\_i)^2}{E\_i}$$

where $O\_i$ is the observed frequency of each group and $E\_i$ is the expected frequency. The actual Chi Square statistic can be calculated easily in R since we already have our expected frequency and observed frequency of each element which is stored in a vector. 
```r
    sum((observed - expected)^2 / expected)

    ## [1] 8.44
```

Now we want to find out how likely is it that we get the test statistic that we have given that the choice is random. For chi-square test we will be using the chi-square distribution to test the result with. We want the value of distribution of chi square at p &lt;= .05. Also, since we have 4 different groups, degrees of freedom is *k* − 1 which is equals to 3. Thus we can call the R function for chi square distribution. Below shows the critical value when p = .05. 

```r
    #hypothesis
    qchisq(p=.95, df = 3)

    ## [1] 7.814728
```

since our chi square statistic (8.44) is bigger than 7.81, we will reject the null hypothesis that there is no preference for any of the shapes. 

Note: if we wanted the exact P-value, we can do: 

```r
    pchisq(q=8.44, df = 3, lower.tail=FALSE)

    ## [1] 0.03774185

```
Since .03 < .05, we can also reject the null hypothesis. 

Testing for independence
========================

Testing for independence is another way we can use Chi-Square statistic. Dataset contains to variable, Species and Choice. There are 2 different species, robot and human. There are 3 different choices flower, data and puppy. So the dataset below can be conceptualised as a 2x3 contingency table. In this example, we are trying to see if the choice of items each participant picked is independent of its species, i.e there is no association between choice and species.   
```r
    load("data/chapek9.Rdata")
    head(chapek9)

    ##   species choice
    ## 1   robot flower
    ## 2   human   data
    ## 3   human   data
    ## 4   human   data
    ## 5   robot   data
    ## 6   human flower

```
Uing xtabs function which works on data frame, we can break down the frequencies of each choice by different species.

```r
    frequencies <- xtabs(~ choice + species, data = chapek9)
    frequencies

    ##         species
    ## choice   robot human
    ##   puppy     13    15
    ##   flower    30    13
    ##   data      44    65

```
**Null hypothesis**: choice of items is independent of species.

**Alternative hypothesis**: choice of items is not independent of species

We can use the summary function to calculate the expected frequency. Recall that the Expected frequency of a given cell is given by
$$P(row) * P(column)$$
We can thus calculate the value of expected frequency of each cell with the summary data
```r
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

```
Similar to previous example, we can get the sum of the result of each cell using the sum function. First we place the probabilities into a vector first. then we will take this vector and compare it with the object “frequencies” we created. We can then use the sum function to calculate the chi square statistic

```r
    p <- c(robotpuppy, robotflower, robotdata, humanpuppy, humanflower, humandata)

    expected <- 180 * p

    sum((frequencies - expected)^2/expected)

    ## [1] 10.72157

```
Recall that the degrees of freedom for test of independence is given by:
degrees of freedom = (number of rows − 1)(number of columns − 1)

```r
    pchisq(q=10.72157, df=(3-1)*(2-1), lower.tail = FALSE)

    ## [1] 0.004697217

```
Since p &lt; .05, we can reject the null hypothesis that the items
chosens and species type is independent of one another.

Effect size
-----------

2 types of effect size:
1) **phi statistic**(used for 2x2 contingency table):
$$\\phi = \\sqrt\\frac{X^2}{N}$$
Using our first example where *X*<sup>2</sup> = 8.44 and N = 200,

```r
    # phi coefficient
    sqrt(8.44/200)

    ## [1] 0.2054264
```
2) **Cramer’s V** (used for a larger contingency table):

$$\\text{Cramer's V} = \\sqrt\\frac{X^2}{N \* (k-1)}$$
, where $k = min(\text{number of rows}, \text{number of columns})$.

Using our second example where *X*<sup>2</sup> = 10.72 and *N* = 180, and k for a 2x3 contingency table is 1:

```r
    sqrt(10.72/(180*1))

    ## [1] 0.2440401
```

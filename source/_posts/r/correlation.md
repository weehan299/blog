---
title: Correlation
date: 2020-07-05 17:01:47
intro: Applying correlation in R
tags:
- R
- SpssToR
categories:
- r
---
## Calling the correlation test function

Correlation in R is quite straight forward. As there is already a specified function for you to call to measure the correlation. So on top of showing correlation here, this post will also show how to filter out outliers from the data, using the tidyverse package. This is a dataset that I picked out from one of my modules. Questions associated with the data are not shown here so I am guessing that's fine. 

The data is in an excel document, so to open it, we would need any package that does this, I will be using readxl for the job. 
``` r
library(tidyverse)
library(readxl)
corr_data <- readxl::read_xlsx("section c.xlsx")

head(corr_data)

    ## # A tibble: 6 x 4
    ##       P    ES     V     S
    ##   <dbl> <dbl> <dbl> <dbl>
    ## 1     1    16     8     5
    ## 2     2    19     8     6
    ## 3     3    15     7     7
    ## 4     4    15     8     6
    ## 5     5    14     6     5
    ## 6     6    15     8     6
```
To find out the correlation between any of the variables all we need to do is call the "cor.test" function that is inherent in base R. For instance to determine whether variable V and ES are potentially measuring the same construct, we would see whether their correlation is strong or weak. 
``` r
#is V and ES measuring the same thing?
cor.test(corr_data$V, corr_data$ES)

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  corr_data$V and corr_data$ES
    ## t = 4.3508, df = 18, p-value = 0.0003852
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.4002302 0.8797465
    ## sample estimates:
    ##       cor 
    ## 0.7159491
```
According to the results, it shows that there is a strong correlation of .716, which is also significant. Thus there might be some evidence that V and ES are measuring the same construct. 

## Filtering out results
Suppose we would like to filter out results that are too extreme. We would classify extreme as any result that is 2.5 s.d. away from the mean. First we need to create new variables that is the standardised version of each of the original variables, I will call these variables standardise\_X, where X is the variable name. If this was done in SPSS, it we will use Transform > Compute Variables. In R, we can use the "mutate" function from tidyverse to create the new variables.

After that, all we need to do is to filter the variables with its standardised scores being less than 2.5. We can do this using the filter function from tidyverse.
``` r
# filtering out outliers i.e. standardised scores > 2.5
sd_corr_data<- corr_data %>% mutate(
            standardised_P = (P - mean(P))/ sd(P),
            standardised_ES = (ES - mean(ES))/ sd(ES),
            standardised_V = (V - mean(V))/ sd(V),
            standardised_S = (S - mean(S))/ sd(S),
            )


sd_corr_data %>% filter(standardised_ES < 2.5 &
                          standardised_P < 2.5 &
                          standardised_S < 2.5  &
                          standardised_V < 2.5)

    ## # A tibble: 19 x 8
    ##        P    ES     V     S standardised_P standardised_ES
    ##    <dbl> <dbl> <dbl> <dbl>          <dbl>           <dbl>
    ##  1     1    16     8     5        -1.61             0.256
    ##  2     2    19     8     6        -1.44             1.02 
    ##  3     3    15     7     7        -1.27             0    
    ##  4     4    15     8     6        -1.10             0    
    ##  5     5    14     6     5        -0.930           -0.256
    ##  6     6    15     8     6        -0.761            0    
    ##  7     7    13     7     6        -0.592           -0.512
    ##  8     8    12     8     6        -0.423           -0.768
    ## # ... with 2 more variables: standardised_V <dbl>,
    ## #   standardised_S <dbl>
```

As you can see, initially, the has 20 rows, after filtering, there is only 19 rows left which means that one row has been filtered out because one of its varibale is too extreme. We can recompute the correlation, without this outlier to get a score that is less skewed.

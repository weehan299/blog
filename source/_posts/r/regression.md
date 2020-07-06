---
title: Regression
date: 2020-07-06 08:32:39
intro: Applying Regression in R
tags:
- R
- SpssToR
categories:
- r
---
## Regression Model

Recall that a regression model is given by: 
$$\hat{Y}\_i = \beta\_0 + \beta\_1X\_{1i} +\beta\_2X\_{2i} + ... +\beta\_nX\_{ni} + \epsilon\_i$$, where $\beta\_0$ represents the intecept, $\beta\_i$ represents your model parameters and $\epsilon$ represents the error in your model. $X\_{ji}$ and $\hat{Y\_{i}}$ are your experiment variables and experimental values respectively. What you are trying to do in a linear regression is to find a model that using the experimental variables that best fit the experimental values we have. We usually calculate this using by minimising the *sum of squares errors*. More on that later.

### Calling the regression function in R
The linear regression function is R is lm(), which stands for linear model. It accepts primarily 2 parameters: 
  1. A formula: is written in this form: outcome variable ~ predictor variables
  2. Your dataset

In this example, we will be using the trees dataset found in R datasets package. There are 3 variables in this data, Girth, Height and Volume

```r
data(trees)
str(trees)
	## 'data.frame':    31 obs. of  3 variables:
	##  $ Girth : num  8.3 8.6 8.8 10.5 10.7 10.8 11 11 11.1 11.2 ...
	##  $ Height: num  70 65 63 72 81 83 66 75 80 75 ...
	##  $ Volume: num  10.3 10.3 10.2 16.4 18.8 19.7 15.6 18.2 22.6 19.9 ...
```
We can first construct a simple linear regression to see if girth of the tree predicts the volume of the tree. We can do this by calling the lm() function: 
```r
model <- lm(formula = Volume ~ Girth, data = trees)
summary(model)
	## 
	## Call:
	## lm(formula = Volume ~ Girth, data = trees)
	## 
	## Residuals:
	##    Min     1Q Median     3Q    Max 
	## -8.065 -3.107  0.152  3.495  9.587 
	## 
	## Coefficients:
	##             Estimate Std. Error t value Pr(>|t|)    
	## (Intercept) -36.9435     3.3651  -10.98 7.62e-12 ***
	## Girth         5.0659     0.2474   20.48  < 2e-16 ***
	## ---
	## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
	## 
	## Residual standard error: 4.252 on 29 degrees of freedom
	## Multiple R-squared:  0.9353, Adjusted R-squared:  0.9331 
	## F-statistic: 419.4 on 1 and 29 DF,  p-value: < 2.2e-16
```

Looking at the Estimate of the result, we can see that for every unit increase of girth of the tree, the volume of the tree increases by 5.0659 units. 

t value for the Girth tells you whether this estimated coefficient is significant or not as it shows you how many standard deviation is the coefficient away from the null hypothesis value (which is 0 in this case). 

Pr(>|t|) is just your p value that tells you if the result you get is significant.

### Estimating the model
In general, we can calculate the total error in a mdoel by squaring differences between observed values of the outcome and predicted values that come from the model.
$$\text{total error} = \sum^n\_{i=1} (\text{observed}\_i - \text{model}\_i)^2$$

However, we have to first compare whether our model is better at prediction than just random chance. So we need to compare our model with some kind of baseline to see how much better it does in predicting the outcome. This can be done by using the mean of the experimental values as our baseline model. i.e. If our model is pure noise, the values we predict will be no better than just using the mean of the resuls. Using the formula above we can then calculating the total error of this baseline model using:

$$SS\_T = (Y\_i-M\_Y)^2$$, where Y is your experimental values and $M\_Y$ is the mean. This is known as **total sum of squares ($SS\_T$)**.

We can also calculate the total error our own regression model using the same formula, but this time we use our own predicted Y value ($\hat{Y}$) for comparison with the actual experiental values.

$$SS\_T = (Y - \hat{Y})^2$$, this is also known as your **residual sum of squares ($SS\_{R}$)**.

Using the regression results above, we can see use the intercept and the slope of the regression to form a regression equation in R. Y.pred in our example will be the predicted value of the regression equation for each X in the dataset. We can then use this regression equation to calculate $SS\_T$ and $SS\_R$ using the same formula above.
```r
X <- trees$Girth
Y <- trees$Volume
Y.pred <- 5.0659 * X - 36.9435

SS.resid <- sum((Y - Y.pred)^2)
SS.resid
	## [1] 524.3025


SS.tot <- sum((Y - mean(Y))^2)
SS.tot
	## [1] 8106.084
```
#### Calculating $R^2$
A useful tool that we can calculate from sum of squares is the coefficient of determination ($R^2$). It measures the proportion of variance that is accounted for by your regression equation. The higher the $R^2$ value, the greater the proportion of variance that is accounted for. This can be calculated with the given formula.
$$ R^2  
 = \frac{SS\_M}{SS\_T}
 = 1 - \frac{SS\_R}{SS\_T}$$, where $SS\_M = SS\_T - SS\_R$

 Doing it in R we will get: 
 ```r
 R.squared <- 1 - (SS.resid/SS.tot)
 R.squared
	 ## [1] 0.9353199
```
Recall that $R^2$ can also be derived from the correlation square of the variables. Doing it in R yields the same value.
```r
cor(X,Y)^2
	## [1] 0.9353199
```

---
title: "Meta Analysis in R"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(
	echo = TRUE,
	message = FALSE,
	warning = FALSE
)
```
Here is an example of using the metafor package to conduct meta analyses in R.  First, we create an artificial data set containing means, sd's, and n's from both treatment and control groups across 15 studies.  We set each of these values equal to their corresponding values in the escalc function as displayed below.  We set append to TRUE to stack the 15 values for each of the parameters into six variables.  The data (i.e. dat in the example below) also contains two possible moderators cat, which is a generic categorical variable with a 1 and 0, and a contin variable which is a generic continuous indicator.  Finally, the escalc package produces two important values yi and vi.  yi is the differenced effect for each study divided by the pooled standard deviation (i.e. Cohen's D), which we indicated by selecting the "SMD" measure and vi is the variance associated with that effect size.
```{r, message=FALSE, warning=FALSE}
library(metafor)
set.seed(12345)
datExample = cbind(mean1 = rnorm(15), mean2 = rnorm(15), sd1 = abs(rnorm(15)), sd2 = abs(rnorm(15)), n1 = abs(rnorm(15)*20), n2 = abs(rnorm(15)*20), cat = c(rep(1, 7), rep(0,8)), contin = rnorm(15))

dat = escalc(measure = "SMD", m1i = mean1, m2i = mean2, sd1i = sd1,  sd2i = sd2, n1i = n1, n2i = n2, data = datExample, append = TRUE)
dat

```
Now we can combine the effect sizes and the estimate overall impact and if that impact is significantly different from zero.  The first model is a fixed effect, which assumes that the intercepts (i.e. starting values) are the same across studies.  This assumption seems unlikely, since most studies are conducted in different conditions.  However, the metafor package provides a test of heterogeneity to evaluate whether a random effects model is a better fit.  In the fixed effect analysis, the overall effect size is .0818.

Next, we have the random effects model, which is almost identical, except that we drop the method = "FE" going to the default of random effects.  The random effects model includes several heterogeneity estimators.  The I^2 evaluates the percentage of variability in the effect sizes that is accounted for by the variability between the estimates.  The H^2 statistic evaluates the total variability divided by the within-study variability.  Values closer to one indicate that most of the variability is due to within study variation and values above one indicate larger portions of the variability are not due to within study variation.  All other results are  interpreted the same as the fixed effect model.

Finally, we have a model with two moderators a binary categorical variable (cat) and a continuous variable (contin).  Although the categorical variable is already properly coded, I used the factor(cat) to demonstrate that instead of creating dummy codes for a categorical variable with more than two categories, researchers can use the factor() function to create n-1 dummy variable automatically.  Then we include the continuous variable, contin, set the data, and select the knha = TRUE, because that is the appropriate model when including both categorical and continuous variable in a random effects model.  With this example, there is an omnibus test of the included moderator’s effects.  Then there are individual estimates and test for each of the included moderators.  For example, the effect size after accounting for the cat variable is -.6509.   
```{r}
rma(yi, vi, data = dat, method = "FE")

rma(yi, vi, data = dat)

rma(yi,vi, mods = ~ factor(cat) + contin, data = dat, knha = TRUE)
```


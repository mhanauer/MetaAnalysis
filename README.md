---
title: "Meta Analysis"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Here
```{r}
library(metafor)
set.seed(12345)
datExample = cbind(mean1 = rnorm(15), mean2 = rnorm(15), sd1 = abs(rnorm(15)), sd2 = abs(rnorm(15)), n1 = abs(rnorm(15)*20), n2 = abs(rnorm(15)*20), cat = c(rep(1, 7), rep(0,8)), contin = rnorm(15))

dat = escalc(measure = "SMD", m1i = mean1, m2i = mean2, sd1i = sd1,  sd2i = sd2, n1i = n1, n2i = n2, data = datExample, append = TRUE)

# Example of fixed effect model
rma(yi, vi, data = dat, method = "FE")

# Example of random effects
res = rma(yi, vi, data = dat)
confint(res)

# Example of mixed model with covariates as fixed soaking up differences in effect sizes.  These are continuous variables
res = rma(yi,vi, mods = cbind(ablat, year), data = dat)


# Or let R do the coding and just drop one of them and this says no intercept model.  If we take out the -1 then we have an intercept with one category used as the dummy.
res = rma(yi, vi, mods = ~ factor(cat)-1, data= dat, intercept = TRUE)

res = rma(yi, vi, mods = ~ factor(cat), data= dat, intercept = TRUE)


#Now we have an example with a categorical and continous moderator variable

res = rma(yi,vi, mods = ~ factor(cat) + contin, data = dat, knha = TRUE)
summary(res)
```


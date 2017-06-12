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
data("dat.bcg", package = "metafor")
dat.bcg

dat = escalc(measure = "RR", ai = tpos, bi = tneg, ci = cpos, di = cneg, data = dat.bcg, append = TRUE)

# Example of fixed effect model
rma(yi, vi, data = dat, method = "FE")

# Example of random effects
res = rma(yi, vi, data = dat)
confint(res)

# Example of mixed model with covariates as fixed soaking up differences in effect sizes.  These are continuous variables
res = rma(yi,vi, mods = cbind(ablat, year), data = dat)

# Now we want to analyze the data with categorical covariates.
# Creat dummy codes to indicate the dummy codes
dat$a.random = ifelse(dat$alloc == "random", 1, 0)
dat$a.alternate = ifelse(dat$alloc == "alternate", 1, 0)
dat$a.systematic = ifelse(dat$alloc == "systematic", 1, 0)

#res = rma(yi, vi, mods = cbind(a.random, a.alternative, a.systematic), intercept = FALSE, data = dat)

# Or let R do the coding and just drop one of them and this says no intercept model.  If we take out the -1 then we have an intercept with one category used as the dummy.
res = rma(yi, vi, mods = ~ factor(alloc)-1, data= dat, intercept = TRUE)

rma(yi, vi, mods = cbind(a.alternate , a.systematic), data = dat)

#Now we have an example with a categorical and continous moderator variable

res = rma(yi,vi, mods = ~ factor(alloc) + ablat, data = dat, knha = TRUE)
summary(res)
```


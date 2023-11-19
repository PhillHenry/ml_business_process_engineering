+++
title = 'MLOps: debugging a pipeline'
date = 2022-11-26T11:34:36Z
draft = true
+++

Healthcare in England is broken down into about 40 regions. For each region, we want to measure the differences in clinical outcomes conditioned on the ethnic and socioeconomic categories of the patients. To do this, we feed the data for each health region into a Spark GLM (Generalized Linear Models).

### The problem

Everything was fine with our pipeline for six months before it started to blow up with:

`Caused by: org.apache.spark.SparkException: Failed to execute user defined function(GeneralizedLinearRegressionModel$$Lambda$4903/0x0000000101ee9840: (struct<type:tinyint,size:int,indices:array<int>,values:array<double>>, double) => double)`

Now, before we do a deep dive, the first thing to note is that we have a robust suite of tests that use synthetic data and they are all passing. 

Secondly, the code that was blowing up was used by five other data sets and they were all working fine in production.

If the code seems OK but one path through the ML pipeline was blowing up in code common to other paths, what does this suggest? Well, if it's not the code, there must be something suspicious about the data, right? The tests use synthetic data so of course they would pass.

### The investigation

The first course of action when debugging is to take a good, long stare at the error. This might be obvious but many devs pay insufficient attention to it as it's generally a hundred lines of hard-to-read stack trace. This is like a detective who disregards the crime scene because there's too much evidence to collect. 

Anyway, our murder scene was full of Scala and Python stack traces but if we persevere, we find the line that was triggering the error was a call to `Dataframe.collect()`. This is generally suspicious but on this occasion, we knew we were dealing with a very small data set so this seemed safe. Indeed there were no OOMEs which is the most common problem with calls to `collect()`. 

But remember Spark is lazily evaluated. It could be something deeper in the stack that is the root cause. So, navigating a few stack frames previous, we see some one-hot encoding of ethnic groups. Hmm, what can go wrong with one-hot encoding? Well, one potential gotcha is when there is only one category, an exception will be raised.

However, this seemed unlikely. We break down ethnicities into only five groups and there are over a million people in each health region. It would be extraordinarily unlikely if there were a region that only had patients of a single ethnicity. 

Time to look at the data.

Any region with such homogenous patient data probably has very little data to begin with so lets count the number of rows per region. And bingo! there it is: a region called null that has a single (white) patient. This was a recent development in the data being fed into the model which explained why things had worked so well for so long.

The offending row comes from upstream data sets curated by a different department entirely so we're still considering what to do. For now, we could apply a band-aid and filter out any regions called null or better still, any region with fewer than a few thousand patients (as otherwise we're likely to get single cohorts).

### One model to rule them?

At the end of the day, the code, the model and the data need to be considered holistically. For instance, which data sets you feed into a model must be evaluated beforehand. 

As an example, we also condition on age bands in this particular GLM model so if we were to feed neonatal or paediatric data into the model it would blow up as all patients would fall into the 0-18 age band. Obvious when you think about it but perhaps surprising if you've inherited somebody else's code.

### Conclusion

This project had a robust suite of automated integration tests so we were pretty confident that the problem was not the code. One can never be certain that there is no problem in the code but given our extensive test coverage our prime suspect was the data. These tests allowed us to pivot to where the problem really was: upstream data.

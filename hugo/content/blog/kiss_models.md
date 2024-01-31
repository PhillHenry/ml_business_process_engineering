+++
title = 'KISS Models'
date = 2024-01-31T16:41:22Z
draft = true
+++

## “Keep it Simple, Stupid!”

!["Elimende Inagella's photo of a school sheep"](/img/blog/KISS.jpg)

The KISS principle is another delightful insight from the US military. The idea is that systems work best when they are simple. The [Agile Manifesto](https://agilemanifesto.org/) phrases it in a slightly more neutral tone:

> “Simplicity – the art of maximising the amount of work not done – is essential.”

but the message is the same. 

You may be aware that there are a lot of overly complicated systems out there. There’s even a joke that data scientist tell each other: 

Q. How many models does a data scientist build to solve a problem?

A. Two: a complicated deep learning model for their resume and a simple model for the client.

## Example

A project tried to predict the number of patients in a hospital and the flows between hospital units.
It used an open source, Data Science tool from Facebook to do this.
Given a time series of attendance date, it could then predict the number of patients for the next few days. 

The problems encountered included:

- The tool kept blowing up with `out of memory` errors when presented with real data and integrated with the real infrastructure.

- The flows *between* hospital units did not add up to the total numbers *at* the hospital. (When you think about it, this is not too surprising since the model for flows was independent of the model for attendances and there was no constraint imposed by one on the other).

- The Facebook tool had no semantic knowledge, it just saw a series of numbers. So, for hospitals that had near zero new patients, it might sometimes dip into negative numbers. Clearly, negative 3 patients make no sense.

## An alternative

The simplest solution given some 10 years of data was to take an average of the attendance figures for each day, for each month, for each hospital. 
This is something any Big Data framework could easily do and can be implemented pretty easily.
What's more, this model gave modest (if not stellar) results.

## Conclusion

Start with the simplest model you can. It's generally easier to implement, cognitively less demanding and chances are that it will give reasonable results anyway. If the results are not good enough, only then investigate more exotic models.

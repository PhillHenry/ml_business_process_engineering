+++
title = 'MLOps debugging: an example'
date = 2022-04-11T11:43:05Z
draft = true
+++

In our ML pipeline, we use generalized linear models to calculate the odds of certain clinical outcomes. We showed this to the client but odds were hard for them to understand. "Can we have probabilities instead?" they asked. 

So, having trained the GLMs, we fit the same data and calculate the average probabilities for each cohort. We then bastardise the data to create the counterfactuals For example, socioeconomic status is of interest so let's make everybody the same level (counterfactual) then make our predictions again and compare with the factual results.

Now, one would imagine that if the corresponding GLM coefficient were positive, then the average probability from the counterfactuals would be less than that of the factual data. This is simple maths, right? You dot product your feature vector with the coefficients and plug it into the sigmoid function. A positive coefficient would lead to a bigger product and so a smaller denominator.

We were mostly seeing this but about 10% of our predicitions violated this rule.

### Investigation

A transform compares the two approaches (risks and odds) and with a bit of Pandas:
```
> import pandas as pd
> df = pd.read_csv("/home/henryp/Documents/CandF/fitted_vs_coeffs.txt", delimiter='\t')
> sub = df[["coefficients", "cohort_risk", "counterfactual_cohort_risk", "p_values"]]
> sub["diff"] = sub["cohort_risk"] - sub["counterfactual_cohort_risk"]
> sub = sub.reindex(sub["diff"].abs().sort_values().index)
> sub[-5:]
    coefficients  cohort_risk  counterfactual_cohort_risk  p_values  feature                       model_id               diff
25     -0.520122     0.239911                    0.184961  0.121023  ethnic_category_one_hot_Other a_and_e_cancer         0.054950
23     -0.561247     0.277648                    0.219477  0.006748  ethnic_category_one_hot_Asian a_and_e_cancer         0.058171
44     -0.292537     0.545455                    0.480899  0.821720  ethnic_category_one_hot_Asian wait_18_ophthalmology  0.064556
50     -0.224494     0.307358                    0.229469  0.480723  ethnic_category_one_hot_Black a_and_e_cancer         0.077889
8      -5.340222     0.636364                    0.551042  0.957624  ethnic_category_one_hot_Asian wait_18_ophthalmology  0.085322
```

we note that the p-values indicate a lack of statistical significance in the coefficient even if the difference between factual and counterfactual probabilities can be pretty large.

But given the same data should produce the same p-values and coefficients each time (modulo floating point issues), how could this happen?

In Generalized Logistic Regression models, the coefficients don't change much on each run, even when shuffling the data. This is true irrespective of p-values. Let's demonstrate.

### Shuffling

The Scala code taken from the official Spark docs for a GLR (here) gives this:

```
glr.fit(dataset.orderBy(rand())).coefficients.toArray.zip(glr.fit(dataset.orderBy(rand())).coefficients.toArray).map{ case (x, y) => x - y }

res24: Array[Double] = Array(1.231653667943533E-16, -2.220446049250313E-16, 2.220446049250313E-16, 0.0, 1.1102230246251565E-16, -2.220446049250313E-16, -1.6653345369377348E-16, 2.220446049250313E-16, 2.220446049250313E-16, -2.220446049250313E-16)
```
Running this a few times shows the differences are miniscule even though the p-values are pretty large:
```
glr.fit(dataset.orderBy(rand())).summary.pValues

res25: Array[Double] = Array(0.989426199114056, 0.32060241580811044, 0.3257943227369877, 0.004575078538306521, 0.5286281628105467, 0.16752945248679119, 0.7118614002322872, 0.5322327097421431, 0.467486325282384, 0.3872259825794293, 0.753249430501097)
```
Any differences in the coefficients between runs are almost certainly due to floating point arithmetic.

Modification

However, things are very different when we modify the data; even a small perturbation can radically change coefficients with high p-values.

Note that there are only 500 rows in this test data set. But let's fit two models where on average we drop one row at random. Then we see things like this:
```
def compare2(): Unit = {
  val tol         = 0.002 
  val fitted      = glr.fit(dataset.where(rand() > tol))
  val pValues     = fitted.summary.pValues 
  val stdErrors   = fitted.summary.coefficientStandardErrors
  val coeffs_rand = fitted.coefficients.toArray  
  val other       = glr.fit(dataset.where(rand() > tol))
  val diffs       = other.coefficients.toArray.zip(coeffs_rand).map{ case (x, y) => x - y }
  val diffs_pc    = diffs.zip(coeffs_rand).map{ case (x, y) => (x / y) * 100 } ;  
  val meta        = pValues.zip(diffs).zip(diffs_pc).zip(stdErrors).map{ case(((p, d), pc), s) => (p, d, pc, s) }
  println("%-15s%-15s%-15s%-15s".format("p-value", "difference", "% difference", "std error"))
  meta.sortBy(x => -math.abs(x._1)).foreach { case (p, d, pc, s) => println(s"%-15.3f%-15.3f%-15.3f%-15.3f".format(p, d, pc, s)) }
}

scala> compare2()
p-value        difference     % difference   std error      
0.977          0.051          -224.562       0.793          
0.602          0.101          -24.475        0.792          
0.574          0.043          9.546          0.793          
0.551          -0.035         7.432          0.795          
0.459          0.044          -7.145         0.828          
0.456          0.085          14.590         0.776          
0.283          -0.068         -7.897         0.803          
0.275          0.133          -15.230        0.796          
0.134          -0.067         -5.484         0.811          
0.006          0.119          5.188          0.830        
```

We need run it only a dozen or so times to see that the coefficients with the largest p-value tend to have the largest percentage discrepancies between the two fits and that the sign can change.

### The Culprit

These CLI antics lead me to question the "given the same data" assumption. A closer look at the timestamps of the input data sets revealed they were built a week apart (Palantir's Foundry will by default silently fall back to data sets on a different branch if they have not been built on the current branch. This data may be stale). Well, our data slowly grows over time. So, could these small differences be responsible for big swings in the GLM coefficients? I created in the pipeline a single snapshot of the data from which both the risk and odds values derived and there were zero discrepancies. Sweet.

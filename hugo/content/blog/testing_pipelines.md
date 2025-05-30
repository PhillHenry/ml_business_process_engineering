+++
title = 'Testing pipelines'
date = 2024-12-09T17:31:24Z
draft = true
+++

!["Rodion Kutsaiev's photo of pipelines"](/img/blog/rodion-kutsaiev-pipeline.jpg)


A lot of developer wonder how to test pipelines - see this [Discord thread](https://discord.com/channels/566333122615181327/566333122615181333/1311277597468000337). The best way we've found is to create fixed, known data that when our transform acts on it, we can make reasonable assertions about what comes out.

## Synthetic data

We are operating in the healthcare domain. We have a data set of events at hospitals and we want to turn them into a running total of patients who happen to occupy the hospital on any given day. Our test data should be:
> precisely `x` medical events over `D` days for `y` different unique patients, distributed evenly over hospitals `{a,b,c}` where each patient is admitted on day `i mod D` and discharged `i mod w` days later, where i is the unique patient id in range `[0,y]`. 
Finally, the discharge date is null every `z` patients because we know we have bad data (urgh).

If we turn this natural language into a (Python) code signature, it looks like this: 

```
def random_inpatient_data(
    num_rows: int,
    num_patients: int,
    providers: list,
    discharge_null_every=97,
    attendance_date_fn=lambda i: random_timestamp(i, datetime.strptime("2021-11-01", "%Y-%m-%d")),
    start_time_fn=random_time_given,
) -> DataFrame:
    """
    Fake but realistic data for tests.
    :param num_rows: Total number of raws for this data set
    :param num_patients: The number of unique patients in this data set
    :param providers: possible providers from white a synthetic site will be created
    :param discharge_null_every: step size for null discharge dates
    :param attendance_date_fn: A function taking a seed and returning an attendance date
    :param start_time_fn: A function taking a date and a seed and generating a timestamp
    """
...
```

There's some things to note. First, that there are sensible defaults so our tests create the data with only the peculiarities salient to its needs. Secondly, the data is random-ish but the same for any given set of parameters. 

It all sounds a little complicated but the whole thing is some two dozen lines that's called repeatedly across the test suite.

## The assertions

There are certain invariants we can assert on. Some are straightforward like the hospital occupancy must be always be equal or greater than zero; or that the number of admissions for a day must always be less or equal to the running total for that day.

Some are a little more involved, for instance the sum of daily occupancy deltas over the time frame is zero (ie, everybody who is admitted is ultimately discharged). Obviously the algorithm must be robust and not count zombie patients who appear to be never discharged - remember that "the discharge date is null every z patients" above?

Another invariant is that we should have a reading for all days in a contiguous block. Remember that the input data is a series of events. If 10 patients are admitted on Monday and 5 are dischared on Friday and noting happens on Tuesday to Thursday, do we still have readings for those dates even though nothing happened? (We should)

Crafting the test data also raised some interesting corner cases that we needed to take back to the business analysts. For example. if a patient is discharged the same day they're admitted, do they show up on that day's occupancy numbers or not? If the discharge date is null what do we do with this patient? 

## Conclusion

The use of synthetic data is a powerful tool when building automated regression tests. Tools like [Deequ](https://github.com/awslabs/deequ) can test data quality but require a curated data sets. This is much harder than it sounds especially when their schemas and semantics change. 
 
Creating code that tests the functionality of your pipeline allows you to refactor your code with confidence. Once you try it, you'll never go back.

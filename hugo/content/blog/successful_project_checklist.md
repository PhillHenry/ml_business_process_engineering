+++
title = 'A checklist for a successful project'
date = 2024-02-07T15:39:51Z
draft = true
+++

Requirements

Automated tests

Expectations

CICD


### You can ignore these but you face a world of pain if you do

* **Understand the requirements.** 

Carbon neutral flying cars are an admirable ambition but are they realistic? 
Agree on the acceptance criteria with *all* stakeholders 
(this includes developers and QAs not just the end users). 
You should be able to answer two questions before work begins: *is this feasible* and *is this what is wanted*?

* **Understand the data.**

Some time spent in exploratory data analysis (EDA) is never misspent.
Data quality is often poor and needs reviewing.
Remember that the best machine learning models in the world will disappoint if the data is dirty.

* **Code repos.** 

Incredibly, I know of large organisations where the machine learning code lives on the laptops of data scientists.
They do not use code repositories and as a result, the code is not version controlled nor available for review.

* **Continuous integration.** 

Most data science projects rely on cleaning and rendering the data into a format that is digestible to the ML model. 
Feature engineering as its name suggests is just plain, old fashioned engineering - and that requires *automated regression tests*.
Without automated tests, a simple error might degrade the model and it's the Devil's own work to discover what the bug is.

* **Continuous deployment**

It's rare for a model to be deployed and then the project to be considered complete.
Rather, ML projects need continuous tweaking, just like software projects do.
If you introduce a new model, you want to see the results immediately.
To do this, projects need to be deployed with the press of a button, or as close as dammit.

* **Reproducibility**

It's good science to be able to repeat the experiment and get the same results. You don't want to be part of the [Replication Crisis](https://en.wikipedia.org/wiki/Replication_crisis).
This means that both the code *and the data* need to be versioned and accessible at any point in time.
You get this for free with something like Palantir but their Foundry product is expensive and politically controversial.
So, you might want to look at open source alternatives like [Project Nessie](https://projectnessie.org/) that tries to be Git to Data.

* **Determinism**

Related to this is using deterministic functions. 
This makes testing easier.
If a business requirement asks for *any* item in a set, that value might be different between runs.
Overall, this might not have a significant impact and the business might be happy.
But if the testers are checking a sample of the data, this might be different each time and a baseline will be hard to establish.

* **Monitoring**

Much has been written about *data drift* but there are other more prosaic reasons your data may change over time. 
One scenario I saw was an upstream team stopped updating a huge data set without telling anybody else.
Slowly but surely the data became unusable.
Fortunately, we had a page that gave data quality metrics.
Unfortunately, the QA team stopped checking it. 
We could have sent automated emails but automated emails often go unread. 
This is a tricky situation and you'll have to establish your own processes to prevent warnings being ignored. 

* **Documentation**

Documentation quickly falls out of synch so automated this as much as possible. There are many BDD frameworks out there that help keep your docs in lockstep with your code. I strongly recommend you use one.

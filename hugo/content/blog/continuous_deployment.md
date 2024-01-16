+++
title = 'You Need Continuous Deployment!'
date = 2024-01-15T16:41:15Z
draft = true
+++
The Spitfire was the most awesome dog-fighter of the Second World War. 

Captured German airmen would routinely lie that they were shot down by a Spitfire rather than any other plane as there was no shame in losing to such a formidable foe. 

When Field Marshal Hermann Göring asked a Luftwaffe general what he could do to help in his battle with the British, the general bitingly replied that he'd like a squadron of their Spitfires.

![Paul Jespers' photo of a Spitfire"](/img/blog/spitfire2.jpg)

And yet the Spitfire did not enter service fully formed. It underwent a process of continual improvement. For example, by the end of the war it was over 130km/h faster than its earlier versions.


This illustrates that contrary to popular opinion, engineering is generally a gradual process. 


And this is just as true for data science which is underpinned by data engineering. Models need constant tuning. In the case of an anti-fraud model that I saw one bank deploy, the model would degrade within <i>weeks</i> of its release as the scammers quickly realised what no longer worked and moved quickly on to their next trick. Consequently, the model needed to be constantly retrained and constantly <b>redeployed</b>.


![Kitchener](/img/blog/you_need_continuous_deployment.jpg)
> "I highly recommend getting a 'hello world' type project working with continuous deployment ... before you proceed directly into a complex ML project when you are learning new technology." 
> - Practical  MLOps, O'Reilly

Continuous deployment can also identify hard-to-catch bugs in a machine learning model. Example: we were continuously deploying to a test environment when we noticed our model's <a href="https://en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve">area under the curve</a> drop a few percentage points. It wasn't much but enough to make us roll back to a previous Git commit and examine what had changed. It was a subtle bug that our otherwise robust suite of unit tests did not catch. Without this continuous release process, we'd probably never have found this bug as it degraded performance but not by enough to make us launch a full-scale investigation. (As with most ML projects, we didn't know <i>a priori</i> what the answer should be).


The continuous improvement process in aeronautical engineering that lead to the Spitfire has been around for almost a century.
The software development community has employed continuous deployment for decades. 
But it's only the most mature data science teams today that have an automated pipeline. 

If you want to introduce continuous deployment to your ML model, [contact us](/contact/).

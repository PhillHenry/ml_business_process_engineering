+++
title = 'Right-sizing your team'
date = 2023-12-07T16:02:52Z
draft = true
+++

Shortly after 9/11, the US military conducted war games that pitched American forces against an unnamed Middle Eastern country. The Middle Eastern country in this simulation was commanded by the retired maverick, Paul Van Riper. The US forces were commanded by four star general, Burwell B. Bell. 

Bell adopted a very conventional strategy - after all, he commanded the most powerful fleet in the World so what need had he of subterfuge? He demanded Van Riper’s surrender and sailed his fleet straight into the Persian Gulf.

Van Riper knew he possessed laughably inferior forces so was forced to use radical tactics. He decentralised control, used unconventional communication techniques and encouraged decision-making at the local level.

The battle was over in a few minutes. Bell was stunned. Van Riper had “sunk my damn navy!” The organisers of the war game were so shocked they called a halt to the proceedings, refloated Bell’s armada and, much to Van Riper’s chagrin, started the game again as if nothing had happened.

The average data scientist does not look nor think like the average marine but Van Riper’s strategy is a perfect metaphor for how to run a data science project. In particular, he employed:

- Small, decentralised units granted a large degree of autonomy
- Thinking outside the box when it comes to communication and coordination
- Eschewing conventional wisdom 

Van Riper’s methodology might have been radical in the military world, but it’s business as usual in software engineering. Data science must make that same step.

# Less is more

In Victorian times, the productivity of a factory was related to how many workers it had. Well into the 20th Century, the number of hands on an assembly line would dictate, say, how many cars were built. Things started to change in the 1980s with Japanese production techniques and automation. But it wasn’t until the internet boom of the late 90s when the word “knowledge worker” entered common usage and the idea of command-and-control production became outdated.

Perhaps MBA programmes have not yet caught up as surprisingly few management consultants seem to be aware that “less is more” when it comes to software. Our Victorian forebears would be shocked but in the knowledge economy, _fewer_ workers are needed for a tech project to succeed not more.

The Agile movement recognised this decades ago. One of the Agile movement's founders, Jim Highsmith, described in 1994 how he “put a **small** team together in a room; had short, iterative development; and used end-of-iteration customer focus group reviews. It was wildly successful”<sup>[1]</sup>. If you talk to anybody who has been around the Agile world for a while, you’ll hear similar stories.

I experienced a similar success story in data science when we had adopted Highsmith's principles (the only tenet we did not adopt was having a **colocated** team as this was during Covid). We were a small team of three data science developers plus a truly excellent analyst who was our proxy to the business. With such a small, self-organising collective, we all knew what the other was working on so there was no duplication of work and almost no misunderstanding. We delivered on time and on budget and later heard that our competitor was still putting together their pitch by the time we had finished - a pitch that proposed a head count of more than a dozen.

# Workshops are for Victorians

Highsmith also touches on another aspect of Agile methodologies: short iterations. In the world of data science, it’s common to see a workshop organised at the beginning of a project and then the team told to go off and build what was discussed with no further input. Alas, it’s not long before they hit a roadblock and they need guidance from subject matter experts. But without the cadence of regular meetings with the customer, timelines soon start to slip.

In an attempt to compensate for this oversight, more bodies are often thrown at the problem. This inevitably causes communication problems. In software cicles, this is know an Brook’s Law and is stated as:

> “When a task cannot be partitioned because of sequential constraints, the application of more effort has no effect on the schedule. The bearing of a child takes nine months, no matter how many women are assigned.”

Professor Fred Brooks was the project manager of a large IBM system back in the 1960s and 70s but this doesn’t make his advice any less salient. His seminal book<sup>[2]</sup> that contains the brutal lessons he learned has been in print ever since it was first published in 1975. 

Most computer programmers know of Brooke’s Law even if those managing them perhaps do not. It’s a universal principle that applies equally to data science. 

To summarize: a team should be as small as possible but no smaller. 


[1] “Agile Software Development Ecosystems” - Jim Highsmith 

[2] “The Mythical Man-Month” - Fred Brooks

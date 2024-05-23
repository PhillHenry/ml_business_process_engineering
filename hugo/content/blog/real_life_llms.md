+++
title = 'Real Life AI'
date = 2024-05-23T20:06:34+01:00
draft = false
+++

It's been a year since ChatGPT was released to universal acclaim. Now things are calming down, let's strip away the hype and see if it can actually do something useful other than code snippets.

We have 650 lines of SQL Server code that we want converted to the Spark SQL dialect. **This is a real life problem**. We wonder if ChatGPT can save us tediously converting it by hand.

## A large file

![Upload the SQL](/img/blog/rows_upload.png)

ChatGPT starts by giving a nice summary of what the SQL does. It then converts the first 50 lines or so. These are correct but do not present much of a challenge. Still, so far so good.

![Carry on](/img/blog/carry_on.png)

Now it gets to its first real problem. SQL Server and Spark have the parameters of the `DATEDIFF` keyword in the opposite order to each other. Does ChatGPT know this?

![Datediff](/img/blog/datediff.png)

Bravo! It reverses the arguments correctly.

![Carry on again](/img/blog/carry_on_2.png)

Now we encounter the first oddity. ChatGPT hallucinates a table that doesn't exist.

![Hallucination](/img/blog/hallucination.png)

Tables

- `SET_RecordIds_0420`
- `SET_RecordIds_0520`
- `SET_RecordIds_0620`
- `SET_RecordIds_0720`
- `SET_RecordIds_0820`
- `SET_RecordIds_0920`

exist but `SET_RecordIds_1020` is totally fictitious. Yeah, the names of these tables are not great but - hey! - this is the real world, right? Things aren't perfect. 

OK, let's just delete references to `SET_RecordIds_1020` and move on.

![Carry on again](/img/blog/carry_on_3.png)

Now this is downright annoying. It says `Replace 'some_table' with the actual source table`. Huh? It's telling me to fill in its blanks!

![Do my work for me](/img/blog/replace_table.png)

I've given it all the information it needs. Why can't it finish the job? 

Instead, I need to decipher about 200 lines of code it's written and try to make it work since we're nowhere near finished. 
And I'm not filled with trust that it can see this task to the bitter end.
At this point, I think I'm better off doing it by hand.

## Conclusion

ChatGPT is great for simple snippets - a better [StackOverflow](https://stackoverflow.com/), perhaps. But is it going to revolutionise computer coding any time soon? Well, colour me disappointed.

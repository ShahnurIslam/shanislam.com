# SQL Part 2 of 4


I said I'd continue these posts this year and that's what I plan to do! A sequel to my post on SQL! *(My wife thought this was a terrible joke)*

Now let's talk about SEGMENTATION! 

Segmentation isn't something I found as intuitive as basic SQL. You can generally figure out what a basic SQL statement is going to do before it runs. Have a look at my last  [SQL post](http://shan-data-science.co.uk/2017-05-15-sql1/) for an example. 

![LOTR](/images/posts/25sryb.jpg)


The general idea of segmentation is exactly as it sounds. You're slicing the data into segments*(I've also seen this called stratifying)*.

This can also lead to clustering but that involves using algorithms such as Hierarchical or K-means that I won't cover that here*(Maybe in future posts!)*


Here are a few real world examples of segmentation:

- Grouping CUSTOMERs into segments based on their sales data. You could potentially have 3 groups such as *low spenders, medium spenders, high spenders*
- Grouping data by address e.g. City. Looking at most profitable cities
- Grouping into desirable records and undesirable records. E.g. success/unsuccessful campaign

I'll be using third example to demonstrate this

I've created some dummy table of records that shows a list of 500 advertising campaigns*(I work in an advertising so made it relatable)* with their spends and income generated. You can find the data here.

I've called this table CAMPAIGNS in my database.

Here's the syntax

```
SELECT * FROM
(SELECT
CASE WHEN ROI > 0.8 "Successful" else "UNSUCCESSFUL")

```

Campaign and their spends with sales generated. How do you know which is successful and which isn't. Needs to be defined ROI > 0.8. 

How many campaigns were successful?




Bonus what's the average Sale value of these campaigns

Obviously this is dummy data and we can do more analysis
Data can be found here

Thanks for reading, appreciate it.

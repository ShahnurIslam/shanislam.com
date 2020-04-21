# G1 Climax


Here's a post that is slightly different to the other posts that I've done so far. Rather than having a step by step tutorial
on how to do something. The below allows me to work on my python skills and explain a few things as I go along.

People who work in Data Science tend to work in programming languages R or Python. I've been learning both languages over the last two years and at the moment I'm trying to get better at my Python. One of things people say is to get better at data science is to work on data that you have an interest in. 

One of my main passions in life has been wrestling (which my wife says is lame).  I've watched wrestling for the last 19 years which when you write down is actually quite scary. In this case, I decided do some analysis on Japanese Wrestling! In particular, the G1 Climax tournament.

![G127](/img/G1271.jpg)

The G1 Climax is an annual Japanese wrestling tournament that's held every summer over 20 days. Rather than a simple knockout tournament it's a league made up of two blocks (named A block & B block) each consisting of 20 wrestlers. The winner of each block then face off and the winner goes to headline wrestle kingdom in January.

I made the below in Jupyter Notebook and converted it to a markdown file to post on the site. Let's begin!


Let's start off with importing all the relevant libraries which will be updated as we go


```python
import pandas as pd #standard package to work with dataframes
import numpy as np
import itertools # We use this to do some permutations to work out potential matches
import matplotlib.pyplot as plt
import seaborn as sns
import math
import decimal
import datetime
from sklearn.linear_model import LinearRegression
from sklearn.metrics import r2_score
sns.set_style('white')
```

Here are some potential things to look at:
*Who on average has highest match rating?
Does Dave Meltzer's rating correlate with match time or any other variables?*

Let's start off by reading the csv file I quickly made up with the wrestler's names and which block they're in.

```python
df = pd.read_csv("G1 Competitors.csv") #Using pandas read_csv
```

We use the data frame property .info() to give us some quick info on the data frame

```python
df.info() #use the dataframe propetry .info() to give us information on the dataframe
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 10 entries, 0 to 9
    Data columns (total 2 columns):
    A Block    10 non-null object
    B Block    10 non-null object
    dtypes: object(2)
    memory usage: 240.0+ bytes

The information above tells us that the data frame only contains 10 entries with no null objects which is good to see. We can just type in **df** into the console and see what the whole data frame looks like. Of course if we had more rows we wouldn't be able to do this and would use *df.head()*

```python
df
```


| | A Block | B Block |
| :--- | :--- | :--- |
| 0 | Hiroshi Tanahashi | Kazuchika Okada |
| 1 | Togi Makabe | Toru Yano |
| 2 | Tomohiro Ishii | Satoshi Kojima |
| 3 | Hirooki Goto | Michael Elgin |
| 4 | YOSHI-HASHI | Juice Robinson |
| 5 | Bad Luck Fale | Tama Tonga |
| 6 | Yuji Nagata | SANADA |
| 7 | Zack Sabre Jr. | EVIL |
| 8 | Kota Ibushi | Minoru Suzuki |
| 9 | Tetsuya Naito | Kenny Omega |

I noticed that some of the wrestler's name have capitals while others don't. Let's convert all the names to upper case.
We use the data frame method applymap which applies a function to every cell in a data frame and rather than writing a whole function we will just create one inside in the applymap also known as an anonymous function.

```python
df = df.applymap(lambda x: x.upper()) 
df # Let's print the dataframe to see if that's worked
```

| | A Block | B Block |
| :--- | :--- | :--- |
| 0 | HIROSHI TANAHASHI | KAZUCHIKA OKADA |
| 1 | TOGI MAKABE | TORU YANO |
| 2 | TOMOHIRO ISHII | SATOSHI KOJIMA |
| 3 | HIROOKI GOTO | MICHAEL ELGIN |
| 4 | YOSHI-HASHI | JUICE ROBINSON |
| 5 | BAD LUCK FALE | TAMA TONGA |
| 6 | YUJI NAGATA | SANADA |
| 7 | ZACK SABRE JR. | EVIL |
| 8 | KOTA IBUSHI | MINORU SUZUKI |
| 9 | TETSUYA NAITO | KENNY OMEGA |

Let's now separate the wrestler from data frames **df** in two data frames called **A_Block** and **B_Block**


```python
A_Block = pd.DataFrame(df['A Block'])
A_Block.columns = ['Wrestler']
B_Block = pd.DataFrame(df['B Block'])
B_Block.columns = ['Wrestler']
```

Ok we've now got two separate data frames called *A_Block* & *B_Block*. Let's add the usual fields you'd see in any sports league **'Matches','Wins','Losses','Draws'**. 

I'm also going to include **'Match Time'** as the total time a wrestler has competed. I also plan to add another column called **'DMR'**, which stands for Dave Meltzer Rating. Dave Meltzer is a 30 year wrestling journalist whose ratings of matches are out of five and is held in high standing in the wrestling community. By default, I've set these to NaN


```python
A_Block['Matches'] = np.NAN
A_Block['Wins'] = np.NAN
A_Block['Losses'] = np.NAN
A_Block['Draws'] = np.NAN
A_Block['Points'] = np.NAN
A_Block['Match_Time'] = np.NAN
A_Block['DMR'] = 0.00
B_Block['Matches'] = np.NAN
B_Block['Wins'] = np.NAN
B_Block['Losses'] = np.NAN
B_Block['Draws'] = np.NAN
B_Block['Points'] = np.NAN
B_Block['Match_Time'] = np.NAN
B_Block['DMR'] = 0.00
```
Lets use head to see if the tables has been set up correctly

```python
A_Block.head(1)
```

| | Wrestler | Matches | Wins | Losses | Draws | Points | Match_Time | DMR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | HIROSHI TANAHASHI | NaN | NaN | NaN | NaN | NaN | NaN | 0.0 |

Checking B Block

```python
B_Block.head(1)#B block
```

| | Wrestler | Matches | Wins | Losses | Draws | Points | Match_Time | DMR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | KAZUCHIKA OKADA | NaN | NaN | NaN | NaN | NaN | NaN | 0.0 |

We've now created our results data frames. Let's create two more dataframes that records the result of each match. First of all how many potential matches are there per block? This is a form of permutations, well actually combinations as we don't take the order of the wrestlers into account. We can use the combinations formula(below) where my ***n=10*** & ***r=2***


![Combination_equation](/img/Combination_equation.gif)


```python
math.factorial(10)/(math.factorial(2)*math.factorial(10-2))
```




    45.0

This tells us there are a total of 45 possible matches for each block.

I was going to write a custom function to list all these possible matches but luckily the python community is so large someone has already done this for me! The function I'm going to use is within the python library **itertools**. We will use the function **combinations()** as we're looking for all unique combinations. The combination function takes a series as a parameter so we can't simply pass in the data frame and we need to select the Wrestler column as a series.

Using the combination function, I'll create two new data frames called **A_matches** and **B_matches**


```python
A_matches = pd.DataFrame.from_records(list(itertools.combinations(A_Block['Wrestler'],2)), columns = ['Wrestler 1', 'Wrestler 2'])
B_matches = pd.DataFrame.from_records(list(itertools.combinations(B_Block['Wrestler'],2)), columns = ['Wrestler 1', 'Wrestler 2'])
```

Check that A_matches data frame has been set up correctly

```python
A_matches.head()
```

| | Wrestler 1 | Wrestler 2 |
| :--- | :--- | :--- |
| 0 | HIROSHI TANAHASHI | TOGI MAKABE |
| 1 | HIROSHI TANAHASHI | TOMOHIRO ISHII |
| 2 | HIROSHI TANAHASHI | HIROOKI GOTO |
| 3 | HIROSHI TANAHASHI | YOSHI-HASHI |
| 4 | HIROSHI TANAHASHI | BAD LUCK FALE |


Ok we've got all the potential matches for each block. Let's add a few more fields to these data frames. Let's add the fields **Winner, Loser, Match_Time, Draw, DMR**. We've also added a match counter field labelled **Matches**

```python

A_matches['Winner'] = np.NAN

B_matches['Winner'] = np.NAN

A_matches['Loser'] = np.NAN

B_matches['Loser'] = np.NAN

A_matches['Match'] = 1

B_matches['Match'] = 1

B_matches['Match_Time'] = np.NAN

A_matches['Match_Time'] = np.NAN

B_matches['Draw'] = False

A_matches['Draw'] = False

B_matches['DMR'] = 0.00

A_matches['DMR'] = 0.00

```

Again check the table has been updated

```python

A_matches.head(5)

```

| | Wrestler 1 | Wrestler 2 | Winner | Loser | Match | Match_Time | Draw | DMR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | HIROSHI TANAHASHI | TOGI MAKABE | NaN | NaN | 1 | NaN | False | 0.0 |
| 1 | HIROSHI TANAHASHI | TOMOHIRO ISHII | NaN | NaN | 1 | NaN | False | 0.0 |
| 2 | HIROSHI TANAHASHI | HIROOKI GOTO | NaN | NaN | 1 | NaN | False | 0.0 |
| 3 | HIROSHI TANAHASHI | YOSHI-HASHI | NaN | NaN | 1 | NaN | False | 0.0 |
| 4 | HIROSHI TANAHASHI | BAD LUCK FALE | NaN | NaN | 1 | NaN | False | 0.0 |


Ok we now have a table of the potential matches and all the additional fields. I decided to write two custom functions, one that updates the matches data frames with the results and the other to update the block table. I didn't want to type out the full names either as the spellings of Japanese names can be quite hard. So the function does a partial match and prints out the names it's matched as a check.

I didn't want to bombard this post with long bits of code so those functions and updates can be found here:

[Code for Functions and update](https://github.com/ShahnurIslam/shahnurislam.github.io/blob/master/_posts/2017-10-23-G1_Climax_code.md)

Let's look at the results tables and see who came out on top!

```python
A_Block
```

| | Wrestler | Matches | Wins | Losses | Draws | Points | Match_Time | DMR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | TETSUYA NAITO | 9 | 7 | 2 | 0 | 14 | 0.0 | 4.000000 |
| 1 | HIROSHI TANAHASHI | 9 | 6 | 3 | 0 | 12 | 0.0 | 4.111111 |
| 2 | BAD LUCK FALE | 9 | 6 | 3 | 0 | 12 | 0.0 | 3.166667 |
| 3 | KOTA IBUSHI | 9 | 5 | 4 | 0 | 10 | 0.0 | 4.222222 |
| 4 | ZACK SABRE JR. | 9 | 5 | 4 | 0 | 10 | 0.0 | 3.611111 |
| 5 | HIROOKI GOTO | 9 | 5 | 4 | 0 | 10 | 0.0 | 3.694444 |
| 6 | TOMOHIRO ISHII | 9 | 4 | 5 | 0 | 8 | 0.0 | 4.250000 |
| 7 | TOGI MAKABE | 9 | 4 | 5 | 0 | 8 | 0.0 | 3.611111 |
| 8 | YOSHI-HASHI | 9 | 2 | 7 | 0 | 4 | 0.0 | 3.638889 |
| 9 | YUJI NAGATA | 9 | 1 | 8 | 0 | 2 | 0.0 | 4.138889 |


B Block..


```python
B_Block
```



| | Wrestler | Matches | Wins | Losses | Draws | Points | Match_Time | DMR |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | KENNY OMEGA | 9 | 7 | 2 | 0 | 14 | 0.0 | 3.888889 |
| 1 | KAZUCHIKA OKADA | 9 | 6 | 2 | 1 | 13 | 0.0 | 4.250000 |
| 2 | EVIL | 9 | 6 | 3 | 0 | 12 | 0.0 | 3.444444 |
| 3 | MINORU SUZUKI | 9 | 4 | 4 | 1 | 9 | 0.0 | 3.611111 |
| 4 | SANADA | 9 | 4 | 5 | 0 | 8 | 0.0 | 3.666667 |
| 5 | MICHAEL ELGIN | 9 | 4 | 5 | 0 | 8 | 0.0 | 3.583333 |
| 6 | JUICE ROBINSON | 9 | 4 | 5 | 0 | 8 | 0.0 | 3.222222 |
| 7 | TAMA TONGA | 9 | 4 | 5 | 0 | 8 | 0.0 | 2.944444 |
| 8 | TORU YANO | 9 | 4 | 5 | 0 | 8 | 0.0 | 1.750000 |
| 9 | SATOSHI KOJIMA | 9 | 1 | 8 | 0 | 2 | 0.0 | 3.305556 |


Data tables are fine, but a visual representation is always better. Let's have a look at the block results in graph form, using the Seaborn package

```python

ax= sns.barplot(y='Wrestler', x='Points', data=A_Block, color= 'Red')
ax.set_xlim(0,18)
sns.despine()
ax.set_ylabel('')
ax.set_xlabel('points')
ax.set_title('A Block')
plt.show()
ax= sns.barplot(y='Wrestler', x='Points', data=B_Block,  color= 'Blue')
ax.set_xlim(0,18)
sns.despine()
ax.set_ylabel('') 
ax.set_xlabel('points')
ax.set_title('B Block')
plt.show()
```


![png](/img/chart_1.png)

![png](/img/output_40_1.png)

The winner of the A Block was Kenny Omega and the B Block was Tetsuya Naito who are two of the biggest wrestlers there so not a surprise *(Yes I know wrestling is fake as my wife keeps saying)*

Ok who was the highest rated star of the tournament

```python
Combined = pd.concat([A_Block, B_Block])
Combined['Block'] = np.NAN
Combined.iloc[0:10,8] ="A Block"
Combined.iloc[10:20,8] ="B Block"
Combined = Combined.sort_values(['DMR'],ascending=False).reset_index(drop=True)
ax = sns.barplot(y='Wrestler', x='DMR',data=Combined, color = 'Gold')
sns.despine()
ax.set_xlabel('Average Dave Meltzer Rating')
plt.show()
```


![png](/img/output_43_0.png)


Ok so the top two wrestlers were Tomohiro Ishii and Kazuchika Okada. I wasn't surprised with Okada who's doing amazing this year but Ishii was a surprise. Yano had the worst overall rating which kind of makes sense as he's more of a comedy act and not a competitive wrestler.

One of the potential questions I had was if a good Meltzer rating related to the match length. Let's plot these two against each other. 


```python
C_matches = pd.concat([A_matches, B_matches])
C_matches['Match_Time'] = C_matches['Match_Time'].str.split(':').apply(lambda x: int(x[0]) * 60 + int(x[1]))
ax = sns.regplot(y='DMR', x='Match_Time',data=C_matches)
plt.show()
```


![png](/img/output_46_0.png)


OK so some correlation between the two which makes some sense. Obviously this doesn't account for who's in the match and what actually happened in it. Let's see what our equation would look like


```python

lm = LinearRegression()
X = C_matches['Match_Time'].values[:,np.newaxis]
y = C_matches['DMR'].values
lm.fit(X,y)
m = lm.coef_[0]
b = lm.intercept_
print("Our Linear equation is " + 'y = {0} * x + {1}'.format(m, b))
```

    Our Linear equation is y = 0.002155728983767316 * x + 1.8171627906221903
    

Let's see what the mean squared error is and variance score. These calculations tell us how well our model fits the data.



```python
print("Mean squared error: %.2f" % np.mean((lm.predict(X) - y) ** 2))
# Explained variance score: 1 is perfect prediction
print('Variance score: %.2f' % lm.score(X, y))
```

    Mean squared error: 0.36
    Variance score: 0.58
    

Ok so in our model, 58% of the variability in Match ratings can be explained using match time. We also got a really low mean squared error which means the model is a really good fit. 

**But like I said this doesn't account for the context of the matches despite the good fit.**

Hopefully this wasn't too complicated but I wanted to do analysis on some data that I was interested in. I'll most likely add to this post in the future as I learn more modelling tools that account for more of the variables present.

Thanks for reading this post if you did! Any feedback or tips as always would be greatly appreciated.

Shan




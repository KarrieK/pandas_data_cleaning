# Cleaning dirty data using Pandas and Jupyter notebook

There is more to life than a million rows - fact. Most data journalists start in excel, then progress to SQL and so forth but once your data swells in size most people struggle to clean millions of rows of dirty data. 

Rather than venturing down the SQL cleaning route and acknowledging that OpenRefine has its limitations I'm putting together a little cheat sheet on how to clean dirty data using pandas in Jupyter notebook. 

##First steps - importing data and taking a look

It's all well and good saying we're going to clean dirty data but do we even know how it's dirty?
We need to eyeball that sucker and figure how it looks. 

First thing we need to do is read our data into pandas and take a look for ourselves.

`import pandas as pd`

`df = pd.read_csv('/user/home/test.csv')`

`df.head()`

Here we import pandas using the alias 'pd', then we read in our data.

`df.head` - shows us the first 5 rows and headers - it gives us an idea what to expect. 
`df.tail` - shows us the last 5 rows

Take a good look at that data and figure out what values you were expecting and what looks unusual. This is a good time to pull out your data dictionary and start looking though your data. 

We also have to consider what type of values each of our columns are stored as. You might see that numbers are imported as text strings making it impossible to perform calculations on them. 

To check this we use the following command:

`df.types`

This will return a list with your data types in it - the most commong types are int, float, datetime and object. An object is often an alias for a string. All Pandas knows is that it cant perform mathematical calculations on an object. 

Next we want to know how many columns and rows are in our dataset. To do that we use .shape like below:
`df.shape` 

Maybe we want to see some key stats in our dataframe without delving too deep, mean values, min and max. Just so we can get a feel of what we're working with. To do that we use the .describe like below:
`df.describe` 

##Merging, joining and concatenating data

Sometimes before we can clean up our dataset we need to re-structure or build it; merging, joining and concatenating rows and columns enables us to take multiple csvs and join them together. This saves time when it comes to cleaning our data for analysis

###Concatenating data frames

Below we have three dataframes df1, df2 and df3 that we want to merge together to create one mighty dataset 
```
In [1]: df1 = pd.DataFrame({'A': ['A0', 'A1', 'A2', 'A3'],
   ...:                     'B': ['B0', 'B1', 'B2', 'B3'],
   ...:                     'C': ['C0', 'C1', 'C2', 'C3'],
   ...:                     'D': ['D0', 'D1', 'D2', 'D3']},
   ...:                     index=[0, 1, 2, 3])
   ...: `

In [2]: df2 = pd.DataFrame({'A': ['A4', 'A5', 'A6', 'A7'],
   ...:                     'B': ['B4', 'B5', 'B6', 'B7'],
   ...:                     'C': ['C4', 'C5', 'C6', 'C7'],
   ...:                     'D': ['D4', 'D5', 'D6', 'D7']},
   ...:                      index=[4, 5, 6, 7])
   ...:

In [3]: df3 = pd.DataFrame({'A': ['A8', 'A9', 'A10', 'A11'],
   ...:                     'B': ['B8', 'B9', 'B10', 'B11'],
   ...:                     'C': ['C8', 'C9', 'C10', 'C11'],
   ...:                     'D': ['D8', 'D9', 'D10', 'D11']},
   ...:                     index=[8, 9, 10, 11])
   ...: 
   ```
   
  To do this we are going to concatenate them using pd.concat

```
In [4]: frames = [df1, df2, df3]

In [5]: result = pd.concat(frames)
```
While I'm a fan of pd.concat you can use .append to join your dataframes together. Check our the code below:

`result = df1.append([df2, df3])`
 
##Cleaning

Before we touch a single object we need to make a copy of our data first

`df2 = df.copy()`

Now we can get cracking. Hopefully at this point you have an idea of how your data is dirty and how you can clean it. Howver if you suspect that maybe everything isn't what it seems and that that pesky csv format has led to disjointed data in columns we can check that out. 

To peer into our data in a single column and make sure it only contains dates and no postcodes or amounts and no names we can use the following command:

`df2.DATE.value_counts().sort_index()`

This will give us a list of all the unique entries and the number of each. Any unslightly data whcih has bled in from other columns should be clustered at the bottom ready for you to strip out. 

But maybe we're good to go, but our data types are all wrong. Perhaps our amounts are a string 1,234,222 and we want them as 1234222 so we can convert them into a numeric value. Then we need to remove the commas. To do this we are going to use `str.replace()`
```
df2['amount'] = df2['amount'].str.replace(',', '')
df2.head()
```

###Converting data types

When you upload your csv to pandas, it might not automatically detect that the correct data type for a number.
Pandas often reads in numbers in as objects. But we can not perform calculations on objects.

The first thing we do is check our data types 

`df.dtypes`

Date               int64
Postcode           object
Names              object
Amount             object

dtype: object

In order to sum or count the 'Amount' column we need to convert the data type of an integer. 

To do this we use the following code below:

```
df['Amount'] = pd.to_numeric(df['Amount'])
df.dtypes
```
###Deleting a column

Dropping a column is very simple and straightforward in Pandas. 

`del df2['column_name']` 

Be aware that you cannot string multiple column names together like 
`del df2['col1','col2','col3']`

Instead you need to stack them on top of each other like below
```
del df2['column_name'] 
del df2['column_name'] 
del df2['column_name']
```

###Re-ordering columns

Re-ordering columns is very fast and easy. We specify the order we want using double square brackets. 

`df2 = df2[['A', 'B', 'C','D','E']]`

###Renaming column headers

In order to re-name a column header, we need to specify that our current column is equal to a new one. 

`df2 = df2.rename(columns={'amount_clean': 'amount'})`

##Dates and time

Working with dates and time is pretty tricky in post programming languages, hell it's tricky in excel. What I have found though is that you can extract years, months and days from your date column without too much hassle. 

We can also convert time stamps into total minutes, hours or seconds using the datetime library. 

###Dealing with dates

Say you have a Date Column with dates that look like this: 01/02/2010 or 01-05-2010. We want to extract the month or year without splitting it like a string. 

First thing you need to do is to confirm what sort of data you're working with. Here we use our handy old `dtypes` command again. You should see something like this:

`df2.dtypes`
```
DATE                          object
POSTCODE                      object
dtype: object
```
This means that Pandas is interpreting our data as an object, a container of sorts of data it's not really able to parse. Generally this could mean that our data is a string. 

We can use a python function to confirm that our DATE column is definitely a string:

`type(df2['DATE'][0])`
`str` is our output confirming our suspicions.

So what we need is a format we can work with, luckily in python there is a great library called Datetime which will do the job for us. 

So back at the top of our program we import Datetime underneath where we previous imported pandas and we re-load our csv

```
import pandas as pd
import datetime
df = pd.read_csv('/user/home/test.csv')
```
Then we convert our python object into a Datetime object while at the same time creating a new column called 'Year' in our dataframe:

`df2['YEAR'] = pd.DatetimeIndex(df2['DATE']).year`

Run `df2.head()` after running the conversion above and you should have a new column in your dataframe with years cleanly extracted. 
###Working with times

So I FOI'd a government department for 911/999 call from a specific city. I need to calculate the mean of my time to figure out which areas get the quickest responsebut my data is a string and looks like this `00:00:00`. I check my data type and yup anothr object. 

Well we need to convert it to something we can work with. Because theoretically our fire department is supposed to arrive to a call within ten minutes, I want the total seconds for each call in a new column. 

To do this I make sure I have imported the library `datetime`like above then I create an empty list and write a for loop using one of the functions .totalseconds() contained within the datetime module. It looks something like this:

```
totes = []
for td in df['Best Response Duration Time']:
    totes.append(td.total_seconds())

len(totes)     
    ```
By grabbing the length of my new list I know if I have the correct number of total seconds for the number of rows in my dataframe. 

Now I need to assign that list as a new column in my data frame so I can compare mean response times with postcodes. 

```
se = pd.Series(totes)
df['RESPONSE_TIME_SECONDS'] = se.values

```
I make sure everything has gone smoothly with df.head() but we should have a new column in our dataframe with the total number of seconds stored as an integer ready to be used for analysis.

##Saving data

So your data is nice and clean and now you want to save it to csv. This is pretty easy in Pandas. We need to specify the new name and the encoding. 

`df2.to_csv('clean_data.csv', encoding='utf8')`

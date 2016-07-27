# Cleaning dirty data using Pandas and Jupyter notebook

There is more to life than a million rows - fact. Most data journalists start in excel, then progress to SQL and so forth but once your data swells in size I struggle to clean millions of rows of dirty data. 

Rather than venturing down the SQL cleaning route and acknowledging that OpenRefine has its limitations I'm putting together a little cheat sheet on how to clean dirty data using pandas in Jupyter notebook. 

##First steps - importing data and taking a look

It's all well and good saying we're going to clean dirty data but do we even know how it's dirty?
We need to eyeball that sucker and figure how how it looks. 

First thing we need to do is read our data into pandas and take a look for ourselves.

`import pandas as pd`

`df = pd.read_csv('/user/home/test.csv')`

`df.head()`

Here we import pandas using the alias 'pd', then we read in our data.

`df.head` - shows us the first 5 rows and headers - it gives us an idea what to expect. 
`df.head` - shows us the last 5 rows

Take a good look at that data and figure out what values you were expecting and what looks unusual. This is a good time to pull out your data dictionary and start looking though your data. 

We also have to consider what time of values each of our columns are stored as. You might see that numbers are imported as text strings making it impossible to perform calculations on them. 

To check this we use the following command:

`df.types`

This will return a list with your data types in it

`df.shape` - shows us how many observations are in our data set

##Merging, joining and concatenating data

Sometimes before cleaning our data set we need to build it first, merging, joining and concatenating rows and columns enables us to take multiple csvs and join them together. This saves time when it comes to cleaning our data for analysis

###Concatenating data frames

Below we have three data frame that df1, df2 and df3 
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
   
  We want to combine these three data frames into a single data frame that we can analyse. To do this we are going to concatenate them. 

```
In [4]: frames = [df1, df2, df3]

In [5]: result = pd.concat(frames)
```

###Cleaning

First thing we do is make a copy of our data 

`df2 = df.copy()`

Maybe our amounts are a string 1,234,222 and we want them as 1234222 so we can convert them into a numeric value. Then we need to remove the commas. To do this we are going to use `str.replace()`
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

`del df2.['column_name']` 

###Re-ordering columns

Re-ordering columns is very fast and easy. We specify the order we want using double square brackets. 

`df2 = df2[['A', 'B', 'C','D','E']]`

###Renaming column headers

In order to re-name a column header, we need to specify that our current column is equal to a new one. 

`df2 = df2.rename(columns={'amount_clean': 'amount'})`

Saving data

So your data is nice and clean and now you want to save it to csv. This is pretty easy in Pandas. We need to specify the new name and the encoding. 

`df2.to_csv('clean_data.csv', encoding='utf8')`

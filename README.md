# Cleaning dirty data using Pandas and Jupyter notebook

There is more to life than a million rows - fact. Most data journalists start in excel, then progress to SQL and so forth but once your data swells in size I struggle to clean millions of rows of dirty data. 

Rather than venturing down the SQL cleaning route and acknowledging that OpenRefine has its limitations I'm putting together a little cheat sheet on how to clean dirty data using pandas in Jupyter notebook. 

##First steps - take a look at that data

It's all well and good saying we're going to clean dirty data but do we even know how it's dirty?
We need to eyeball that sucker and figure how how it looks. 

First thing we need to do is read our data into pandas and take a look for ourselves.

import pandas as pd

df = pd.read_csv('/user/home/test.csv')
df.head()

Take a good look at that data and figure out what values you were expecting and what looks unusual. This is a good time to pull out your data dictionary and start looking though your data. 

#Merging, joining and concatenating data



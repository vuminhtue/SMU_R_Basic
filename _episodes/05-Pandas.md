---
title: "Introduction to Pandas"
teaching: 20
exercises: 0
questions:
- "What is Pandas"
- "DataFrame in Python"
- "How to read text/csv file"
objectives:
- "Working with Data Frame"
- "Create data frame"
- "Import and export data frame"
- "Working with text/csv files"
keypoints:
- "Working with csv input data"
---

## Pandas
Pandas[https://pandas.pydata.org/] stands for "Python and data analysis" and "panel data".

- Pandas is an open source Python library specialize in data structures and operations for the manipulation of numerical tables and time series

- Pandas is a fast, powerful, flexible and easy to use open source data analysis and manipulation tool, it is built on top of the Python programming language.

- To use pandas, one needs to install and import it into the library

```python
!pip install pandas
import pandas as pd
```

There are 2 important data structure in pandas: (1) Series and (2) Data Frame

## Pandas Series

```python
s1 = pd.Series([0,1,2,3,4,5])
```

Pandas Series is a multidimensional numpy array with array index on the first column and array value on the second column.
To retrieve index and its values:

```python
s1.index
s1.values
type(s1.index)
type(s1.values)
```

The index can be changed for more meaning full Series:

```python
s1.index=['Tiger','Cow','Polar Bear','Mustang','Lion','Dragon']
s1
```

Or a new pandas Series can be created using index:

```python
s2 = pd.Series([1,2,3,4,5,6],index=s1.index)
s2
```

One can add the 2 Series if they have similar index:

```python
s3 = s1*s2
```

To access index of a Series:

```python
#Using index name
s3['Mustang']

#Using bracket
s3[3]

#Using index location
s3.iloc[3]

#Select subset of Series using filtering
s3[s3>20]
```

Insert an index value

```python
s3['Nothing']=np.nan
s3
```

Catch a null value

```python
s3.isnull()

#Assign null value with some numbers
s3[s3.isnull()]=1
# or
s3.fillna(1)
```

Drop null value (Remove entire row with NaN value)

```python
s3.dropna()
```

Apply function over all index of Series:

```python
s3.apply(np.log10)
```

For longer function, we can use lambda functions.
For example, if there are less than 20 available, we will add the value by 10:

```python
s3.apply(lambda x: x if x>20 else x+10)
```

## Pandas DataFrames
A DataFrame is considered a table of data (similar to a single Excel spreadsheet). It consists of many different pandas Series (as columns) that having the same index.
One should utilize the DataFrame when working with csv, table format.

A sample of DataFrame

```python
year = range(2015,2021)
Series1 = pd.Series([1,2,3,4,5,6],index=year)
Series10 = pd.Series([10,20,30,40,50,60],index=year)
Series100 = pd.Series([100,200,300,400,500,600],index=year)
df1 = pd.DataFrame([Series1,Series10,Series100],index=['N1','N10','N100'])
```

Another way to create a DataFrame using concat function

```python
df2 = pd.concat([Series1,Series10,Series100],axis=1)
df2.columns = (['N1','N2','N3'])
df2
```

Check the Data Type of the two DataFrame:

```python
type(df1)
type(df2)
```

Now let's read in some csv file.
The data is related to number of covid case in USA counted on Jan 1st, 2021. I have uploaded it to my account and opened access for everyone:

```python
df = pd.read_csv('https://raw.githubusercontent.com/vuminhtue/SMU_Python_Basic/master/data/1-1-21%20US%20covid19.csv')
df.head()
```

Some basic information from the DataFrame

```python
#Head & Tail of DataFrame with number of row to be printed:
df.head(6)
df.tail(6)

# The index of dataframe
df.index

# Set the index of dataframe
df.set_index(['State/Territory','Total Cases'])
df.reset_index
df = df.set_index('State/Territory')

# Sort the index
df.sort_index(ascending=True)

# Column Name of DataFrame
df.columns

# Transpose DataFrame
df.T

# Value of certain Series:
df['Total Cases']
df[['Total Cases','Confirmed Cases']]

# Print entire row value:
df.loc['Texas','South Carolina']
```

Index location
Similar to other programming language like Matlab or R using tabular data, pandas DataFrame also has ability to access the _index location_ using **iloc**:

```python
df.iloc[0,0]
df.iloc[0:2,0:5]
df.iloc[:3,3:]
```

Querying a DataFrame

```python
df['Total Cases']>50000
high_case = df[df['Total Cases']>50000]
high_case.head()
type(high_case)
```

Some Statistics with DataFrame

```python
# What is the max number of case
high_case['Total Cases'].max()

# What is the corresponding index (state) name? Using **idxmax** or **argmax**
high_case['Total Cases'].idxmax()

# Reveal all information on the state with max number of case
high_case.loc[high_case['Total Cases'].idxmax()]

# Drop by columns, axis=1
high_case.drop(['Total Cases','Case Rate per 100000'],axis=1)

# Drop by rows, axis=0
high_case.drop(['Alabama','Indiana'],axis=0)

# Find the State from the South with more than 40000 Total Cases:
df[(df['State Region']=='South') & (df['Total Cases']>40000)]
```

Create new colum named Ratio Death/Total Cases (%) and the value is division of 2 columns 'Total Deaths' by 'Total Cases'

```python
df['Ratio Death/Total Cases (%)']=df['Total Deaths']/df['Total Cases']*100
```

Splitting and Merging DataFrame

```python
#Splitting DataFrame
df1 = df.iloc[0:6,0:3]
df2 = df.iloc[2:8:,3:5]

# Merge Data Frame using pd.merge
df3_in = pd.merge(df1,df2,how='inner',left_index=True,right_index=True)
df3_out = pd.merge(df1,df2,how='outer',left_index=True,right_index=True)
df3_left = pd.merge(df1,df2,how='left',left_index=True,right_index=True)
df3_right = pd.merge(df1,df2,how='right',left_index=True,right_index=True)
```

Save DataFrame to new csv file

```python
df3_in.to_csv('df3.csv')
```

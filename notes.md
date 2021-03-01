[TOC]



## Lesson 1: Installing Pandas and Jupyter and Loading Data

Installation:

```bash
pip install pandas
pip install jupyterlab
```

Start a jupyter notebook:

```shell
jupyter notebook
```

```python
#importing pandas
import pandas as pd
#reading csv
df = pd.read_csv("file.csv")
#peeking into the data
df
#get a tuple of number of rows and columns
df.shape
#get number of rows, columns, datatype of each colums, object:strings, int64: integers, float64: floats
df.info()
#set the number of rows and colums jupyter shows when simply type the dataframe variable
pd.set_option("display.max_columns", 80)
pd.set_option("display.max_rows", 85)
#seeing first n number of rows in a df
df.head() #default 5
df.head(10)
#seeing last n number of rows in a df
df.tail() #default 5
df.tail(10)
```

***References:***

*Virtual Environment Tutorial - [https://youtu.be/Kg1Yvry_Ydk](https://www.youtube.com/watch?v=Kg1Yvry_Ydk&t=0s)* 

*Jupyter Tutorial - [https://youtu.be/HW29067qVWk](https://www.youtube.com/watch?v=HW29067qVWk&t=0s)* 

## Lesson 2: DataFrame and Series Basics - Selecting Rows and Columns

DataFrame: Two-dimensional, size-mutable, potentially heterogeneous tabular data. In layman's term, a collection of data formatted into rows and columns. 

Series: One-dimensional ndarray with axis labels. In layman's term, a collection data into many rows; but only one column. 

A nice way to visualize DataFrame is to look at it like a dictionary of lists.

```python
people = {
    "first": ["Corey", 'Jane', 'John'], 
    "last": ["Schafer", 'Doe', 'Doe'], 
    "email": ["CoreyMSchafer@gmail.com", 'JaneDoe@email.com', 'JohnDoe@email.com']
}
people["email"][2]
'JohnDoe@email.com'
```

to create a DataFrame from the dictionary:

```python
df = pd.DataFrame(people)
```

getting the email column:

```python
df["email"]
```

getting email and last name column:

```python
df[["email", "last"]]
```

to see a list of all the *column* in a DataFrame:

```python
df.columns
```

iloc, and loc methods can be used to get specific rows and columns from a data frame, ilock stand for integer location, thus used with integer indexes. loc takes labels, if there is no label it works like ilock, taking integers. 

iloc: getting the first first two rows and two columns :

```python
df.iloc[[0,1],[0,1]]
```

loc: getting first two rows and mail and last name column:

```python
df.loc[[0,1], ['email', 'last']]
```

loc: getting first 10 rows and all the columns from *a* to *x*:

```python
df.loc[0:9, a:x]
```

***notice: the last item in the slices for loc are inclusive, the slices are not provided as lists***

___

Now let's move to the big data set:

To get the value_counts of Hobbyist column in our dataset:

```python
df['Hobbyist'].value_counts()
```

## Lesson 3: Indexes - set, reset and use indexes

Setting a column 'email' as index for the data frame:

```python
df.set_index('email')
```

this however returns a DataFrame with 'email' as index. To make the change inplace:

```python
df.set_index('email', inplace=True)
```

Now we can use .loc to access the rows using the email index:

```python
df.loc['Corey@mail.com']
df.loc['Corey@mail.com', 'pay']
df.loc['Corey@mail.com', 'first':'pay']
```

To see all the indexes in a DataFrame:

```python
df.index
```

To reset the index to default:

```python
df.reset_index(inplace=True)
```

Setting index when a DataFrame is loaded from a file:

```python
df = pd.read_csv('file.csv', index_col='columnName')
```

to sort the indexes alphabetically:

```python
df.sort_index(inplace=True)
```

to sort the indexes in inverse order:

```python
df.sort_index(ascending=False, inplace=True)
```

to sort a Series in inverse by it's values:

```python
Series.sort_values(ascending=True)
```

## Lesson 4: Filtering - Using Conditionals to Filter Rows and Columns

Make a filter for people whose last name is 'Doe'

```python
filt = (df['last'] == 'Doe')
```

Different ways to apply a filter to DataFrame:

directly putting a filter

```python
df[df['last']=='Doe']
```

feeding the filter:

```python
df[filt]
```

**best way to feed a filter is to use .loc, this way we can access specific columns if we need to:**

```python
df.loc[filt]
df.loc[filt, 'email']
```

& | operators for and or:

Making a filter for last name is Doe **and** first name is John:

```python
filt = (df['last'] == 'Doe') & (df['first'] == 'John')
```

Filter for last name is Doe **or** first name is John:

```python
filt = (df['last'] == 'Doe') | (df['first'] == 'John')
```

Filter for people from India and with salary greater than 50000:

```python
filt = (df['Country'] == 'India') & (df['salary'] > 50000)
```

Getting the **Opposite** of a filter using **~**:

```python
df.loc[~filt]
```

**List/Multiple filter**:

Filter for people from India, United States, Canada, United Kingdom:

```python
countries = ['India', 'United States', 'Canada', 'United Kingdom']
filt_countries = (df['Country'].isin(countries))
```

**str method:**

Make a filter for people who know python setting na value to False:

```python
filt_python = (df['Language'].str.contains('Python', na=False))
```

## Lesson 5: Updating Rows and Columns - Modifying Data within DataFrame

Changing all the column names by passing a list:

```python
df.columns = ['first_name', 'last_name', 'email', 'pay']
```

by using list comprehension:

```python
df.columns = [cn.upper() for cn in df.columns]
```

using .str method

```python
df.columns = df.columns.str.replace(' ', '_')
```

 by using .rename() and passing a dic:

```python
df.rename(columns={'first_name':'first', 'last_name':'last'}, inplace=True)
```

Changing data of a specific cell:

by passing all the values in a row as a list:

```python
df.loc[2] = ['John', 'Smith', 'JohnSmith@email.com', 14000]
```

selecting specific values and passing values as a list:

```python
df.loc[2, ['first', 'pay']] = ['Alex', 15000]
```

changing one value:

```python
df.loc[2, 'last'] = 'Jannat'
```

the same can be done using .at:

```python
df.at[2, 'last'] = 'Jannat'
```

Changing all the rows 

```python
df['email'] = df['email'].str.lower()
```

****

**Always use one of these indexers .at .loc .iloc to change a specific value.***

The following will not work:

```python
df[filt]['last'] = 'Doe'
```

Use loc:

```python
df.loc[filt, 'last'] = 'Doe'
```

___

Four Methods of changing values:

#### **apply**

Applies a function to each value in a series:

```python
df['email'].apply(len)
df['pay'].apply(str).apply(len)
```

Applying your function:

```python
def int_len(num):
    return len(str(num))

df['pay'].apply(int_len)
```

Applying lambda function:

```python
df['pay'] = df['pay'].apply(lambda salary: salary*1.05)
```

***using apply on a DataFrame applies the function on each row or column of the DataFrame***

```python
df.apply(len) # gives length of each column
df.apply(len, axis=1) # give the length of each rows
```

finding the min or max values in a row or a column:

```python
df.apply(pd.Series.min)
df.apply(lambda x: x.min())
```



#### **applymap**

Applies a function to all the values(cells) in a DataFrame:

```python
df.applymap(len)
df.applymap(str).applymap(len)
```

#### **Substitute values in a series using map and replace**

**map**

Used when substituting all the values, will substitute values that are not provided NaN

```python
df['first'] = df['first'].map({'Anam':'Adam', 'Rik': 'Rick'})
```

**replace**

Used when substituting some values without touching the other:

```python
df['first'] = df['first'].replace({'Anam':'Adam'})
```

## Lesson 6: Add or Remove Rows and Columns from DataFrame

Adding column:

```python
df['newCol'] = ['Val1', 'Val2', 'Val3']
```

```python
df['full_name'] = df['first'] + " " + df['last']
```

Deleting a column:

```python
df.drop(columns='columnName', inplace=True)
```

Splitting a full name and adding to tow separate first and last name columns

```python
df[['first', 'last']] = df['full'].str.split(' ', expand=True)
```

Adding Row:

Adding a row using append method

```python
df.append({'first':'Jane', 'last':'Doe', 'pay':10000}, ignore_index=True)
```

Adding one DataFrame to another:

```python
df = df.append(df_anoterh, ignore_index=True)
```

Deleting Row:

```python
df.drop(3, inplace=True)
```

Deleting rows using conditionals:

```python
filt = (df['last'] == 'Doe')
df.drop(index=(df[filt].index, inplace=True)
```

## Lesson 7: Sorting Data

Sort by values of column 'last'

```python
df.sort_values(by='last')
df.sort_values(by='last', ascending=False, )
```

Sort by multiple columns

```python
df.sort_values(by=['last', 'first'], ascending=False)
```

Will be sorted with 'first' is 'last' has same value for two rows.

To use different orders for each column we are sorting by:

```python
df.sort_values(by=['last', 'first'], ascending=[False, True])
```

Reseting to the default with index sort:

```python
df.sort_index(inplace=True)
```

Sorting a series or column:

```python
df['last'].sort_values()
```

Sort df in ascending order by 'Country', and descending order by 'Salary':

```python
df.sort_values(by=['Country', 'Salary'], ascending=[True, False], inplace=True)
```

Seeing 50 largest values from 'Salary'

```python
df['Salary'].nlargest(50)
```

Getting all survey results for 50 people with highest salary

```python
df.nlargest(50, 'Salary')
```

getting all survey results for 50 people with lowest salary:

```python
df.nsmallest(50, 'Salary')
```

## Lesson 8: Grouping and Aggregating - Analyzing and Exploring Your Data

Data aggregating: mean, median, mode

To see the median value of the column 'Salary':

```python
df['Salary'].median()
```

Median of all the columns with numerical values in a DataFrame:

```python
df.median()
```

To see a statistical overview of all the columns with numerical values in DataFrame:

```python
df.describe()
```

Get a similar overview for a single column:

```python
df['Column'].describe()
```

To count the non NaN values in a column:

```python
df['Column'].count()
```

To count the number of time a specific value appears in column use value_counts method:

```python
df['Hobbyist'].value_counts()
```

To see the same result as percentage:

```python
df['Hobbyist'].value_counts(normalize=True)
```

Grouping Data:

Grouping does: split, apply function, combine results

Make a groupby object by 'Country':

```python
country_grp = df.groupby(['Country'])
```

See all the result from 'United States' group from the groupby object:

```python
country_grp.get_group('United States')
```

See the value counts of 'EdLevel' column grouped by 'Country'

```python
country_grp['EdLevel'].value_counts()
```

See the median of 'Salary' column grouped by 'Country' and sort in descending order:

```python
country_grp['Salary'].median().sort_values(ascending=False)
```

See the median and mode using agg method

```python
country_agg = df.groupby(['Country'])['Salary'].agg(['mean', 'median'])
```

Get the mean and median for 'India':

```python
country_agg.loc['India']
```

To see how many participants from India knew python with filter:

```python
filt = df['Country'] == 'India'
df.loc[filt]['LanguageWorkedWith'].str.contains('Python').sum()
```

Without filter

```python
country_grp = df.groupby(['Country'])
country_grp['LanguageWorkedWith'].apply(lambda x: x.str.contains('Python').sum())
```

make df_pctPy by concat-ing series knowPython and totRespondent:

```python
df_pctPy = pd.concat([knowPython, totRespondent], axis='columns')
```

Make a new column pctKnowsPython by dividing 'KnowsPython' column by 'TotalRespondents':

```python
df_pctPy['pctKnowsPyton'] = (df_pctPy['KnowsPython']/df_pctPy['TotalRepondents']) * 100
```

## Lesson 9: Cleaning Data- Casting Datatypes and Handling Missing Values

Drop rows with any missing value

```python
df.dropna() #which is default behaviour df.dropna(axis='index', how='any')
```

Drop rows with all values missing:

```python
df.dropna(axis='index', how='all')
```

Drop columns with all values missing:

```python
df.dropna(axis='columns', how='all')
```

Drop rows with missing value in 'email' column

```python
df.dropna(axis='index', how='any', subset=['email'])
```

Drop rows with missing both 'email' and 'last':

```python
df.dropna(axis='index', how='all', subset=['email', 'last'])
```

Drop rows with missing any of 'email', 'last', or 'pay':

```python
df.dropna(axis='index'), how='any', subset=['email', 'last', 'pay']
```

Handing custom na values such as string 'NA', 'Missing':

First, replace all 'NA' string with np.nan in df

```python
df.replace('NA', na.nan, inplace=True)
df.replace('Missing', na.nan, inplace=True)
```

See the entire df with bool values for na values

```python
df.isna()
```

Fill all the na values with 0

```python
df.fillna(0)
```

See datatypes of each column in df

```python
df.dtypes
```

Change data types of a column

```python
df['age'] = df['age'].astype(float)
```

Replace 'less than 1 year' with 0 in 'experience' column

```python
df['experience'].replace('less than 1 year', 0, inplace=True)
df['experience'].replace('more than 50 years'), 51, inplace=True)
```

Changing datatype of the column to float and get the median:

```python
df['experience'].astype(float).median()
```

Seeing all the unique values in a series

```python
df['EdLevel'].unique()
```

Passing a list of values to be treated as NaN values when loading a csv file:

```python
na_vals = ['NA', 'Missing', 'MISSING']
df = pd.read_csv('file.csv', na_values=na_vals)
```

## Lesson 10: Working with Dates and Time Series Data

Getting the day_name of a datetime in index 0 and column 'Date' of 'df'

```python
df.loc[0, 'Date'].day_name()
```

Converting 'Date' column to a datetime object assuming its string format is 2020-03-23 07-PM

```python
df['Date'] = pd.to_datetime(df['Date'], format='%Y-%m-%d %I-%p')
```

* * Reference for formatting str to date time: 
  * https://strftime.org/
  * https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior

Setting a column as date time object when loading a csv:

```python
import datetime
d_parser = lambda x: datetime.datetime.strptime(x, '%Y-%m-%d %I-%p')
df = read_csv('file.csv', parse_dates=['Date'], date_parser=d_parser)
```

 Getting a series of all the day of week names for 'Date' column

```python
df['Date'].apply(lambda x: x.strftime("%A"))
```

or

```python
df['Date'].dt.day_name()
```

Getting the latest and earliest dates using .min() on a datetime series

```python
df['Date'].min()
df['Date'].max()
```

Getting the timedelta between two dates:

```python
df['Date'].max() - df['Date'].min()
```

Creating a filter for datetime object where year is greater or equal to 2020:

```python
filt = (df['Date'] >= '2020')
df.loc[filt]
```

Creating a filter for datetime object for only the result of 2019

```python
filt = (df['Date'] >= '2019') & (df['Date'] < '2020')
```

Getting result from one specific day to another

```python
filt = (df['Date'] >= pd.to_datetime('2020-01-01')) & (df['Date'] < pd.to_datetime('2021-01-01'))
```

 We can use datetime object to index and directly use a year for filtering out data or slicing:

```python
df.set_index('Date', inplace=True)
df['2020-01':'2020-02']
```

* **Re-sampling**

Resampling 'High' column by Weekly and gettting max

```python
df['High'].resample('W').max()
```

For other date offseting: https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects

Using .agg method to resample a dataframe by passing a dictionary:

```python
def give_first(ser):
    return ser.iloc[0]
def give_last(ser):
    return ser.iloc[-1]
df_monthly = df.resample('M').agg({'Open':give_first, 'High':'max', 'Low':'min', 'Volume':'sum', 'Close':give_last, 'Adj Close':give_last })
```

## Lesson 11: Reading and Writing Data to Different Sources - Excel, JSON, SQL, etc

* **CSV:**

Reading:

```python
df = pd.read_csv('file.csv', index_col='ColName')
```

Writing:

```python
df.to_csv('modified.csv')
```

Reading and Writing to a tab delimited file:

```python
df = pd.read_csv('tab_delimited.csv', sep='\t')
df.to_csv('tab_delimited.csv', sep='\t')
#the separator can be anything; \t for tab
```

* **Excel**

To read and write excel file you need to pip install the following packages:

> xlwt to write to older xls
>
> xlrd to read older xls
>
> openpyxl  to read and write newer xlsx fromat

To write to an excel file

```python
df.to_excel('modified.xlsx')
```

***You can specify sheet names and many other things when reading and writing excel, search fro .to_excel in documentations***

To read

```python
frm_xl = pd.read_excel('modified.xlsx', index_col='Respondent')
```

* **JSON**

To write

```python
df.to_json('modified.json')
#to make it listlike
df.to_json('modified.json', orient='records', lines=True)
```

*You can do many other things with json here, check .to_json in documentations*

To read

```python
test_df = pd.read_json('modified.json', orient='records', lines=True)
```

* **SQL**

Watch the last video in the series to read and write SQL.

* **Loading from url**

```python
test_df = pd.read_csv('url')
```


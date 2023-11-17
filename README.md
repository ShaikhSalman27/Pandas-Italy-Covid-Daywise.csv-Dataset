# Pandas-Italy-Covid-Daywise.csv Dataset


import pandas as pd

covid_df = pd.read_csv('italy-covid-daywise.csv')
covid_df

type(covid_df)

covid_df.info()

covid_df.describe()

covid_df.columns

covid_df.shape

### Retrieving data from a data frame

# pandas formate similar to this

covid_data_dict = {
    'data' : ['2020-08-30', '2020-08-31', '2020-09-01', '2020-09-02', '2020-09-05'],
    'new_cases' : [1444, 1365, 996, 975, 1326],
    'new_deaths' : [1, 4, 6, 8, 6],
    'new_tests' : [53541, 42583, 53395, None, None]
}

covid_data_dict['new_cases']

covid_df['new_cases']

type(covid_df['new_cases'])

covid_df['new_cases'][246]

covid_df['new_tests'][240]

### .at

covid_df.at[246,'new_cases']

covid_df.at[240, 'new_tests']

# same as covid_df['new_cases']

covid_df.new_cases

### columns of list

cases_df = covid_df[['date', 'new_cases']]
cases_df

### Copy() method

covid_df_copy = covid_df.copy()
covid_df_copy

### Loc

covid_df.loc[243]

type(covid_df.loc[243])

covid_df.head(5)

covid_df.tail(4)

covid_df.at[0, 'new_tests']

covid_df.at[0, 'new_cases']

type(covid_df.at[0, 'new_tests'])

# we can find the first index that doesn't count a Nan value using first_valid_index method of a series.

covid_df.new_tests.first_valid_index()

covid_df.loc[108:115]

covid_df.new_cases.first_valid_index()

# the sample method can be used to retieve a random sample of rows from the data frame

covid_df.sample(6)

## Analyzing Data from data frames

# What is the total number of reported cases & deaths related to covid-19 in italy

total_cases = covid_df.new_cases.sum()
total_deaths = covid_df.new_deaths.sum()

print(f'the number of reported cases is {total_cases} and deaths is {total_deaths}')

# What is the overall death rate (ratio of reported deaths to reported cases) 

death_rate = covid_df.new_deaths.sum() / covid_df.new_cases.sum()

print(f'the overall death rate is {death_rate,}')

# what is the overall number of tests conduct. A total of 935310 tests were conduct before daily test numbers where being reported

intial_test = 935310
total_test = intial_test + covid_df.new_tests.sum()
total_test

# What fraction of test returned a positive result

positive_rate = total_cases / total_test

print(f'{positive_rate * 100}% of test in itally led to a positive diagnosis')

### Quering & sorting rows

high_new_cases = covid_df.new_cases > 1000
high_new_cases

covid_df[high_new_cases]

# Quering & sorting rows 
# ye upper vale dono se easy hai

high_new_cases_df = covid_df[covid_df.new_cases > 1000]
high_new_cases_df

from IPython.display import display 
with pd.option_context('display.max_rows', 100):
    display(covid_df[covid_df.new_cases > 1000])

# determine the days when the ratio of cases reported to tests conducted is higher than the overall positive rate

positive_rate

high_ratio_df = covid_df[covid_df.new_cases / covid_df.new_tests > positive_rate]
high_ratio_df

covid_df.new_cases / covid_df.new_tests

covid_df['positive_rate'] = covid_df.new_cases / covid_df.new_tests
covid_df

# remove the positive_rate columns using te drop method

covid_df.drop(columns = ["positive_rate"], inplace=True)
covid_df

# Highest cases maximum 10

covid_df.sort_values('new_cases', ascending=False).head(10)

covid_df.sort_values('new_deaths', ascending=False).head(10)

covid_df.sort_values('new_tests', ascending=False).head(10)

# check at the some days before and after 20'th june

covid_df.loc[169:175]

# the .at method can be used to modify a specific value within the data frame

covid_df.at[172, 'new_cases'] = (covid_df.at[171, 'new_cases'] + covid_df.at[172, 'new_cases']) / 2

covid_df.loc[172]

### pd.to_datetime

covid_df['date'] = pd.to_datetime(covid_df.date)
covid_df['date']

covid_df['year'] = pd.DatetimeIndex(covid_df.date).year
covid_df['month'] = pd.DatetimeIndex(covid_df.date).month
covid_df['day'] = pd.DatetimeIndex(covid_df.date).day
covid_df['weekday'] = pd.DatetimeIndex(covid_df.date).weekday

covid_df

# Query the rows for may

covid_df_may = covid_df[covid_df.month == 5]
covid_df_may

# Extract the subset of columns to be aggregate

covid_df_may_metrics = covid_df_may[["new_cases", "new_deaths", "new_tests"]]
covid_df_may_metrics

# Get the column wise some

covid_may_total = covid_df_may_metrics.sum()
covid_may_total

type(covid_may_total)

# the opration above also combine into a single statement

covid_df[covid_df.month == 5][['new_cases', 'new_deaths', 'new_tests']].sum()

# Overall average
covid_df.new_cases.mean()

# Average for sundaays
covid_df[covid_df.weekday == 6].new_cases.mean()

### Grouping & Aggregation

covid_month_df = covid_df.groupby('month')[['new_cases', 'new_deaths', 'new_tests']].sum()
covid_month_df

# Find april

covid_month_df.loc[4]

# Month mean()

covid_df_month_mean = covid_df.groupby('month')[['new_cases', 'new_deaths', 'new_tests']].mean()
covid_df_month_mean

covid_df_week_mean = covid_df.groupby('weekday')[['new_cases', 'new_deaths', 'new_tests']].mean()
covid_df_week_mean

### cumulative sum (cumsum)

covid_df['total_cases'] = covid_df.new_cases.cumsum()
covid_df['total_deaths'] = covid_df.new_deaths.cumsum()
covid_df['total_tests'] = covid_df.new_tests.cumsum() + intial_test

covid_df

## New CSV merge to old CSV

location_df = pd.read_csv('countries.csv')
location_df

location_df[location_df.name == "Italy"]

covid_df['location'] = 'Italy'
covid_df

### merged

merged_df = covid_df.merge(location_df, left_on="location", right_on="name")
merged_df

# we can now calculate metrics like cases per million, deaths per million & tests per million.

merged_df['case_per_million'] = merged_df.total_cases * 1e6 / merged_df.latitude
merged_df['deaths_per_million'] = merged_df.total_deaths * 1e6 / merged_df.latitude
merged_df['tests_per_million'] = merged_df.total_tests * 1e6 / merged_df.latitude

merged_df

merged_df.sample(20)

## Writing data back to files

result_df = merged_df[['date',
                      'new_cases',
                      'total_cases',
                      'new_deaths',
                      'total_tests',
                      'new_tests',
                      'total_tests',
                      'case_per_million',
                      'deaths_per_million',
                      'tests_per_million']]
result_df

### result_df write to .CSV

result_df.to_csv('results.csv', index=None)

## Basic plotting with pandas

import matplotlib.pyplot as plt
import seaborn as sns

# result_df.new_cases.plt();
# plt.result_df.new_cases.plot();

sns.histplot(x = 'new_cases', y = 'new_deaths', data=result_df)

result_df.set_index('date', inplace=True)
result_df

result_df.loc['2020-08-30']

result_df.new_cases.plot();

result_df.new_cases.plot()
result_df.new_deaths.plot();

result_df.total_cases.plot()
result_df.new_deaths.plot();

death_rate = result_df.new_deaths / result_df.total_cases
death_rate.plot(title = 'Death Rate');

positive_rates = result_df.new_cases / result_df.total_cases
positive_rates.plot(title = 'positive Rate');

covid_month_df

covid_month_df.new_cases.plot(kind='bar')

covid_month_df.new_deaths.plot(kind='bar')

covid_month_df.new_tests.plot(kind='bar')

covid_month_df.new_cases.plot(kind='hist')

covid_month_df.new_tests.plot(kind='box')

covid_month_df.new_cases.plot(kind='hist')


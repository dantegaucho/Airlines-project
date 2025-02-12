# Airline Passenger Satisfaction 
This dataset contains an airline passenger satisafaction survey. We are going to use this data for analysis, explore and draw insights for existing and new airlines yet to launched. 

## Objectives
We will be to:
* Read and understand the type and its content.
* Clean the data 
* Interpret descriptive statistics and data visualiations to present our findings.

**Answer the following questions:**

1. What factors are highly correlated to a satisfied (or dissatisfied) passenger? 
2. Which services have the greatest impact on passenger satisfaction?
3. Can you predict passenger satisfaction? 
   ![Riyadh_Air](Riyadh_air_white.jpg)

 ## Task: Analyze the correlation between Departure Delay / Arrival in minutes and passenger satisafaction

 ## Business Understanding

 ### Which services have the greatest impact on passenger satisfation?

 In the world of business today, it is all about competitiveness, creating and maintaining your brand and most importantly providing top-notch customer services. While this has been demonstrated by well-known companies around the world, every airline is up to task to delivery exceptional services to both local and international passengers. Currently, every airline hopes to go above and beyond passenger expectations, grow its brand worldwide and have as many destinations as possible.
  ![Boeing_787](Riyadh_Air_Boeing_787_at_Dubai_Airshow_2023.jpg) 

 We are going to look at factors that determine passenger satisfaction or dissatisfaction. 


 # Data Understanding
 
This dataset was first modified by John D [here](https://www.kaggle.com/datasets/johndddddd/customer-satisfaction) and later modified by TJ KLEIN [here](https://www.kaggle.com/datasets/teejmahal20/airline-passenger-satisfaction/data)

### Data Visualization
Finally, we will plot graphs for a better understanding and draw insights. Check [My_tableau](https://public.tableau.com/authoring/120224DM/Dashboard1#1) for more visualizations.

## Getting into the Data

Below we import all modules which will be used to read and explore the data.

```
# Import the necessary modules as their alias
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

```

## Load the file
```
df = pd.read_csv('train.csv', index= 0) 
df.head() # check the first five rows
```


```
# check also the last rows
df.tail()
```

## Explore the data
```
df.info()
```
```
# check the columns in the dataset
df.columns
```
```
# assert statements to check the data

assert df.columns[0] == 'id'
assert len(df.columns) == 26
```

## Check for duplicates and unwanted observations
Here we are going to check for duplicates and missing values

```
# check for duplicated values
df.duplicated()
```
### Handling Missing values

```
# check for missing values
df.isnull.any()
```
```
# count the number of missing values in each columns
df.isnull().sum()

#sort the missing values in descending values
df.isnull().sum().sort_values(ascending=False)
```
```
# check the number of unique values in each column
df.nunique()
```

```
# calculate the percentage of missing values
missing_values = np.round(((df.isnull().sum() / len(df)) * 100), 2)
missing_values
```

Preview the `df[Arrival Delay in Minutes]`
``` df['Arrival Delay in Minutes']
```
Then fill the missing values with the `fillna` method. `.fillna()` fills the missing values by propagating the last valid observation to the next valid.
```
# forward fill the missing values
df['Arrival Delay in Minutes'] = df['Arrival Delay in Minutes'].fillna(df['Arrival Delay in Minutes'].mean())
```
### Explore the factors that correlate with satisfaction

**find the correlation of the dataset**

``` # get the correlation
df.corr()
```
![correlation_matrix](images\correlation_matrix.png)


From the above table we can find that Departure Delay in Minutes is highly correlated with satisafaction.
We can draw a quick insight that it is one of the major causes of dissatisfaction.

## DATA visualization
Here we first a plot a heatmap to get a clear picture of correlation
```
plt.figure(figsize=(10, 6))
sns.heatmap(df.corr(), annot=True, cmap='coolwarm', cbar=True, fmt='.2f')
plt.title('Correlation Matrix')
plt.show()
```

### Inspect any outliers in the dataset
```
# plot a box plot of the `Departure Delay in Minutes` column
plt.figure(figsize=(8, 6))
sns.boxplot(x=df['Departure Delay in Minutes'], orient='h', color='blue')
plt.xlabel('Departure Delay in Minutes', fontsize=14)
plt.title('Box plot of Departure Delay in Minutes')

plt.tight_layout()
plt.show()
```


* The above plot indicates skewness to the right due extreme values(outliers)
* These outliers hinder our visualization therefore we have to remove them but first we have to locate them.

**Import additional modules** 
```
import os
from scipy import stats
from scipy.stats import norm
from matplotlib.cbook import boxplot_stats
```
```
stat = boxplot_stats(df['Departure Delay in Minutes'])
stat
```
[{'mean': 14.815618263012011,
  'iqr': 12.0,
  'cilo': -0.05844734191582151,
  'cihi': 0.05844734191582151,
  'whishi': 30,
  'whislo': 0,
  'fliers': array([ 43,  49,  52, ...,  47,  35, 110]),
  'q1': 0.0,
  'med': 0.0,
  'q3': 12.0}]

Identify the possible outliers
 ```
 q1 = df['Departure Delay in Minutes'].quantile(0.25) # first quartile value
q3 = df['Departure Delay in Minutes'].quantile(0.75) # third quartile value

iqr = q3 - q1 # interquartile range

# outliers
outliers = ((df['Departure Delay in Minutes'] < (q1 - 1.5 * iqr)) | (df['Departure Delay in Minutes'] > (q3 + 1.5 * iqr))) 
outliers
```
```
# number of outliers
outliers_list = list(outliers[outliers == True].index)
df.loc[outliers_list]
```
### *plot a bar chart of departure delay in minutes against satisfaction*

```
# plot the distribution of the `Departure Delay in Minutes` column
plt.figure(figsize=(10, 6)) 
sns.histplot(df['Departure Delay in Minutes'], kde=True, color='blue', bins=30)
plt.title('Distribution of Departure Delay in Minutes')
plt.xlabel('Departure Delay in Minutes')
plt.ylabel('Frequency')
plt.tight_layout()  
plt.show()
```
Create a pairplot to compare the relationship between Departure Delay in Minutes and Arrival Delay in Minutes
```
cols = ['Departure Delay in Minutes', 'Arrival Delay in Minutes']
plt.figure(figsize=(10, 6))
sns.pairplot(df[cols], kind='scatter', diag_kind='kde')
plt.title('Pairplot of Departure Delay in Minutes and Arrival Delay in Minutes')
plt.tight_layout()
plt.show()
```
* From the plot above we can see that many points are clustered near the zero in both diagrams which means many flights depart and arrive close to on time.

* The bottom-left scatter plot indicate a strong positive correlation between departure and arrival delays > meaning as the departure delay increases the arrival also increases.

* From the histograms there are small delays to very large delays.

### Plot the relationship between 'Departure Delay in Minutes' and 'satisfaction'
```
# plot the relationship between 'Departure Delay in Minutes' and 'satisfaction'
plt.figure(figsize=(10, 6))
sns.barplot(x='satisfaction', y='Departure Delay in Minutes', data=df, palette='viridis')
plt.title('Relationship between Departure Delay in Minutes and Satisfaction')
plt.xlabel('Satisfaction')
plt.ylabel('Departure Delay in Minutes')
plt.tight_layout()
plt.show()

# save the figure
plt.savefig('departure_delay_satisfaction.png')
```
**Interpretation:**
* Increased departure delays increased dissatifaction

### Let's see the relationship between 'Customer type' and 'satisfaction'
```
# plot the relationship between 'Customer type' and 'satisfaction'
plt.figure(figsize=(10, 6))
sns.countplot(x='Customer Type', hue='satisfaction', data=df, palette='viridis')
plt.title('Relationship between Customer Type and Satisfaction')
plt.xlabel('Customer Type')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```

**Interpretation**
1.  A good number of loyal customers are dissatisfied
2. A very low number of disloyal customers were satisfied and majority dissatisfied

### Check the relationship between Type of Travel and satisfaction
# plot the relationship between 'Type of Travel' and 'satisfaction'
```
plt.figure(figsize=(10, 6))
sns.countplot(x='Type of Travel', hue='satisfaction', data=df, palette='viridis')
plt.title('Relationship between Type of Travel and Satisfaction')
plt.xlabel('Type of Travel')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```
**Interpretation**
1. Passengers on business travel were more than those on personal travel
2. The number of business travel passengers are more than those on personal travel

### To have a clear information on satisfaction and dissatisfaction lets take a look foods and drinks served and each class in the dataset.

```
# plot the relationship between 'Class', 'food and drink' and 'satisfaction'
fig, ax = plt.subplots(1, 2 ,figsize=(10, 6))
sns.countplot(x= 'Food and drink', hue='satisfaction', data=df, palette='viridis', ax=ax[0])
ax[0].set_title('Relationship between Food and drink and Satisfaction')
ax[0].set_xlabel('Food and drink')
ax[0].set_ylabel('Count')


sns.countplot(x='Class', hue='satisfaction', data=df, palette='deep', ax=ax[1])
ax[1].set_title('Relationship between Class and Satisfaction')
ax[1].set_xlabel('Class')
ax[1].set_ylabel('Count')
plt.tight_layout()
plt.show()
```
**Interpretation**

* There is high dissatisfaction in this column
* The Eco class has a higher dissatisfaction than the Eco Plus class
* Business class  passengers are more satisfied

### Lets check satisfaction in the Gender column
```
# plot the relation between 'Gender' and 'satisfaction'
fig, ax = plt.subplots(figsize=(10, 6))
sns.countplot(x= 'Gender', hue='satisfaction', data=df, palette='deep', ax=ax)
plt.title('Relationship between Gender and Satisfaction')
plt.xlabel('Gender')
plt.ylabel('Count')
plt.tight_layout()
plt.show()
```
**Interpretation**
* There is slight higher disatisfaction than satisafction

### Finally, this plot will show us the satisfaction across 'Age' column

```
# plot the relationship between 'Age' and 'satisfaction'
bins = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
labels = ['0-10', '11-20', '21-30', '31-40', '41-50', '51-60', '61-70', '71-80', '81-90', '91-100']

df['AgeGroup'] = pd.cut(df['Age'], bins=bins, labels=labels)

plt.figure(figsize=(10, 6))
sns.histplot(x='AgeGroup', hue='satisfaction', data=df, palette='viridis', bins=bins, stat= 'probability')
plt.title('Relationship between Age Group and Satisfaction')
plt.xlabel('Age Group')
plt.ylabel('Count')

plt.tight_layout()
plt.show()
```
**Interpretation**

* There is significant dissatisfaction in the age category and majority are young and middle-aged passengers.
* Only a few in their 40's and 50's are satisfied.

### Find the percentage of satisfied and unsatisfied customers
# find the percentage of satisfied and unsatisfied customers
```
satisfied = df['satisfaction'].value_counts(normalize=True) * 100
satisfied
```

# Summary
We have seen majority of passengers are dissatisfied across all columns.
The main drivers of customer dissatisfaction are:
1. Departure delay
2. Arrival delay 
3. Food and drinks



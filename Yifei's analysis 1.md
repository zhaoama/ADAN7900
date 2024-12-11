```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import scipy as sp
sales_data = pd.read_csv('project.sales.cities.csv')
acs_data = pd.read_csv('project.acs.cities.csv')
print(sales_data.head())
print(acs_data.head())
```

              city  category  sale.dollars  sale.volume
    0  Cedar Falls      Misc        494165        27993
    1  Cedar Falls    Whisky       1568696        93104
    2  Cedar Falls       Rum        809757        57070
    3  Cedar Falls    Brandy         80056         4982
    4  Cedar Falls  Amaretto         21811         2194
           city  high.school  bachelor  unemployment   income  population  \
    0    Ackley         88.8      20.0           4.0  30790.0        1738   
    1  Ackworth         85.5      12.7           9.1  41250.0          66   
    2     Adair         96.6      19.0           6.8  32639.0         813   
    3      Adel         93.7      32.0           3.1  37130.0        4030   
    4     Afton         89.4      11.5           5.7  27457.0         967   
    
       pop.white  pop.black  pop.indian  pop.asian  pop.hawai  pop.other  \
    0       1715          0           0          0          0         15   
    1         66          0           0          0          0          0   
    2        788          4           1          4          0          3   
    3       3956         20           0          0          0          0   
    4        953          2           6          2          0          0   
    
       pop.multi  
    0          8  
    1          0  
    2         13  
    3         54  
    4          4  
    


```python
sales_trend = sales_data.groupby('category')[['sale.dollars', 'sale.volume']].sum().reset_index()
plt.figure(figsize=(12, 6))
sns.scatterplot(x='category', y='sale.dollars', data=sales_trend, color="blue", label="Sales ($)")
sns.lineplot(x='category', y='sale.dollars', data=sales_trend, color="red", label="Fitting Line ($)")
plt.xticks(rotation=45)
plt.title("Sales Trend by Category (Dollars)")
plt.xlabel("Alcohol Category")
plt.ylabel("Sales Amount ($)")
plt.legend()
plt.show()
```


    
![png](output_1_0.png)
    



```python
plt.figure(figsize=(12, 6))
sns.scatterplot(x='category', y='sale.volume', data=sales_trend, color="green", label="Sales Volume")
sns.lineplot(x='category', y='sale.volume', data=sales_trend, color="orange", label="Fitting Line (Volume)")
plt.xticks(rotation=45)
plt.title("Sales Trend by Category (Volume)")
plt.xlabel("Alcohol Category")
plt.ylabel("Sales Volume")
plt.legend()
plt.show()
```


    
![png](output_2_0.png)
    



```python
top_categories = sales_data.groupby('category')['sale.dollars'].sum().sort_values(ascending=False)
plt.figure(figsize=(15, 9))
top_categories.plot(kind='bar', color='purple')
bars = plt.bar(top_categories.index, top_categories.values)
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2,  
        bar.get_height(),  
        f"${bar.get_height():,.0f}",  
        ha='center',  
        va='bottom', 
        fontsize=10   
    )
plt.title("Top Alcohol Categories by Sales")
plt.xlabel("Alcohol Category")
plt.ylabel("Total Sales ($)")
plt.xticks(rotation=45)
plt.savefig("Top Cat.jpg", format="jpg")
plt.show()
```


    
![png](output_3_0.png)
    



```python
merged_data = pd.merge(sales_data, acs_data, on='city')
merged_data['per_capita_sales'] = merged_data['sale.dollars'] / merged_data['population']
top_cities = merged_data.groupby('city')['per_capita_sales'].sum().sort_values(ascending=False).head(10)
plt.figure(figsize=(15, 9))
top_cities.plot(kind='bar', color='teal')
bars = plt.bar(top_cities.index, top_cities.values)
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2,  
        bar.get_height(),  
        f"${bar.get_height():,.0f}",  
        ha='center',  
        va='bottom', 
        fontsize=10   
    )
plt.title("Top Cities by Per Capita Sales")
plt.xlabel("City")
plt.ylabel("Per Capita Sales ($)")
plt.xticks(rotation=45)
plt.show()
```


    
![png](output_4_0.png)
    



```python
from scipy.stats import linregress
plt.figure(figsize=(10, 6))
x = merged_data['unemployment']
y = merged_data['sale.dollars']
slope, intercept, r_value, p_value, std_err = linregress(x, y)
sns.regplot(
    x=x, 
    y=y, 
    data=merged_data, 
    scatter_kws={'alpha': 0.5}, 
    line_kws={'color': 'red'}
)

plt.title("Alcohol Consumption vs Unemployment Rate")
plt.xlabel("Unemployment Rate (%)")
plt.ylabel("Alcohol Consumption (Dollars)")
plt.show()
```


    
![png](output_5_0.png)
    



```python
plt.figure(figsize=(10, 6))
x = merged_data['income']
y = merged_data['sale.dollars']
sns.regplot(
    x=x, 
    y=y, 
    data=merged_data, 
    scatter_kws={'alpha': 0.5}, 
    line_kws={'color': 'green'}
)
plt.title("Alcohol Consumption vs Income")
plt.xlabel("Income ($)")
plt.ylabel("Alcohol Consumption (Dollars)")
plt.show()
```


    
![png](output_6_0.png)
    



```python
plt.figure(figsize=(10, 6))
sns.regplot(
    x='high.school', 
    y='sale.dollars', 
    data=merged_data, 
    scatter_kws={'alpha': 0.5}, 
    line_kws={'color': 'blue'}
)
plt.title("Alcohol Consumption vs High School Education Level")
plt.xlabel("High School Completion (%)")
plt.ylabel("Alcohol Consumption (Dollars)")
plt.show()

plt.figure(figsize=(10, 6))
sns.regplot(
    x='bachelor', 
    y='sale.dollars', 
    data=merged_data, 
    scatter_kws={'alpha': 0.5}, 
    line_kws={'color': 'orange'}
)
plt.title("Alcohol Consumption vs Bachelor Education Level")
plt.xlabel("Bachelor's Degree Completion (%)")
plt.ylabel("Alcohol Consumption (Dollars)")
plt.show()
```


    
![png](output_7_0.png)
    



    
![png](output_7_1.png)
    



```python
sales_data = sales_data[sales_data['sale.volume'] > 0].dropna(subset=['sale.dollars', 'sale.volume'])
sales_data['unit_price'] = sales_data['sale.dollars'] / sales_data['sale.volume']
sales_data = sales_data[~sales_data['unit_price'].isnull()]
sales_data = sales_data[sales_data['unit_price'].apply(lambda x: x != float('inf'))]
avg_unit_price = sales_data.groupby('category')['unit_price'].mean().sort_values(ascending=False)
plt.figure(figsize=(15, 9))
bars = plt.bar(avg_unit_price.index, avg_unit_price.values, color='orange')
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2, 
        bar.get_height(), 
        f"${bar.get_height():.2f}", 
        ha='center', 
        va='bottom', 
        fontsize=10
    )
plt.title("Alcohol Categories by Unit Price")
plt.xlabel("Alcohol Category")
plt.ylabel("Unit Price ($/volume)")
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig("Unit Price.jpg", format="jpg")
plt.show()
```


    
![png](output_8_0.png)
    



```python
merged_data = pd.merge(sales_data, acs_data, on='city')
top_cities = merged_data.groupby('city')['sale.dollars'].sum().sort_values(ascending=False).head(10)
plt.figure(figsize=(15, 9))
top_cities.plot(kind='bar', color='teal')
bars = plt.bar(top_cities.index, top_cities.values)
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2,  
        bar.get_height(),  
        f"${bar.get_height():,.0f}",  
        ha='center',  
        va='bottom', 
        fontsize=10   
    )
plt.title("Top Cities Sales")
plt.xlabel("City")
plt.ylabel("Sales ($)")
plt.xticks(rotation=45)
plt.savefig("Top City.jpg", format="jpg")
plt.show()
```


    
![png](output_9_0.png)
    



```python
top_cities = (
    merged_data.groupby('city')['per_capita_sales']
    .sum()
    .sort_values(ascending=False)
    .head(10)
)
top_cities_names = top_cities.index
df_top_cities = merged_data[merged_data['city'].isin(top_cities_names)]
pivot_df = df_top_cities.pivot_table(
    index='city', 
    columns='category', 
    values='per_capita_sales', 
    aggfunc='sum', 
    fill_value=0
)
pivot_df = pivot_df.loc[top_cities_names]
pivot_df.plot(kind='bar', stacked=True, figsize=(15, 9), cmap='tab10')
plt.title('Per Capita Alcohol Sales by Category in Top Cities', fontsize=14)
plt.ylabel('Per Capita Sales ($)', fontsize=12)
plt.xlabel('City', fontsize=12)
plt.xticks(rotation=45, fontsize=10)
plt.legend(title='Category', bbox_to_anchor=(1.05, 1), loc='upper left', fontsize=10)
plt.tight_layout()
plt.savefig("Top Cities by Per Capita Sales.jpg", format="jpg")
plt.show()

```


    
![png](output_10_0.png)
    



```python
merged_data['price_per_unit'] = merged_data['sale.dollars'] / merged_data['sale.volume']
bins = [0, 20, 40, 60,80, 100]
labels = ['0-20%', '20-40%', '40-60%', '60-80%','80%-100%']
merged_data['bachelor_group'] = pd.cut(merged_data['bachelor'], bins=bins, labels=labels, include_lowest=True)
plt.figure(figsize=(15, 9))
sns.scatterplot(
    data=merged_data, 
    x='income', 
    y='price_per_unit', 
    hue='high.school',
    style='bachelor_group', 
    palette='coolwarm'
)
sns.regplot(
    data=merged_data, 
    x='income', 
    y='price_per_unit', 
    scatter=False, 
    color='black', 
    ci=None
)
plt.title("Relationship Between Income and Price Per Unit by High School Education Level", fontsize=14)
plt.xlabel("Income ($)", fontsize=12)
plt.ylabel("Price Per Unit ($)", fontsize=12)
plt.legend(title="High School Education Levels")
plt.tight_layout()
plt.savefig("Relationships-income,education,unitprice.jpg", format="jpg")
plt.show()

```


    
![png](output_11_0.png)
    



```python
top_cities = (
    merged_data.groupby('city')['sale.dollars']
    .sum()
    .sort_values(ascending=False)
    .head(10)
)
top_cities_names = top_cities.index
df_top_cities = merged_data[merged_data['city'].isin(top_cities_names)]
pivot_df = df_top_cities.pivot_table(
    index='city', 
    columns='category', 
    values='sale.dollars', 
    aggfunc='sum', 
    fill_value=0
)
pivot_df = pivot_df.loc[top_cities_names]
pivot_df.plot(kind='bar', stacked=True, figsize=(15, 9), cmap='tab10')
plt.title('Alcohol Sales by Category in Top Cities', fontsize=14)
plt.ylabel('Total Sales ($)', fontsize=12)
plt.xlabel('City', fontsize=12)
plt.xticks(rotation=45, fontsize=10)
plt.legend(title='Category', bbox_to_anchor=(1.05, 1), loc='upper left', fontsize=10)
plt.tight_layout()
plt.show()

```


    
![png](output_12_0.png)
    



```python

```

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import numpy as np
import scipy as sp
data = pd.read_csv('cities_data.clean.2.csv')
print(data.head())
```

       Unnamed: 0         city  high.school  bachelor  unemployment  income  \
    0           1  Cedar Falls         95.6      43.4           4.7   22434   
    1           2       Gowrie         91.1      21.6           8.2   30643   
    2           3     Waterloo         87.5      22.0           8.3   29201   
    3           4   West Union         92.5      26.0           6.2   26134   
    4           5     Rockwell         92.5      11.8           3.1   33281   
    
       population  pop.white  pop.black  pop.indian  pop.asian  pop.hawai  \
    0       40828      37938        939         120       1192         45   
    1        1047        977          3           0          6          0   
    2       68357      52104      10744         270       1346        254   
    3        2592       2413        116          20         37          0   
    4        1100       1062         32           0          0          0   
    
       pop.other  pop.multi  income_per_capita  prop_white  prop_black  \
    0        158        436           0.549476   92.921524    2.299892   
    1          0         61          29.267431   93.314231    0.286533   
    2       1670       1969           0.427184   76.223357   15.717483   
    3          0          6          10.082562   93.094136    4.475309   
    4          0          6          30.255455   96.545455    2.909091   
    
       total.sale  total.volume      price  
    0     4943947        339266  14.572480  
    1       86254          6546  13.176596  
    2     9631196        627241  15.354857  
    3      267066         22291  11.980889  
    4       91209          7519  12.130469  
    


```python
data=df
df['race_category'] = df[['prop_white', 'pop.black', 'pop.indian', 'pop.asian', 'pop.other','pop.hawai','pop.multi']].idxmax(axis=1)
plt.figure(figsize=(10, 6))
sns.scatterplot(
    x='income',
    y='total.sale',
    hue='bachelor',
    style='race_category',
    size='bachelor',
    data=data,
    palette='coolwarm',
    sizes=(50, 200)
)
plt.title("Alcohol Consumption vs Bachelor Education Level by Race Group")
plt.xlabel("Income (Dollars)")
plt.ylabel("Alcohol Consumption (Dollars)")
plt.legend(title="Education & Racial Group")
plt.savefig("Alcohol Consumption vs Bachelor Education Level by Race Group.jpg", format="jpg")
plt.show()
```


    
![png](output_1_0.png)
    



```python
df=data
df['per_capita_sales'] = df['total.sale'] / df['population']
per_capita_by_race = {
    'White': (df['per_capita_sales'] * df['pop.white']).sum() / df['pop.white'].sum(),
    'Black': (df['per_capita_sales'] * df['pop.black']).sum() / df['pop.black'].sum(),
    'Indian': (df['per_capita_sales'] * df['pop.indian']).sum() / df['pop.indian'].sum(),
    'Asian': (df['per_capita_sales'] * df['pop.asian']).sum() / df['pop.asian'].sum(),
    'Hawaiian': (df['per_capita_sales'] * df['pop.hawai']).sum() / df['pop.hawai'].sum(),
    'Other': (df['per_capita_sales'] * df['pop.other']).sum() / df['pop.other'].sum(),
    'Multi': (df['per_capita_sales'] * df['pop.multi']).sum() / df['pop.multi'].sum()
}
per_capita_df = pd.DataFrame(list(per_capita_by_race.items()), columns=['Race', 'Per Capita Sales'])
per_capita_df = per_capita_df.sort_values(by='Per Capita Sales', ascending=False)
plt.figure(figsize=(10, 6))
bars = plt.bar(per_capita_df['Race'], per_capita_df['Per Capita Sales'])
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2,  
        bar.get_height(),  
        f"${bar.get_height():,.0f}",  
        ha='center',  
        va='bottom',  
        fontsize=10   
    )
plt.title('Per Capita Alcohol Sales by Race Group', fontsize=14)
plt.xlabel('Race Group', fontsize=12)
plt.ylabel('Per Capita Sales ($)', fontsize=12)
plt.xticks(rotation=45, fontsize=10)
plt.tight_layout()
plt.show()
```


    
![png](output_2_0.png)
    



```python
data=df
per_capita_by_race_income = {
    'White': (df['income'] * df['pop.white']).sum() / df['pop.white'].sum(),
    'Black': (df['income'] * df['pop.black']).sum() / df['pop.black'].sum(),
    'Indian': (df['income'] * df['pop.indian']).sum() / df['pop.indian'].sum(),
    'Asian': (df['income'] * df['pop.asian']).sum() / df['pop.asian'].sum(),
    'Hawaiian': (df['income'] * df['pop.hawai']).sum() / df['pop.hawai'].sum(),
    'Other': (df['income'] * df['pop.other']).sum() / df['pop.other'].sum(),
    'Multi': (df['income'] * df['pop.multi']).sum() / df['pop.multi'].sum()
}
per_capita_income_df = pd.DataFrame(list(per_capita_by_race_income.items()), columns=['Race', 'Per Capita Income'])
per_capita_income_df = per_capita_income_df.sort_values(by='Per Capita Income', ascending=False)
plt.figure(figsize=(10, 6))
bars = plt.bar(per_capita_income_df['Race'], per_capita_income_df['Per Capita Income'])
for bar in bars:
    plt.text(
        bar.get_x() + bar.get_width() / 2,  
        bar.get_height(),  
        f"${bar.get_height():,.0f}", 
        ha='center',  
        va='bottom',  
        fontsize=10   
    )
plt.title('Per Capita Income by Race Group', fontsize=14)
plt.xlabel('Race Group', fontsize=12)
plt.ylabel('Per Capita Income ($)', fontsize=12)
plt.xticks(rotation=45, fontsize=10)
plt.tight_layout()
plt.savefig("Per Capita Income by Race Group.jpg", format="jpg")
plt.show()

```


    
![png](output_3_0.png)
    



```python

```


```python

```

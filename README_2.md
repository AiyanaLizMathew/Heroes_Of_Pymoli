
# Heroes Of Pymoli- File 2

### Observation Trends
#### Observation 1: The trends shows that more Male players has played the game than Females and other genders.
#### Observation 2: The trends shows that this game was more popular in the Age Group 20-24.
#### Observation 3: Also the most profitable item and most popular item are the same which is Mourning Blade.



```python
#Import Dependencies
import pandas as pd
import os
```

### Reading the contents of the file


```python
#File path
filepath1=os.path.join("Resources","purchase_data2.json")
#Open and read the file
Heroes_Pymoli_DF=pd.read_json(filepath1)

```

## Player Count


```python
#Find total number of players and store in a Data Frame
Total_players_Df=pd.DataFrame({"Total Players": [len(Heroes_Pymoli_DF["SN"].unique())]})
Total_players_Df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>74</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Total)


```python
#Purchasing Analysis

#Calculating the values and storing to lists
Unique_items=len(Heroes_Pymoli_DF["Item ID"].unique())
Avg_Purchase_Price=round(Heroes_Pymoli_DF["Price"].mean(),2)
No_of_Purchases=len(Heroes_Pymoli_DF["Price"])
Total_Revenue=round(Heroes_Pymoli_DF["Price"].sum(),2)

#Creating the Purchasing Analysis Dataframe
Purchasing_Analysis_Df=pd.DataFrame({"Number of Unique Items":[Unique_items],
                                     "Average Price":"$"+str(Avg_Purchase_Price),
                                     "Number of Purchases":[No_of_Purchases],
                                     "Total Revenue":"$"+str(Total_Revenue)})

Purchasing_Analysis_Df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.92</td>
      <td>78</td>
      <td>64</td>
      <td>$228.1</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
#Gender Demographics

#Grouping based on gender and SN(username)
Grouped_Gender_count=Heroes_Pymoli_DF.groupby(["Gender","SN"],as_index=False).count()

#Counting the Gender on the grouped data
Gender_count=Grouped_Gender_count["Gender"].value_counts()
#Calculating the gender percentage
Gender_percentage=Gender_count*100/Gender_count.sum()

#Creating the Gender Demographics DataFrame
Gender_Demographics_Df=pd.DataFrame({"Percentage of Players":Gender_percentage,"Total count":Gender_count})

#Formatting the columns to the desired format
Gender_Demographics_Df["Percentage of Players"]=Gender_Demographics_Df["Percentage of Players"].map("{:.2f}%".format)

Gender_Demographics_Df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.08%</td>
      <td>60</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.57%</td>
      <td>13</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.35%</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)


```python
#Purchasing Analysis based on Gender

Purchase_Count=Heroes_Pymoli_DF.groupby(["Gender"],as_index=False)["Price"].count()
Purchase_Count=Purchase_Count.rename(columns={"Price":"Purchase Count"})

Average_Purchase_Price_G=Heroes_Pymoli_DF.groupby(["Gender"],as_index=False)["Price"].mean()
Average_Purchase_Price_G=Average_Purchase_Price_G.rename(columns={"Price":"Average Purchase Price"})

Total_Purchase_Value=Heroes_Pymoli_DF.groupby(["Gender"],as_index=False)["Price"].sum()
Total_Purchase_Value=Total_Purchase_Value.rename(columns={"Price":"Total Purchase Value"})

#Merging the three columns to a final dataframe
Purchasing_Analysis_Gender_Df=pd.merge(Purchase_Count,Average_Purchase_Price_G,on="Gender")
Purchasing_Analysis_Gender_Df=Purchasing_Analysis_Gender_Df.merge(Total_Purchase_Value,on="Gender")

#Setting the index to Gender
Purchasing_Analysis_Gender_Df.set_index("Gender",inplace=True)
#Creating a temporary dataframe joining Gender_Demographics_Df and Purchasing_Analysis_Gender_Df
Temp_DF=Purchasing_Analysis_Gender_Df.join(Gender_Demographics_Df)

#Inserting the fourth column to the final dataframe
Purchasing_Analysis_Gender_Df["Normalized Totals"]=round(Temp_DF["Total Purchase Value"]/Temp_DF["Total count"],2)

#Deleting the temporary dataframe
del Temp_DF

#Formatting the columns
Purchasing_Analysis_Gender_Df["Average Purchase Price"]=Purchasing_Analysis_Gender_Df["Average Purchase Price"].map("${:.2f}".format)
Purchasing_Analysis_Gender_Df["Total Purchase Value"]=Purchasing_Analysis_Gender_Df["Total Purchase Value"].map("${:.2f}".format)
Purchasing_Analysis_Gender_Df["Normalized Totals"]=Purchasing_Analysis_Gender_Df["Normalized Totals"].map("${:.2f}".format)

Purchasing_Analysis_Gender_Df

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>13</td>
      <td>$3.18</td>
      <td>$41.38</td>
      <td>$3.18</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>64</td>
      <td>$2.88</td>
      <td>$184.60</td>
      <td>$3.08</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1</td>
      <td>$2.12</td>
      <td>$2.12</td>
      <td>$2.12</td>
    </tr>
  </tbody>
</table>
</div>



## Age Demographics


```python
#Age Demographics
bins=[0,9,14,19,24,29,34,39,100]
group_names=["< 10","10-14","15-19","20-24","25-29","30-34","35-39","40+"]

#Cutting based on the bin values and appending a new column to Heroes_Pymoli_DF
Heroes_Pymoli_DF["Age Group"]=pd.cut(Heroes_Pymoli_DF["Age"],bins,labels=group_names)

#Grouping based on the Age group and SN(Single User)
Age_Demographics=Heroes_Pymoli_DF.groupby(["Age Group","SN"])["Age"].count().to_frame()
#Resetting the index so as to make only Age Group as Index and not SN
Age_Demographics.reset_index(inplace=True)
#Setting the index to Age Group and count how many users plays
Age_Demographics=Age_Demographics.groupby(["Age Group"])["SN"].count().to_frame()
#Renaming the SN column as the Total count of player under that Age Group
Age_Demographics=Age_Demographics.rename(columns={"SN":"Total Count"})

#Calculating and adding the percentage of Players to the dataframe
Age_Demographics["Percentage of Players"]=(Age_Demographics["Total Count"]/Age_Demographics["Total Count"].sum())*100

#Formatting the columns
Age_Demographics["Percentage of Players"]=Age_Demographics["Percentage of Players"].map("{:.2f}%".format)

Age_Demographics

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 10</th>
      <td>5</td>
      <td>6.76%</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>3</td>
      <td>4.05%</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>11</td>
      <td>14.86%</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>34</td>
      <td>45.95%</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>8</td>
      <td>10.81%</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>6</td>
      <td>8.11%</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>6</td>
      <td>8.11%</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1</td>
      <td>1.35%</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)


```python
#Purchasing Analysis based on Age

Purchase_Count_Age=Heroes_Pymoli_DF.groupby(["Age Group"],as_index=False)["Price"].count()
Purchase_Count_Age=Purchase_Count_Age.rename(columns={"Price":"Purchase Count"})

Average_Purchase_Price_Age=Heroes_Pymoli_DF.groupby(["Age Group"],as_index=False)["Price"].mean()
Average_Purchase_Price_Age=Average_Purchase_Price_Age.rename(columns={"Price":"Average Purchase Price"})

Total_Purchase_Value_Age=Heroes_Pymoli_DF.groupby(["Age Group"],as_index=False)["Price"].sum()
Total_Purchase_Value_Age=Total_Purchase_Value_Age.rename(columns={"Price":"Total Purchase Value"})

#Merging the three columns to a final table
Purchasing_Analysis_Age_Df=pd.merge(Purchase_Count_Age,Average_Purchase_Price_Age,on="Age Group")
Purchasing_Analysis_Age_Df=Purchasing_Analysis_Age_Df.merge(Total_Purchase_Value_Age,on="Age Group")

#Setting the index to Gender
Purchasing_Analysis_Age_Df.set_index("Age Group",inplace=True)
#Creating a temporary dataframe joining Purchasing_Analysis_Age_Df and Age_Demographics
Temp_DF=Purchasing_Analysis_Age_Df.join(Age_Demographics)
#Inserting the fourth column to the final dataframe
Purchasing_Analysis_Age_Df["Normalized Totals"]=round(Temp_DF["Total Purchase Value"]/Temp_DF["Total Count"],2)

#Deleting the temporary dataframe
del Temp_DF

#Formatting the columns
Purchasing_Analysis_Age_Df["Average Purchase Price"]=Purchasing_Analysis_Age_Df["Average Purchase Price"].map("${:.2f}".format)
Purchasing_Analysis_Age_Df["Total Purchase Value"]=Purchasing_Analysis_Age_Df["Total Purchase Value"].map("${:.2f}".format)
Purchasing_Analysis_Age_Df["Normalized Totals"]=Purchasing_Analysis_Age_Df["Normalized Totals"].map("${:.2f}".format)

Purchasing_Analysis_Age_Df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Group</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt; 10</th>
      <td>5</td>
      <td>$2.76</td>
      <td>$13.82</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>3</td>
      <td>$2.99</td>
      <td>$8.96</td>
      <td>$2.99</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>11</td>
      <td>$2.76</td>
      <td>$30.41</td>
      <td>$2.76</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>36</td>
      <td>$3.02</td>
      <td>$108.89</td>
      <td>$3.20</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>9</td>
      <td>$2.90</td>
      <td>$26.11</td>
      <td>$3.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>7</td>
      <td>$1.98</td>
      <td>$13.89</td>
      <td>$2.32</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>6</td>
      <td>$3.56</td>
      <td>$21.37</td>
      <td>$3.56</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1</td>
      <td>$4.65</td>
      <td>$4.65</td>
      <td>$4.65</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
#Top Spenders

#Grouping based on users
Top_Spenders=Heroes_Pymoli_DF.groupby(Heroes_Pymoli_DF["SN"], as_index = False)

Purchase_Counts_Top=Top_Spenders["Price"].count().reset_index(drop=True)
Purchase_Counts_Top=Purchase_Counts_Top.rename(columns={"Price":"Purchase Count"})

Total_Purchase_Value_Top=Top_Spenders["Price"].sum().sort_values("Price",ascending=False).head().reset_index(drop=True)
Total_Purchase_Value_Top=Total_Purchase_Value_Top.rename(columns={"Price":"Total Purchase Value"})

#Merging the two columns to a final dataframe
Top_5_Spenders_DF=Total_Purchase_Value_Top.merge(Purchase_Counts_Top,on="SN",how="left")

#Inserting the third column to the final dataframe
Top_5_Spenders_DF["Average Purchase Price"]=(Top_5_Spenders_DF["Total Purchase Value"]/Top_5_Spenders_DF["Purchase Count"]).map("${:.2f}".format)

#Formatting the columns to desired format
Top_5_Spenders_DF["Total Purchase Value"]=Top_5_Spenders_DF["Total Purchase Value"].map("${:.2f}".format)
#Setting the index of final dataframe
Top_5_Spenders_DF.set_index("SN",inplace=True)
#Re-arranging the columns of final dataframe
Top_5_Spenders_DF=Top_5_Spenders_DF[["Purchase Count","Average Purchase Price","Total Purchase Value"]]

Top_5_Spenders_DF

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Sundaky74</th>
      <td>2</td>
      <td>$3.71</td>
      <td>$7.41</td>
    </tr>
    <tr>
      <th>Aidaira26</th>
      <td>2</td>
      <td>$2.56</td>
      <td>$5.13</td>
    </tr>
    <tr>
      <th>Eusty71</th>
      <td>1</td>
      <td>$4.81</td>
      <td>$4.81</td>
    </tr>
    <tr>
      <th>Chanirra64</th>
      <td>1</td>
      <td>$4.78</td>
      <td>$4.78</td>
    </tr>
    <tr>
      <th>Alarap40</th>
      <td>1</td>
      <td>$4.71</td>
      <td>$4.71</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
#Most Popular Items
#Grouping based on Item ID and Item Name
Most_Popular_Item=Heroes_Pymoli_DF.groupby(["Item ID","Item Name"], as_index = False)

#Finding the most popular item from the grouped dataframe by sorting the count of purchased items in the descending order and retturning the first 5 rows
Most_Popular_Item_DF=Most_Popular_Item["Price"].count().sort_values("Price",ascending=False).head().reset_index(drop=True)
Most_Popular_Item_DF=Most_Popular_Item_DF.rename(columns={"Price":"Purchase Count"})

Item_Price=Most_Popular_Item["Price"].mean()

#Merging the two columns to a final dataframe
Most_Popular_Item_DF=Most_Popular_Item_DF.merge(Item_Price,on=["Item ID","Item Name"],how="left")

Most_Popular_Item_DF=Most_Popular_Item_DF.rename(columns={"Price":"Item Price"})
Most_Popular_Item_DF["Total Purchase Value"]=Most_Popular_Item_DF["Purchase Count"]*Most_Popular_Item_DF["Item Price"]

#Setting the index of final dataframe
Most_Popular_Item_DF.set_index(["Item ID","Item Name"],inplace=True)
#Formatting the columns to desired format
Most_Popular_Item_DF["Item Price"]=Most_Popular_Item_DF["Item Price"].map("${:.2f}".format)
Most_Popular_Item_DF["Total Purchase Value"]=Most_Popular_Item_DF["Total Purchase Value"].map("${:.2f}".format)

Most_Popular_Item_DF

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>94</th>
      <th>Mourning Blade</th>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>90</th>
      <th>Betrayer</th>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>111</th>
      <th>Misery's End</th>
      <td>2</td>
      <td>$1.79</td>
      <td>$3.58</td>
    </tr>
    <tr>
      <th>64</th>
      <th>Fusion Pummel</th>
      <td>2</td>
      <td>$2.42</td>
      <td>$4.84</td>
    </tr>
    <tr>
      <th>154</th>
      <th>Feral Katana</th>
      <td>2</td>
      <td>$4.11</td>
      <td>$8.22</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
#Most Profitable Item
#Grouping based on Item ID and Item Name
Most_Profitable_Item=Heroes_Pymoli_DF.groupby(["Item ID","Item Name"], as_index = False)

#Finding the most profitable item from the grouped dataframe by sorting in the sum of Price in the descending order and returning the first 5 rows
Most_Profitable_Item_DF=Most_Profitable_Item["Price"].sum().sort_values("Price",ascending=False).head().reset_index(drop=True)

Item_Price=Most_Profitable_Item["Price"].mean()

#Merging the two columns to a final dataframe
Most_Profitable_Item_DF=Most_Profitable_Item_DF.merge(Item_Price,on=["Item ID","Item Name"],how="left")

Purchase_Count=Most_Profitable_Item["Price"].count()
Most_Profitable_Item_DF=Most_Profitable_Item_DF.merge(Purchase_Count,on=["Item ID","Item Name"],how="left")


#Setting the index of final dataframe
Most_Profitable_Item_DF.set_index(["Item ID","Item Name"],inplace=True)
#Renaming and Formatting the columns to desired format
Most_Profitable_Item_DF=Most_Profitable_Item_DF.rename(columns={"Price_x":"Total Purchase Value",
                                                                "Price_y":"Item Price",
                                                                "Price":"Purchase Count"})
Most_Profitable_Item_DF["Item Price"]=Most_Profitable_Item_DF["Item Price"].map("${:.2f}".format)
Most_Profitable_Item_DF["Total Purchase Value"]=Most_Profitable_Item_DF["Total Purchase Value"].map("${:.2f}".format)
#Re-arranging the columns in  the data frame
Most_Profitable_Item_DF=Most_Profitable_Item_DF[["Purchase Count","Item Price","Total Purchase Value"]]

Most_Profitable_Item_DF

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>94</th>
      <th>Mourning Blade</th>
      <td>3</td>
      <td>$3.64</td>
      <td>$10.92</td>
    </tr>
    <tr>
      <th>117</th>
      <th>Heartstriker, Legacy of the Light</th>
      <td>2</td>
      <td>$4.71</td>
      <td>$9.42</td>
    </tr>
    <tr>
      <th>93</th>
      <th>Apocalyptic Battlescythe</th>
      <td>2</td>
      <td>$4.49</td>
      <td>$8.98</td>
    </tr>
    <tr>
      <th>90</th>
      <th>Betrayer</th>
      <td>2</td>
      <td>$4.12</td>
      <td>$8.24</td>
    </tr>
    <tr>
      <th>154</th>
      <th>Feral Katana</th>
      <td>2</td>
      <td>$4.11</td>
      <td>$8.22</td>
    </tr>
  </tbody>
</table>
</div>



---

_This notebook is based on materials from Coursera course [Introduction to data science with python](https://www.coursera.org/learn/python-data-analysis/)._

---

# Lab 3 - More Pandas
This assignment requires more individual learning then the last one did - you are encouraged to check out the [pandas documentation](http://pandas.pydata.org/pandas-docs/stable/) to find functions or methods you might not have used yet, or ask questions on [Stack Overflow](http://stackoverflow.com/) and tag them as pandas and python related. And of course, the discussion forums are open for interaction with your peers and the course staff.

### Question 1
Load the energy data from the file `Energy Indicators.xls`, which is a list of indicators of [energy supply and renewable electricity production](Energy%20Indicators.xls) from the [United Nations](http://unstats.un.org/unsd/environment/excel_file_tables/2013/Energy%20Indicators.xls) for the year 2013, and should be put into a DataFrame with the variable name of **energy**.

Keep in mind that this is an Excel file, and not a comma separated values file. Also, make sure to exclude the footer and header information from the datafile. The first two columns are unneccessary, so you should get rid of them, and you should change the column labels so that the columns are:

`['Country', 'Energy Supply', 'Energy Supply per Capita', '% Renewable']`

Convert `Energy Supply` to gigajoules (there are 1,000,000 gigajoules in a petajoule). For all countries which have missing data (e.g. data with "...") make sure this is reflected as `np.NaN` values.

Rename the following list of countries (for use in later questions):

```"Republic of Korea": "South Korea",
"United States of America": "United States",
"United Kingdom of Great Britain and Northern Ireland": "United Kingdom",
"China, Hong Kong Special Administrative Region": "Hong Kong"```

There are also several countries with numbers and/or parenthesis in their name. Be sure to remove these, 

e.g. 

`'Bolivia (Plurinational State of)'` should be `'Bolivia'`, 

`'Switzerland17'` should be `'Switzerland'`.

Next, load the GDP data from the file `world_bank.csv`, which is a csv containing countries' GDP from 1960 to 2015 from [World Bank](http://data.worldbank.org/indicator/NY.GDP.MKTP.CD). Call this DataFrame **GDP**. 

Make sure to skip the header, and rename the following list of countries:

```"Korea, Rep.": "South Korea", 
"Iran, Islamic Rep.": "Iran",
"Hong Kong SAR, China": "Hong Kong"```

Finally, load the [Sciamgo Journal and Country Rank data for Energy Engineering and Power Technology](http://www.scimagojr.com/countryrank.php?category=2102) from the file `scimagojr-3.xlsx`, which ranks countries based on their journal contributions in the aforementioned area. Call this DataFrame **ScimEn**.

Join the three datasets: GDP, Energy, and ScimEn into a new dataset (using the intersection of country names). Use only the last 10 years (2006-2015) of GDP data and only the top 15 countries by Scimagojr 'Rank' (Rank 1 through 15). 

The index of this DataFrame should be the name of the country, and the columns should be ['Rank', 'Documents', 'Citable documents', 'Citations', 'Self-citations',
       'Citations per document', 'H index', 'Energy Supply',
       'Energy Supply per Capita', '% Renewable', '2006', '2007', '2008',
       '2009', '2010', '2011', '2012', '2013', '2014', '2015'].

*This function should return a DataFrame with 20 columns and 15 entries.*


```python
import pandas as pd
import numpy as np
 
def get_energy():
    
    energy_df = pd.read_excel('Energy Indicators.xls', skiprows=16)
    del energy_df['Unnamed: 0']
    del energy_df['Unnamed: 1']
    energy_df.dropna(inplace=True)
    energy_df.rename(columns={"Unnamed: 2":"Country", "Renewable Electricity Production": "% Renewable", "Energy Supply per capita": "Energy Supply per Capita"}, inplace=True)
    energy_df.replace('...', np.NaN, inplace=True)
    energy_df['Energy Supply'] = energy_df['Energy Supply'] * 1000000
    energy_df['Country'] = energy_df['Country'].str.replace(' \(.*\)', '')
    energy_df['Country'] = energy_df['Country'].str.replace('[0-9]*', '')
    
    x = { "Republic of Korea": "South Korea", 
         "United States of America": "United States", 
         "United Kingdom of Great Britain and Northern Ireland": "United Kingdom",
         "China, Hong Kong Special Administrative Region": "Hong Kong"}
    for item in x:
        energy_df['Country'].replace(item, x[item], inplace=True)    
    return energy_df

def get_gdp():
    gdp_df = pd.read_csv('world_bank.csv', skiprows=4)
    
    y = { "Korea, Rep.": "South Korea", 
         "Iran, Islamic Rep.": "Iran",
         "Hong Kong SAR, China": "Hong Kong"}
    for item in y:
        gdp_df['Country Name'].replace(item, y[item], inplace=True) 
    gdp_df.rename(columns={'Country Name': 'Country'}, inplace=True)    
    return gdp_df

def get_rank():
    ScimEn = pd.read_excel('scimagojr country rank 1996-2020.xlsx')
    return ScimEn
def answer_one():
    energy_df = get_energy()
    gdp_df = get_gdp()
    ScimEn = get_rank()
    for i in range(1960, 2006):
        gdp_df.drop([str(i)], axis=1, inplace=True)

    ScimEn = ScimEn[0:15]
    temp1 = pd.merge(energy_df, gdp_df, how="inner", left_on="Country", right_on="Country")
    temp2 = pd.merge(temp1, ScimEn, how="inner")
    temp2.set_index('Country', inplace=True)
 
    list_to_be_drop = ['Country Code', 'Indicator Name', 'Indicator Code']
    for item in list_to_be_drop:
        temp2.drop(item, axis=1, inplace=True)
    return temp2    

answer_one()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Supply</th>
      <th>Energy Supply per Capita</th>
      <th>% Renewable</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>Rank</th>
      <th>Region</th>
      <th>Documents</th>
      <th>Citable documents</th>
      <th>Citations</th>
      <th>Self-citations</th>
      <th>Citations per document</th>
      <th>H index</th>
    </tr>
    <tr>
      <th>Country</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Australia</th>
      <td>5.386000e+09</td>
      <td>231.0</td>
      <td>11.810810</td>
      <td>1.021939e+12</td>
      <td>1.060340e+12</td>
      <td>1.099644e+12</td>
      <td>1.119654e+12</td>
      <td>1.142251e+12</td>
      <td>1.169431e+12</td>
      <td>1.211913e+12</td>
      <td>1.241484e+12</td>
      <td>1.272520e+12</td>
      <td>1.301251e+12</td>
      <td>15</td>
      <td>Pacific Region</td>
      <td>20614</td>
      <td>20147</td>
      <td>314307</td>
      <td>51583</td>
      <td>15.25</td>
      <td>176</td>
    </tr>
    <tr>
      <th>Brazil</th>
      <td>1.214900e+10</td>
      <td>59.0</td>
      <td>69.648030</td>
      <td>1.845080e+12</td>
      <td>1.957118e+12</td>
      <td>2.056809e+12</td>
      <td>2.054215e+12</td>
      <td>2.208872e+12</td>
      <td>2.295245e+12</td>
      <td>2.339209e+12</td>
      <td>2.409740e+12</td>
      <td>2.412231e+12</td>
      <td>2.319423e+12</td>
      <td>14</td>
      <td>Latin America</td>
      <td>21524</td>
      <td>21236</td>
      <td>183915</td>
      <td>45172</td>
      <td>8.54</td>
      <td>127</td>
    </tr>
    <tr>
      <th>Canada</th>
      <td>1.043100e+10</td>
      <td>296.0</td>
      <td>61.945430</td>
      <td>1.564469e+12</td>
      <td>1.596740e+12</td>
      <td>1.612713e+12</td>
      <td>1.565145e+12</td>
      <td>1.613406e+12</td>
      <td>1.664087e+12</td>
      <td>1.693133e+12</td>
      <td>1.730688e+12</td>
      <td>1.773486e+12</td>
      <td>1.792609e+12</td>
      <td>8</td>
      <td>Northern America</td>
      <td>33472</td>
      <td>32863</td>
      <td>568080</td>
      <td>100953</td>
      <td>16.97</td>
      <td>227</td>
    </tr>
    <tr>
      <th>China</th>
      <td>1.271910e+11</td>
      <td>93.0</td>
      <td>19.754910</td>
      <td>3.992331e+12</td>
      <td>4.559041e+12</td>
      <td>4.997775e+12</td>
      <td>5.459247e+12</td>
      <td>6.039659e+12</td>
      <td>6.612490e+12</td>
      <td>7.124978e+12</td>
      <td>7.672448e+12</td>
      <td>8.230121e+12</td>
      <td>8.797999e+12</td>
      <td>1</td>
      <td>Asiatic Region</td>
      <td>273437</td>
      <td>272374</td>
      <td>2336764</td>
      <td>1615239</td>
      <td>8.55</td>
      <td>245</td>
    </tr>
    <tr>
      <th>France</th>
      <td>1.059700e+10</td>
      <td>166.0</td>
      <td>17.020280</td>
      <td>2.607840e+12</td>
      <td>2.669424e+12</td>
      <td>2.674637e+12</td>
      <td>2.595967e+12</td>
      <td>2.646995e+12</td>
      <td>2.702032e+12</td>
      <td>2.706968e+12</td>
      <td>2.722567e+12</td>
      <td>2.729632e+12</td>
      <td>2.761185e+12</td>
      <td>11</td>
      <td>Western Europe</td>
      <td>25232</td>
      <td>24732</td>
      <td>343860</td>
      <td>65734</td>
      <td>13.63</td>
      <td>178</td>
    </tr>
    <tr>
      <th>Germany</th>
      <td>1.326100e+10</td>
      <td>165.0</td>
      <td>17.901530</td>
      <td>3.332891e+12</td>
      <td>3.441561e+12</td>
      <td>3.478809e+12</td>
      <td>3.283340e+12</td>
      <td>3.417298e+12</td>
      <td>3.542371e+12</td>
      <td>3.556724e+12</td>
      <td>3.567317e+12</td>
      <td>3.624386e+12</td>
      <td>3.685556e+12</td>
      <td>6</td>
      <td>Western Europe</td>
      <td>38739</td>
      <td>38013</td>
      <td>433148</td>
      <td>95145</td>
      <td>11.18</td>
      <td>196</td>
    </tr>
    <tr>
      <th>India</th>
      <td>3.319500e+10</td>
      <td>26.0</td>
      <td>14.969080</td>
      <td>1.265894e+12</td>
      <td>1.374865e+12</td>
      <td>1.428361e+12</td>
      <td>1.549483e+12</td>
      <td>1.708459e+12</td>
      <td>1.821872e+12</td>
      <td>1.924235e+12</td>
      <td>2.051982e+12</td>
      <td>2.200617e+12</td>
      <td>2.367206e+12</td>
      <td>3</td>
      <td>Asiatic Region</td>
      <td>55082</td>
      <td>53775</td>
      <td>463165</td>
      <td>162944</td>
      <td>8.41</td>
      <td>181</td>
    </tr>
    <tr>
      <th>Iran</th>
      <td>9.172000e+09</td>
      <td>119.0</td>
      <td>5.707721</td>
      <td>3.895523e+11</td>
      <td>4.250646e+11</td>
      <td>4.289909e+11</td>
      <td>4.389208e+11</td>
      <td>4.677902e+11</td>
      <td>4.853309e+11</td>
      <td>4.532569e+11</td>
      <td>4.445926e+11</td>
      <td>4.639027e+11</td>
      <td>NaN</td>
      <td>12</td>
      <td>Middle East</td>
      <td>22933</td>
      <td>22734</td>
      <td>307280</td>
      <td>97038</td>
      <td>13.40</td>
      <td>141</td>
    </tr>
    <tr>
      <th>Italy</th>
      <td>6.530000e+09</td>
      <td>109.0</td>
      <td>33.667230</td>
      <td>2.202170e+12</td>
      <td>2.234627e+12</td>
      <td>2.211154e+12</td>
      <td>2.089938e+12</td>
      <td>2.125185e+12</td>
      <td>2.137439e+12</td>
      <td>2.077184e+12</td>
      <td>2.040871e+12</td>
      <td>2.033868e+12</td>
      <td>2.049316e+12</td>
      <td>9</td>
      <td>Western Europe</td>
      <td>27983</td>
      <td>26940</td>
      <td>352993</td>
      <td>87828</td>
      <td>12.61</td>
      <td>166</td>
    </tr>
    <tr>
      <th>Japan</th>
      <td>1.898400e+10</td>
      <td>149.0</td>
      <td>10.232820</td>
      <td>5.496542e+12</td>
      <td>5.617036e+12</td>
      <td>5.558527e+12</td>
      <td>5.251308e+12</td>
      <td>5.498718e+12</td>
      <td>5.473738e+12</td>
      <td>5.569102e+12</td>
      <td>5.644659e+12</td>
      <td>5.642884e+12</td>
      <td>5.669563e+12</td>
      <td>4</td>
      <td>Asiatic Region</td>
      <td>50523</td>
      <td>50065</td>
      <td>488062</td>
      <td>119930</td>
      <td>9.66</td>
      <td>193</td>
    </tr>
    <tr>
      <th>South Korea</th>
      <td>1.100700e+10</td>
      <td>221.0</td>
      <td>2.279353</td>
      <td>9.410199e+11</td>
      <td>9.924316e+11</td>
      <td>1.020510e+12</td>
      <td>1.027730e+12</td>
      <td>1.094499e+12</td>
      <td>1.134796e+12</td>
      <td>1.160809e+12</td>
      <td>1.194429e+12</td>
      <td>1.234340e+12</td>
      <td>1.266580e+12</td>
      <td>10</td>
      <td>Asiatic Region</td>
      <td>27655</td>
      <td>27445</td>
      <td>328488</td>
      <td>61531</td>
      <td>11.88</td>
      <td>155</td>
    </tr>
    <tr>
      <th>Russian Federation</th>
      <td>3.070900e+10</td>
      <td>214.0</td>
      <td>17.288680</td>
      <td>1.385793e+12</td>
      <td>1.504071e+12</td>
      <td>1.583004e+12</td>
      <td>1.459199e+12</td>
      <td>1.524917e+12</td>
      <td>1.589943e+12</td>
      <td>1.645876e+12</td>
      <td>1.666934e+12</td>
      <td>1.678709e+12</td>
      <td>1.616149e+12</td>
      <td>7</td>
      <td>Eastern Europe</td>
      <td>36735</td>
      <td>36560</td>
      <td>115938</td>
      <td>54993</td>
      <td>3.16</td>
      <td>90</td>
    </tr>
    <tr>
      <th>Spain</th>
      <td>4.923000e+09</td>
      <td>106.0</td>
      <td>37.968590</td>
      <td>1.414823e+12</td>
      <td>1.468146e+12</td>
      <td>1.484530e+12</td>
      <td>1.431475e+12</td>
      <td>1.431673e+12</td>
      <td>1.417355e+12</td>
      <td>1.380216e+12</td>
      <td>1.357139e+12</td>
      <td>1.375605e+12</td>
      <td>1.419821e+12</td>
      <td>13</td>
      <td>Western Europe</td>
      <td>21955</td>
      <td>21597</td>
      <td>352497</td>
      <td>64588</td>
      <td>16.06</td>
      <td>176</td>
    </tr>
    <tr>
      <th>United Kingdom</th>
      <td>7.920000e+09</td>
      <td>124.0</td>
      <td>10.600470</td>
      <td>2.419631e+12</td>
      <td>2.482203e+12</td>
      <td>2.470614e+12</td>
      <td>2.367048e+12</td>
      <td>2.403504e+12</td>
      <td>2.450911e+12</td>
      <td>2.479809e+12</td>
      <td>2.533370e+12</td>
      <td>2.605643e+12</td>
      <td>2.666333e+12</td>
      <td>5</td>
      <td>Western Europe</td>
      <td>43389</td>
      <td>42284</td>
      <td>615670</td>
      <td>111290</td>
      <td>14.19</td>
      <td>226</td>
    </tr>
    <tr>
      <th>United States</th>
      <td>9.083800e+10</td>
      <td>286.0</td>
      <td>11.570980</td>
      <td>1.479230e+13</td>
      <td>1.505540e+13</td>
      <td>1.501149e+13</td>
      <td>1.459484e+13</td>
      <td>1.496437e+13</td>
      <td>1.520402e+13</td>
      <td>1.554216e+13</td>
      <td>1.577367e+13</td>
      <td>1.615662e+13</td>
      <td>1.654857e+13</td>
      <td>2</td>
      <td>Northern America</td>
      <td>175891</td>
      <td>172431</td>
      <td>2230544</td>
      <td>724472</td>
      <td>12.68</td>
      <td>363</td>
    </tr>
  </tbody>
</table>
</div>



### Question 2
The previous question joined three datasets then reduced this to just the top 15 entries. When you joined the datasets, but before you reduced this to the top 15 items, how many entries did you lose?

*This function should return a single number.*


```python
%%HTML
<svg width="800" height="300">
  <circle cx="150" cy="180" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="blue" />
  <circle cx="200" cy="100" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="red" />
  <circle cx="100" cy="100" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="green" />
  <line x1="150" y1="125" x2="300" y2="150" stroke="black" stroke-width="2" fill="black" stroke-dasharray="5,3"/>
  <text  x="300" y="165" font-family="Verdana" font-size="35">Everything but this!</text>
</svg>
```


<svg width="800" height="300">
  <circle cx="150" cy="180" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="blue" />
  <circle cx="200" cy="100" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="red" />
  <circle cx="100" cy="100" r="80" fill-opacity="0.2" stroke="black" stroke-width="2" fill="green" />
  <line x1="150" y1="125" x2="300" y2="150" stroke="black" stroke-width="2" fill="black" stroke-dasharray="5,3"/>
  <text  x="300" y="165" font-family="Verdana" font-size="35">Everything but this!</text>
</svg>




```python
def answer_two():
    energy_df = get_energy()
    gdp_df = get_gdp()
    rank_df = get_rank()
    
    energy_len = len(energy_df)
    gdp_len = len(gdp_df)
    rank_len = len(rank_df)
    
    tempAB = pd.merge(energy_df, gdp_df, how="inner")
    merge_AB_len = len(tempAB)

    tempAC = pd.merge(energy_df, rank_df, how="inner")
    merge_AC_len = len(tempAC)

    tempBC = pd.merge(rank_df, gdp_df, how="inner")
    merge_BC_len = len(tempBC)

    result = energy_len + gdp_len + rank_len - merge_AB_len - merge_AC_len - merge_BC_len
    
    return result

answer_two()
```




    148



<br>

Answer the following questions in the context of only the top 15 countries by Scimagojr Rank (aka the DataFrame returned by `answer_one()`)

### Question 3
What is the average GDP over the last 10 years for each country? (exclude missing values from this calculation.)

*This function should return a Series named `avgGDP` with 15 countries and their average GDP sorted in descending order.*


```python
def answer_three():
    Top15 = answer_one()
    gdp_list = Top15[[str(i) for i in range(2006, 2016)]]
    avgGDP = gdp_list.mean(axis=1).sort_values(ascending=False)
    return avgGDP

answer_three()
```




    Country
    United States         1.536434e+13
    China                 6.348609e+12
    Japan                 5.542208e+12
    Germany               3.493025e+12
    France                2.681725e+12
    United Kingdom        2.487907e+12
    Brazil                2.189794e+12
    Italy                 2.120175e+12
    India                 1.769297e+12
    Canada                1.660647e+12
    Russian Federation    1.565459e+12
    Spain                 1.418078e+12
    Australia             1.164043e+12
    South Korea           1.106715e+12
    Iran                  4.441558e+11
    dtype: float64



### Question 4
By how much had the GDP changed over the 10 year span for the country with the 6th largest average GDP?

*This function should return a single number.*


```python
def answer_four():
    Top15 = answer_one()
    _6th = answer_three().index[5]
    gdp_list = Top15[[str(i) for i in range(2006, 2016)]]
    result = gdp_list.loc[_6th]
    return result[-1] - result[0]

answer_four()
```




    246702696075.3999



### Question 5
What is the mean `Energy Supply per Capita`?

*This function should return a single number.*


```python
def answer_five():
    Top15 = answer_one()
    return Top15['Energy Supply per Capita'].mean(axis=0)

answer_five()
```




    157.6



### Question 6
What country has the maximum % Renewable and what is the percentage?

*This function should return a tuple with the name of the country and the percentage.*


```python
def answer_six():
    Top15 = answer_one()
    max = Top15['% Renewable'].max()
    result = Top15[Top15['% Renewable'] == max]
    return (result.index[0], max)

answer_six()
```




    ('Brazil', 69.64803)



### Question 7
Create a new column that is the ratio of Self-Citations to Total Citations. 
What is the maximum value for this new column, and what country has the highest ratio?

*This function should return a tuple with the name of the country and the ratio.*


```python
def answer_seven():
    Top15 = answer_one()
    total_citations = Top15['Self-citations'].sum()
    Top15['Ratio'] = Top15['Self-citations'] / Top15['Citations']
    max = Top15['Ratio'].max()
    result = Top15[Top15['Ratio'] == max]
    return (result.index[0], max)

answer_seven()
```




    ('China', 0.6912289816173135)



### Question 8

Create a column that estimates the population using Energy Supply and Energy Supply per capita. 
What is the third most populous country according to this estimate?

*This function should return a single string value.*


```python
def answer_eight():
    Top15 = answer_one()
    Top15['Population'] = Top15['Energy Supply'] / Top15['Energy Supply per Capita']
    Top15.sort_values(by='Population', ascending=False, inplace=True)
    return Top15.iloc[2].name

answer_eight()
```




    'United States'



### Question 9
Create a column that estimates the number of citable documents per person. 
What is the correlation between the number of citable documents per capita and the energy supply per capita? Use the `.corr()` method, (Pearson's correlation).

*This function should return a single number.*

*(Optional: Use the built-in function `plot9()` to visualize the relationship between Energy Supply per Capita vs. Citable docs per Capita)*


```python
def answer_nine():
    Top15 = answer_one()
    Top15['PopEst'] = Top15['Energy Supply'] / Top15['Energy Supply per Capita']
    Top15['Citable docs per Capita'] = Top15['Citable documents'] / Top15['PopEst']
    result = Top15['Citable docs per Capita'].corr(Top15['Energy Supply per Capita'])
    return result

answer_nine()
```




    0.7434709127726776




```python
def plot9():
    import matplotlib as plt
    %matplotlib inline
    
    Top15 = answer_one()
    Top15['PopEst'] = Top15['Energy Supply'] / Top15['Energy Supply per Capita']
    Top15['Citable docs per Capita'] = Top15['Citable documents'] / Top15['PopEst']
    Top15.plot(x='Citable docs per Capita', y='Energy Supply per Capita', kind='scatter', xlim=[0, 0.0006])

```


```python
plot9()
```


    
![png](output_23_0.png)
    


### Question 10 (6.6%)
Create a new column with a 1 if the country's % Renewable value is at or above the median for all countries in the top 15, and a 0 if the country's % Renewable value is below the median.

*This function should return a series named `HighRenew` whose index is the country name sorted in ascending order of rank.*


```python
def answer_ten():
    Top15 = answer_one()
    median = Top15['% Renewable'].median()
    Top15['HighRenew'] = (Top15['% Renewable'] >= median) * 1
    return Top15.sort_values('Rank')['HighRenew']

answer_ten()
```




    Country
    China                 1
    United States         0
    India                 0
    Japan                 0
    United Kingdom        0
    Germany               1
    Russian Federation    1
    Canada                1
    Italy                 1
    South Korea           0
    France                1
    Iran                  0
    Spain                 1
    Brazil                1
    Australia             0
    Name: HighRenew, dtype: int32



### Question 11
Use the following dictionary to group the Countries by Continent, then create a dateframe that displays the sample size (the number of countries in each continent bin), and the sum, mean, and std deviation for the estimated population of each country.

```python
ContinentDict  = {'China':'Asia', 
                  'United States':'North America', 
                  'Japan':'Asia', 
                  'United Kingdom':'Europe', 
                  'Russian Federation':'Europe', 
                  'Canada':'North America', 
                  'Germany':'Europe', 
                  'India':'Asia',
                  'France':'Europe', 
                  'South Korea':'Asia', 
                  'Italy':'Europe', 
                  'Spain':'Europe', 
                  'Iran':'Asia',
                  'Australia':'Australia', 
                  'Brazil':'South America'}
```

*This function should return a DataFrame with index named Continent `['Asia', 'Australia', 'Europe', 'North America', 'South America']` and columns `['size', 'sum', 'mean', 'std']`*


```python
ContinentDict  = {'China':'Asia', 
                  'United States':'North America', 
                  'Japan':'Asia', 
                  'United Kingdom':'Europe', 
                  'Russian Federation':'Europe', 
                  'Canada':'North America', 
                  'Germany':'Europe', 
                  'India':'Asia',
                  'France':'Europe', 
                  'South Korea':'Asia', 
                  'Italy':'Europe', 
                  'Spain':'Europe', 
                  'Iran':'Asia',
                  'Australia':'Australia', 
                  'Brazil':'South America'}
def answer_eleven():
    Top15 = answer_one()
    Top15['PopEst'] = Top15['Energy Supply'] / Top15['Energy Supply per Capita']
    Top15_population = Top15['PopEst']
    group = Top15_population.groupby(ContinentDict)
    size = group.size().astype('float64')
    sum = group.sum()
    mean = group.mean()
    std = group.std()
    result = pd.DataFrame({
        'size': size,
        'sum': sum,
        'mean': mean,
        'std': std })
    return result
answer_eleven()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>size</th>
      <th>sum</th>
      <th>mean</th>
      <th>std</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Asia</th>
      <td>5.0</td>
      <td>2.898666e+09</td>
      <td>5.797333e+08</td>
      <td>6.790979e+08</td>
    </tr>
    <tr>
      <th>Australia</th>
      <td>1.0</td>
      <td>2.331602e+07</td>
      <td>2.331602e+07</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>Europe</th>
      <td>6.0</td>
      <td>4.579297e+08</td>
      <td>7.632161e+07</td>
      <td>3.464767e+07</td>
    </tr>
    <tr>
      <th>North America</th>
      <td>2.0</td>
      <td>3.528552e+08</td>
      <td>1.764276e+08</td>
      <td>1.996696e+08</td>
    </tr>
    <tr>
      <th>South America</th>
      <td>1.0</td>
      <td>2.059153e+08</td>
      <td>2.059153e+08</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### Question 12
Cut % Renewable into 5 bins. Group Top15 by the Continent, as well as these new % Renewable bins. How many countries are in each of these groups?

*This function should return a __Series__ with a MultiIndex of `Continent`, then the bins for `% Renewable`. Do not include groups with no countries.*


```python
ContinentDict  = {'China':'Asia', 
                  'United States':'North America', 
                  'Japan':'Asia', 
                  'United Kingdom':'Europe', 
                  'Russian Federation':'Europe', 
                  'Canada':'North America', 
                  'Germany':'Europe', 
                  'India':'Asia',
                  'France':'Europe', 
                  'South Korea':'Asia', 
                  'Italy':'Europe', 
                  'Spain':'Europe', 
                  'Iran':'Asia',
                  'Australia':'Australia', 
                  'Brazil':'South America'}

def answer_twelve():
    Top15 = answer_one()
    cut = pd.cut(Top15['% Renewable'], bins=5)
    Top15['bin'] = cut
    group = Top15.groupby(by=[ContinentDict, cut])
    result = group.size()
    return result

answer_twelve()
```




                   % Renewable     
    Asia           (2.212, 15.753]     4
                   (15.753, 29.227]    1
                   (29.227, 42.701]    0
                   (42.701, 56.174]    0
                   (56.174, 69.648]    0
    Australia      (2.212, 15.753]     1
                   (15.753, 29.227]    0
                   (29.227, 42.701]    0
                   (42.701, 56.174]    0
                   (56.174, 69.648]    0
    Europe         (2.212, 15.753]     1
                   (15.753, 29.227]    3
                   (29.227, 42.701]    2
                   (42.701, 56.174]    0
                   (56.174, 69.648]    0
    North America  (2.212, 15.753]     1
                   (15.753, 29.227]    0
                   (29.227, 42.701]    0
                   (42.701, 56.174]    0
                   (56.174, 69.648]    1
    South America  (2.212, 15.753]     0
                   (15.753, 29.227]    0
                   (29.227, 42.701]    0
                   (42.701, 56.174]    0
                   (56.174, 69.648]    1
    dtype: int64



### Question 13
Convert the Population Estimate series to a string with thousands separator (using commas). Do not round the results.

e.g. 317615384.61538464 -> 317,615,384.61538464

*This function should return a Series `PopEst` whose index is the country name and whose values are the population estimate string.*


```python
def answer_thirteen():
    Top15 = answer_one()
    Top15['PopEst'] = Top15['Energy Supply'] / Top15['Energy Supply per Capita']
    Top15['PopEst'] = Top15['PopEst'].map(lambda x: format(x, ','))
    result = Top15['PopEst']
    return result

answer_thirteen()
```




    Country
    Australia              23,316,017.316017315
    Brazil                 205,915,254.23728815
    Canada                  35,239,864.86486486
    China                 1,367,645,161.2903225
    France                  63,837,349.39759036
    Germany                 80,369,696.96969697
    India                 1,276,730,769.2307692
    Iran                    77,075,630.25210084
    Italy                  59,908,256.880733944
    Japan                  127,409,395.97315437
    South Korea            49,805,429.864253394
    Russian Federation            143,500,000.0
    Spain                    46,443,396.2264151
    United Kingdom         63,870,967.741935484
    United States          317,615,384.61538464
    Name: PopEst, dtype: object



### Optional

Use the built in function `plot_optional()` to see an example visualization.


```python
def plot_optional():
    import matplotlib as plt
    %matplotlib inline
    Top15 = answer_one()
    ax = Top15.plot(x='Rank', y='% Renewable', kind='scatter', 
                    c=['#e41a1c','#377eb8','#e41a1c','#4daf4a','#4daf4a','#377eb8','#4daf4a','#e41a1c',
                       '#4daf4a','#e41a1c','#4daf4a','#4daf4a','#e41a1c','#dede00','#ff7f00'], 
                    xticks=range(1,16), s=6*Top15['2014']/10**10, alpha=.75, figsize=[16,6]);

    for i, txt in enumerate(Top15.index):
        ax.annotate(txt, [Top15['Rank'][i], Top15['% Renewable'][i]], ha='center')

    print("This is an example of a visualization that can be created to help understand the data. \
This is a bubble chart showing % Renewable vs. Rank. The size of the bubble corresponds to the countries' \
2014 GDP, and the color corresponds to the continent.")
```


```python
plot_optional()
```

    This is an example of a visualization that can be created to help understand the data. This is a bubble chart showing % Renewable vs. Rank. The size of the bubble corresponds to the countries' 2014 GDP, and the color corresponds to the continent.
    


    
![png](output_34_1.png)
    



```python

```

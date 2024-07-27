# Investigating Individual Contributions to ActBlue and WinRed
Current investigation is limited to aggregation of individual donation records with the same name and zipcode. The top donators by total number of transactions and by number of transactions in a single day are listed. CSVs are provided for viewing more results than are available in the notebook output. The csvs only include individuals with record counts over 100 to save bandwidth.

## Quickstart / Steps to reproduce
1. Clone repo `git clone https://github.com/hemenge133/actbluewinred.git`
2. [Download 2023-2024 Individual Contributions file from FEC](https://www.fec.gov/files/bulk-downloads/2024/indiv24.zip)
3. Extract indiv24.zip in the same directory as the repo
4. Install dependencies
    - [python3](https://www.python.org/downloads/)
    - `pip install jupyter pandas`
5. Open Notebook file: `actblue.ipynb`

## Summary of findings
- The most frequent donor has made in excess of 20,000 individual donations in approximately 1.333 years or ~40 times per day on average
- The most amount of transactions by a donor in a single day is 4,441. 2,876 have a positive value (see below issue with negative/zero valued transactions).
- More insights to come

## Issues
- Some records have 5 digit zipcodes and others have 9 digit "zip+4" zipcodes, potentially resulting in multiple records in the final dataframe for the same person. I could use the google_maps.geocode API to combine zipcodes if they are within a specified distance, or just truncate the fully specified zipcodes.
    - For example: The 1st and 7th most frequent donor is MAKOWSKI, BRUCE. Both entries have a 48017 zipcode.
    - Solution: For now I am truncating 9 digit zipcodes to 5, and only taking the first 2 words of the name.
- There are many zero or negative valued transactions, ostensibly for chargebacks or voided transactions. Whether they should be counted will be considered. The majority appear to be positive.

## Notebook output

```python
import pandas as pd
import os
import gc
```


```python
# Read in the bulk individual contribution files from FEC.gov and combine

# Directory containing the text files
directory = './indiv24/by_date'

chunk_size = 200000  # Number of rows per chunk, reduce if kernel dies (memory overload)
column_names = [
    'ID', 'Field1', 'Field2', 'Field3', 'Field4', 'Field5', 'Field6', 'Name', 'City', 'State', 'Zip',
    'Employer', 'Occupation', 'Date', 'Amount', 'OtherID', 'Field16', 'Field17', 'Field18', 'Field19', 'Field20'
]

dfs = []
for filename in os.listdir(directory):
    if filename.endswith('.txt'):
        file_path = os.path.join(directory, filename)
        df = pd.read_csv(file_path, delimiter='|', names=column_names, header=None, low_memory=False)
        dfs.append(df)

combined_df = pd.concat(dfs, ignore_index=True)
pd.set_option('display.max_columns', None)
combined_df.head()
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
      <th>ID</th>
      <th>Field1</th>
      <th>Field2</th>
      <th>Field3</th>
      <th>Field4</th>
      <th>Field5</th>
      <th>Field6</th>
      <th>Name</th>
      <th>City</th>
      <th>State</th>
      <th>Zip</th>
      <th>Employer</th>
      <th>Occupation</th>
      <th>Date</th>
      <th>Amount</th>
      <th>OtherID</th>
      <th>Field16</th>
      <th>Field17</th>
      <th>Field18</th>
      <th>Field19</th>
      <th>Field20</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>C00079681</td>
      <td>N</td>
      <td>M6</td>
      <td>P</td>
      <td>202306129581782820</td>
      <td>15</td>
      <td>IND</td>
      <td>GODLEWSKI, KIM</td>
      <td>FENTON</td>
      <td>MI</td>
      <td>484309630</td>
      <td>IPS EQUIPMENT</td>
      <td>VICE PRESIDENT</td>
      <td>5282023.0</td>
      <td>100</td>
      <td>NaN</td>
      <td>2425123</td>
      <td>1707004</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4061220231749193699</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C00409730</td>
      <td>N</td>
      <td>M6</td>
      <td>P</td>
      <td>202306129582118675</td>
      <td>15E</td>
      <td>IND</td>
      <td>ROBERTS, PHILIP</td>
      <td>SEATTLE</td>
      <td>WA</td>
      <td>981092846</td>
      <td>RYAN, SWANSON &amp; CLEVELAND, PLLC</td>
      <td>ATTORNEY</td>
      <td>5282023.0</td>
      <td>500</td>
      <td>C00699470</td>
      <td>15444361</td>
      <td>1707040</td>
      <td>NaN</td>
      <td>* EARMARKED CONTRIBUTION: SEE BELOW</td>
      <td>4061320231749244130</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C00409730</td>
      <td>N</td>
      <td>M6</td>
      <td>P</td>
      <td>202306129582118670</td>
      <td>15E</td>
      <td>IND</td>
      <td>GRUNDSTEIN, LEON</td>
      <td>MERCER ISLAND</td>
      <td>WA</td>
      <td>980402445</td>
      <td>GENCARE, INC</td>
      <td>SENIOR LIVING OWNER, DEVELOPER, OPERAT</td>
      <td>5282023.0</td>
      <td>500</td>
      <td>C00699470</td>
      <td>15444363</td>
      <td>1707040</td>
      <td>NaN</td>
      <td>* EARMARKED CONTRIBUTION: SEE BELOW</td>
      <td>4061320231749244117</td>
    </tr>
    <tr>
      <th>3</th>
      <td>C00409730</td>
      <td>N</td>
      <td>M6</td>
      <td>P</td>
      <td>202306129582118676</td>
      <td>15E</td>
      <td>IND</td>
      <td>SCHOCKEN, JOSEPH L.</td>
      <td>SEATTLE</td>
      <td>WA</td>
      <td>981014129</td>
      <td>BROADMARK CAPITAL</td>
      <td>PRESIDENT</td>
      <td>5282023.0</td>
      <td>5000</td>
      <td>C00699470</td>
      <td>15444362</td>
      <td>1707040</td>
      <td>NaN</td>
      <td>* EARMARKED CONTRIBUTION: SEE BELOW</td>
      <td>4061320231749244134</td>
    </tr>
    <tr>
      <th>4</th>
      <td>C00063164</td>
      <td>N</td>
      <td>M6</td>
      <td>P</td>
      <td>202306139582145211</td>
      <td>15</td>
      <td>IND</td>
      <td>MCMILLAN, S TODD</td>
      <td>COLORADO SPRINGS</td>
      <td>CO</td>
      <td>809623583</td>
      <td>MCDONALD'S</td>
      <td>LICENSEE</td>
      <td>5282023.0</td>
      <td>200</td>
      <td>NaN</td>
      <td>17411206</td>
      <td>1707152</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4061320231749248513</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Create a primary key column by combining Name and Zip
combined_df['PrimaryKey'] = combined_df['Name'] + '_' + combined_df['Zip'].astype(str)

# Group by the primary key and aggregate
final_df = combined_df.groupby('PrimaryKey').agg(
    RecordCount=('PrimaryKey', 'size'),
    TotalAmount=('Amount', 'sum')
).reset_index()
final_df = final_df.sort_values(by='RecordCount', ascending=False)
# output to file
# final_df[final_df['RecordCount'] > 100].to_csv('raw_data_grouped_by_name_and_zip.csv', index=False)

final_df.head(10)
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
      <th>PrimaryKey</th>
      <th>RecordCount</th>
      <th>TotalAmount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1682585</th>
      <td>MAKOWSKI, BRUCE_480171279</td>
      <td>20092</td>
      <td>60654</td>
    </tr>
    <tr>
      <th>1311445</th>
      <td>JAHNS, RICHARD_760405951</td>
      <td>18705</td>
      <td>360303</td>
    </tr>
    <tr>
      <th>867301</th>
      <td>FORD, BEVERLY_300673614</td>
      <td>16791</td>
      <td>155112</td>
    </tr>
    <tr>
      <th>810454</th>
      <td>FARR, GERALD_786665129</td>
      <td>15304</td>
      <td>203311</td>
    </tr>
    <tr>
      <th>1427098</th>
      <td>KESKITALO, CANDACE_55384</td>
      <td>12724</td>
      <td>71559</td>
    </tr>
    <tr>
      <th>2781986</th>
      <td>URBANOWICZ, WENDY_98668</td>
      <td>11912</td>
      <td>64469</td>
    </tr>
    <tr>
      <th>1682583</th>
      <td>MAKOWSKI, BRUCE_48017</td>
      <td>11038</td>
      <td>31870</td>
    </tr>
    <tr>
      <th>1049787</th>
      <td>GRISWOLD, EDSON_80231</td>
      <td>10642</td>
      <td>141357</td>
    </tr>
    <tr>
      <th>1630983</th>
      <td>LOONEY, GEORGE_300402707</td>
      <td>10587</td>
      <td>26656</td>
    </tr>
    <tr>
      <th>2567262</th>
      <td>SOLORZANO, DIANA_92056</td>
      <td>9963</td>
      <td>8914</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Issue: Some entries include middle name or title, or change use a zip+4 code in place of a zip code
filtered_df = final_df.loc[final_df['PrimaryKey'].str.startswith('MAKOWSKI, BRUCE')]
print(filtered_df)
```

                                       PrimaryKey  RecordCount  TotalAmount
    1682585             MAKOWSKI, BRUCE_480171279        20092        60654
    1682583                 MAKOWSKI, BRUCE_48017        11038        31870
    1682579      MAKOWSKI, BRUCE EDWARD_480171279         2146         6945
    1682578  MAKOWSKI, BRUCE EDWARD MR._480171279          342         3004
    1682584             MAKOWSKI, BRUCE_480171227          230         1028
    1682580         MAKOWSKI, BRUCE MR._480171279           94          583
    1682582                 MAKOWSKI, BRUCE_47985            5            4
    1682581              MAKOWSKI, BRUCE MR_48017            4            6



```python
# Solution: Only use first 5 digits of Zip and first two words of name (first and last)
# Remove previous result dataframes from memory
if 'final_df' in locals():
    del final_df
if 'filtered_df' in locals():
    del filtered_df
gc.collect()

combined_df['Name'] = combined_df['Name'].fillna('').astype(str)
combined_df['PrimaryName'] = combined_df['Name'].apply(lambda x: ' '.join(x.split()[:2]))
combined_df['PrimaryZip'] = combined_df['Zip'].astype(str).str[:5]
combined_df['PrimaryKey'] = combined_df['PrimaryName'] + '_' + combined_df['PrimaryZip']
combined_df['Date'] = pd.to_datetime(combined_df['Date'], format='%m%d%Y')
corrected_final_df = combined_df.groupby(['PrimaryKey']).agg(
    RecordCount=('PrimaryKey', 'size'),
    TotalAmount=('Amount', 'sum')
).reset_index()

# Dataframe without zip+4 specificity and without middle names or titles
corrected_final_df = corrected_final_df.sort_values(by='RecordCount', ascending=False)
# output to file
# corrected_final_df[corrected_final_df['RecordCount'] > 100].to_csv('corrected_data_grouped_by_name_and_zip.csv', index=False)
pd.set_option('display.max_rows', None)
corrected_final_df.head(10)
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
      <th>PrimaryKey</th>
      <th>RecordCount</th>
      <th>TotalAmount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1182436</th>
      <td>MAKOWSKI, BRUCE_48017</td>
      <td>33946</td>
      <td>104090</td>
    </tr>
    <tr>
      <th>610786</th>
      <td>FORD, BEVERLY_30067</td>
      <td>23704</td>
      <td>212363</td>
    </tr>
    <tr>
      <th>921807</th>
      <td>JAHNS, RICHARD_76040</td>
      <td>23055</td>
      <td>462964</td>
    </tr>
    <tr>
      <th>1003036</th>
      <td>KESKITALO, CANDACE_55384</td>
      <td>20477</td>
      <td>134036</td>
    </tr>
    <tr>
      <th>1957061</th>
      <td>URBANOWICZ, WENDY_98668</td>
      <td>18657</td>
      <td>159470</td>
    </tr>
    <tr>
      <th>1533107</th>
      <td>PRESLEY, JUANITA_83642</td>
      <td>16211</td>
      <td>249974</td>
    </tr>
    <tr>
      <th>1145859</th>
      <td>LOONEY, GEORGE_30040</td>
      <td>15798</td>
      <td>38273</td>
    </tr>
    <tr>
      <th>738416</th>
      <td>GRISWOLD, EDSON_80231</td>
      <td>15630</td>
      <td>260486</td>
    </tr>
    <tr>
      <th>571009</th>
      <td>FARR, GERALD_78666</td>
      <td>15469</td>
      <td>206141</td>
    </tr>
    <tr>
      <th>1389896</th>
      <td>NGUYEN, KATHERINE_21740</td>
      <td>14719</td>
      <td>12630</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Let's look at the donors who gave the most amount of times in a single day
if 'corrected_final_df' in locals():
    del corrected_final_df
gc.collect()
daily_corrected_final_df = combined_df.groupby(['PrimaryKey', 'Date']).agg(
    RecordCount=('PrimaryKey', 'size'),
    TotalAmount=('Amount', 'sum')
).reset_index()
daily_corrected_final_df = daily_corrected_final_df.sort_values(by='RecordCount', ascending=False)
# output to file
# daily_corrected_final_df[daily_corrected_final_df['RecordCount'] > 100].to_csv('daily_corrected_data_grouped_by_name_and_zip.csv', index=False)
```


```python
daily_corrected_final_df.head(10)
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
      <th>PrimaryKey</th>
      <th>Date</th>
      <th>RecordCount</th>
      <th>TotalAmount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>15280446</th>
      <td>ZARCONE, ANNAMAE_11004</td>
      <td>2023-09-06</td>
      <td>4441</td>
      <td>12876</td>
    </tr>
    <tr>
      <th>9063512</th>
      <td>MCKEE, LAURENCE_29485</td>
      <td>2024-03-26</td>
      <td>3218</td>
      <td>24474</td>
    </tr>
    <tr>
      <th>1934189</th>
      <td>CAFFREY, DOROTHY_60126</td>
      <td>2023-11-01</td>
      <td>2824</td>
      <td>22465</td>
    </tr>
    <tr>
      <th>8254480</th>
      <td>LOONEY, GEORGE_30040</td>
      <td>2024-02-23</td>
      <td>2724</td>
      <td>8519</td>
    </tr>
    <tr>
      <th>7351897</th>
      <td>KIPPUR, SUSAN_10023</td>
      <td>2023-12-10</td>
      <td>2486</td>
      <td>26240</td>
    </tr>
    <tr>
      <th>5865322</th>
      <td>HEATON, VONNIE_30101</td>
      <td>2024-10-09</td>
      <td>2394</td>
      <td>10150</td>
    </tr>
    <tr>
      <th>10916180</th>
      <td>POHOST, GERALD_90005</td>
      <td>2024-02-27</td>
      <td>2178</td>
      <td>8484</td>
    </tr>
    <tr>
      <th>6663259</th>
      <td>JAHNS, RICHARD_76040</td>
      <td>2023-10-06</td>
      <td>2132</td>
      <td>39759</td>
    </tr>
    <tr>
      <th>6663263</th>
      <td>JAHNS, RICHARD_76040</td>
      <td>2023-10-10</td>
      <td>2082</td>
      <td>26698</td>
    </tr>
    <tr>
      <th>4045746</th>
      <td>EVIDON, EVE_55436</td>
      <td>2023-11-14</td>
      <td>1948</td>
      <td>13742</td>
    </tr>
  </tbody>
</table>
</div>




```python
# There were 4,441 transactions attributed to Annamae Zarcone on 2023-09-06 alone
zarcone_96_df =  combined_df[(combined_df['PrimaryKey'] == "ZARCONE, ANNAMAE_11004") & (combined_df['Date'] == "2023-09-06")]
zarcone_96_df = zarcone_96_df[['Name', 'Date', 'Amount']]

zarcone_96_df.head(10)
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
      <th>Name</th>
      <th>Date</th>
      <th>Amount</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9513647</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513648</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513649</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513650</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513651</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513652</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
    <tr>
      <th>9513653</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>6</td>
    </tr>
    <tr>
      <th>9513654</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9513655</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9513656</th>
      <td>ZARCONE, ANNAMAE</td>
      <td>2023-09-06</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Many of the transactions are zero or negative, apparently in reference to chargebacks or adjustments to previous donations
# The negative transactions do not positively contribute to the TotalAmount sum
# Let's count the negative and zero valued transactions
negative_count = (zarcone_96_df['Amount'] < 0).sum()
zero_count = (zarcone_96_df['Amount'] == 0).sum()

# Print the counts
print(f'Number of negative results: {negative_count}')
print(f'Number of zero results: {zero_count}')
pos_count = len(zarcone_96_df) - zero_count - negative_count
print(f'Number of positive results: {pos_count}')
seconds_in_day = 24 * 60 * 60
print(f'Average of {round(seconds_in_day / len(zarcone_96_df), 3)}s between each transaction of any type')
print(f'Average of {round(seconds_in_day / pos_count, 3)}s between each positive transaction')
```

    Number of negative results: 681
    Number of zero results: 884
    Number of positive results: 2876
    Average of 19.455s between each transaction of any type
    Average of 30.042s between each positive transaction


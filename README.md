# Investigating Individual Contributions to ActBlue and WinRed
Current investigation is limited to aggregation of individual donations with the same name and zipcode. The top 100 donators by number of contributions are listed.

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
- More insights to come

## Issues
- Some records have 5 digit zipcodes and others have 9 digit zipcodes, resulting in multiple records in the final dataframe for the same person (potentially). I could use the google_maps.geocode API to combine zipcodes if they are equivalent, or just truncate the fully specified zipcodes.
    - For example: The 1st and 7th most frequent donor is MAKOWSKI, BRUCE. Both entries have a 48017 zipcode.

## Notebook output

```python
import pandas as pd
import os
```


```python
# Read in the bulk individual contribution file from FEC.gov

# Directory containing the text files
directory = './indiv24/by_date'

chunk_size = 200000  # Number of rows per chunk
column_names = [
    'ID', 'Field1', 'Field2', 'Field3', 'Field4', 'Field5', 'Field6', 'Name', 'City', 'State', 'Zip',
    'Employer', 'Occupation', 'Date', 'Amount', 'OtherID', 'Field16', 'Field17', 'Field18', 'Field19', 'Field20'
]

# Initialize an empty list to store DataFrames
dfs = []

# Loop over each file in the directory
for filename in os.listdir(directory):
    if filename.endswith('.txt'):
        file_path = os.path.join(directory, filename)
        df = pd.read_csv(file_path, delimiter='|', names=column_names, header=None, low_memory=False)
        dfs.append(df)

# Concatenate all DataFrames in the list
combined_df = pd.concat(dfs, ignore_index=True)
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
      <th>...</th>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
<p>5 rows × 21 columns</p>
</div>




```python
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
      <th>...</th>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
      <td>...</td>
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
<p>5 rows × 21 columns</p>
</div>




```python
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
```


```python
final_df = final_df.sort_values(by='RecordCount', ascending=False)
pd.set_option('display.max_rows', None)
final_df.head(100)
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
    <tr>
      <th>2978530</th>
      <td>WOODS, BARB_601483638</td>
      <td>9659</td>
      <td>78583</td>
    </tr>
    <tr>
      <th>1821015</th>
      <td>MENEGHELLI, CECILE_944043824</td>
      <td>9101</td>
      <td>60020</td>
    </tr>
    <tr>
      <th>2435594</th>
      <td>SCHWEITZER, MICHELLE_981081538</td>
      <td>9000</td>
      <td>59626</td>
    </tr>
    <tr>
      <th>1442206</th>
      <td>KING, ALLAN_085405852</td>
      <td>8505</td>
      <td>82297</td>
    </tr>
    <tr>
      <th>1659696</th>
      <td>LYONS, CHRISTA LYONS_22314</td>
      <td>8210</td>
      <td>58881</td>
    </tr>
    <tr>
      <th>2441068</th>
      <td>SCOTT, ROGER_421019427</td>
      <td>8199</td>
      <td>29978</td>
    </tr>
    <tr>
      <th>636356</th>
      <td>DEARMAN, MELINDA_78261</td>
      <td>8018</td>
      <td>39973</td>
    </tr>
    <tr>
      <th>2178063</th>
      <td>PRESLEY, JUANITA_836424742</td>
      <td>7720</td>
      <td>118438</td>
    </tr>
    <tr>
      <th>1427099</th>
      <td>KESKITALO, CANDACE_553849734</td>
      <td>7624</td>
      <td>59482</td>
    </tr>
    <tr>
      <th>2524488</th>
      <td>SKINNER, SHARON_958166608</td>
      <td>7520</td>
      <td>75672</td>
    </tr>
    <tr>
      <th>1732584</th>
      <td>MATENOS, AGNES_019608572</td>
      <td>7451</td>
      <td>50862</td>
    </tr>
    <tr>
      <th>1519586</th>
      <td>LAHAY, DAVID_658092356</td>
      <td>7378</td>
      <td>60240</td>
    </tr>
    <tr>
      <th>1880177</th>
      <td>MONDER, STEVEN_452154105</td>
      <td>7014</td>
      <td>133610</td>
    </tr>
    <tr>
      <th>1439245</th>
      <td>KIM, SUN HAE_113673921</td>
      <td>6773</td>
      <td>14239</td>
    </tr>
    <tr>
      <th>1297875</th>
      <td>IVAN, LAJOS_088051232</td>
      <td>6714</td>
      <td>13812</td>
    </tr>
    <tr>
      <th>88092</th>
      <td>AROSE, ROBERT_077622274</td>
      <td>6549</td>
      <td>67052</td>
    </tr>
    <tr>
      <th>867300</th>
      <td>FORD, BEVERLY_30067</td>
      <td>6535</td>
      <td>54183</td>
    </tr>
    <tr>
      <th>1880176</th>
      <td>MONDER, STEVEN_45215</td>
      <td>6402</td>
      <td>119823</td>
    </tr>
    <tr>
      <th>2781987</th>
      <td>URBANOWICZ, WENDY_986686163</td>
      <td>6383</td>
      <td>86047</td>
    </tr>
    <tr>
      <th>1442499</th>
      <td>KING, BILL_73052</td>
      <td>6306</td>
      <td>87756</td>
    </tr>
    <tr>
      <th>1972860</th>
      <td>NEWMAN, HAROLD_33180</td>
      <td>6271</td>
      <td>72414</td>
    </tr>
    <tr>
      <th>358103</th>
      <td>BURGESS, RICHARD_236932544</td>
      <td>6184</td>
      <td>112006</td>
    </tr>
    <tr>
      <th>1010035</th>
      <td>GORE, TRINA_333255426</td>
      <td>6146</td>
      <td>25312</td>
    </tr>
    <tr>
      <th>844067</th>
      <td>FISHER, CHARLES_920071882</td>
      <td>6137</td>
      <td>221812</td>
    </tr>
    <tr>
      <th>2691233</th>
      <td>TAYLOR, JILL_598045849</td>
      <td>6026</td>
      <td>37609</td>
    </tr>
    <tr>
      <th>721694</th>
      <td>DUERST, JOHN_973819212</td>
      <td>5996</td>
      <td>95348</td>
    </tr>
    <tr>
      <th>2178059</th>
      <td>PRESLEY, JUANITA_83642</td>
      <td>5969</td>
      <td>83767</td>
    </tr>
    <tr>
      <th>2441550</th>
      <td>SCOTT, THOMAS_99504</td>
      <td>5878</td>
      <td>49528</td>
    </tr>
    <tr>
      <th>2888644</th>
      <td>WELDEN, AMY_97520</td>
      <td>5875</td>
      <td>26320</td>
    </tr>
    <tr>
      <th>756717</th>
      <td>EHLER, JOANNE_601011859</td>
      <td>5793</td>
      <td>40604</td>
    </tr>
    <tr>
      <th>1345404</th>
      <td>JOHNSON, MARK_45640</td>
      <td>5775</td>
      <td>62264</td>
    </tr>
    <tr>
      <th>1376905</th>
      <td>KADO, AIDA_077537634</td>
      <td>5727</td>
      <td>40616</td>
    </tr>
    <tr>
      <th>2199417</th>
      <td>QUILLIN, RANDY_376601165</td>
      <td>5686</td>
      <td>11544</td>
    </tr>
    <tr>
      <th>1437581</th>
      <td>KILPATRIC, GARY_87505</td>
      <td>5678</td>
      <td>39965</td>
    </tr>
    <tr>
      <th>1978690</th>
      <td>NICHOLLS, ROSALIE_78748</td>
      <td>5593</td>
      <td>61958</td>
    </tr>
    <tr>
      <th>2166537</th>
      <td>POSEY, LANEAL_391574513</td>
      <td>5575</td>
      <td>70287</td>
    </tr>
    <tr>
      <th>2285002</th>
      <td>ROAN DENNIS, CAROL ANN_98277</td>
      <td>5545</td>
      <td>20971</td>
    </tr>
    <tr>
      <th>2828541</th>
      <td>WADE, DAN_891384671</td>
      <td>5507</td>
      <td>21670</td>
    </tr>
    <tr>
      <th>125820</th>
      <td>BALDWIN, JEANNE_08343</td>
      <td>5399</td>
      <td>81518</td>
    </tr>
    <tr>
      <th>1788442</th>
      <td>MCKEE, LAURENCE_294854805</td>
      <td>5380</td>
      <td>59150</td>
    </tr>
    <tr>
      <th>382251</th>
      <td>CAFFREY, DOROTHY_60126</td>
      <td>5354</td>
      <td>42385</td>
    </tr>
    <tr>
      <th>2863243</th>
      <td>WATERMAN, NORMA_982267764</td>
      <td>5328</td>
      <td>30867</td>
    </tr>
    <tr>
      <th>1156030</th>
      <td>HEATON, VONNIE_301018950</td>
      <td>5323</td>
      <td>29937</td>
    </tr>
    <tr>
      <th>3018009</th>
      <td>ZARCONE, ANNAMAE_110041102</td>
      <td>5275</td>
      <td>23933</td>
    </tr>
    <tr>
      <th>1484147</th>
      <td>KOSMINSKY, JANE_10023</td>
      <td>5155</td>
      <td>34327</td>
    </tr>
    <tr>
      <th>455097</th>
      <td>CHASIN, MARK_070526603</td>
      <td>5153</td>
      <td>42172</td>
    </tr>
    <tr>
      <th>1728086</th>
      <td>MASON, GWYNETH_73132</td>
      <td>5136</td>
      <td>21860</td>
    </tr>
    <tr>
      <th>1096425</th>
      <td>HAMLIN, DENNIS_782581629</td>
      <td>5101</td>
      <td>31092</td>
    </tr>
    <tr>
      <th>2545001</th>
      <td>SMITH, KAREN_740083754</td>
      <td>5040</td>
      <td>89497</td>
    </tr>
    <tr>
      <th>2883901</th>
      <td>WEISBART, JAN_90035</td>
      <td>4944</td>
      <td>45610</td>
    </tr>
    <tr>
      <th>1155981</th>
      <td>HEATON, KEVIN_921311632</td>
      <td>4943</td>
      <td>81198</td>
    </tr>
    <tr>
      <th>2154904</th>
      <td>POHOST, GERALD_90005</td>
      <td>4915</td>
      <td>22057</td>
    </tr>
    <tr>
      <th>1382041</th>
      <td>KAM, DINNY_92057</td>
      <td>4906</td>
      <td>21778</td>
    </tr>
    <tr>
      <th>1976694</th>
      <td>NGUYEN, KATHERINE_217403576</td>
      <td>4896</td>
      <td>4658</td>
    </tr>
    <tr>
      <th>1662543</th>
      <td>MAAKE, FISIITETEFA_96782</td>
      <td>4882</td>
      <td>41962</td>
    </tr>
    <tr>
      <th>2046905</th>
      <td>OVERBECK, JOHN_191191528</td>
      <td>4872</td>
      <td>49858</td>
    </tr>
    <tr>
      <th>2831566</th>
      <td>WAGNER, LYNN_335967005</td>
      <td>4870</td>
      <td>73064</td>
    </tr>
    <tr>
      <th>2155109</th>
      <td>POINTS, MARSHA_757032015</td>
      <td>4844</td>
      <td>32348</td>
    </tr>
    <tr>
      <th>2368688</th>
      <td>SALATA, SUSAN L._06604</td>
      <td>4812</td>
      <td>40179</td>
    </tr>
    <tr>
      <th>50814</th>
      <td>ALVES, DIANE_97215</td>
      <td>4773</td>
      <td>43555</td>
    </tr>
    <tr>
      <th>1297874</th>
      <td>IVAN, LAJOS_08805</td>
      <td>4746</td>
      <td>5641</td>
    </tr>
    <tr>
      <th>137997</th>
      <td>BARELAS, HORTENCIA_770354222</td>
      <td>4727</td>
      <td>13363</td>
    </tr>
    <tr>
      <th>2509003</th>
      <td>SIMMONS, JUDY_314042802</td>
      <td>4701</td>
      <td>68733</td>
    </tr>
    <tr>
      <th>797687</th>
      <td>EVIDON, EVE_55436</td>
      <td>4693</td>
      <td>32334</td>
    </tr>
    <tr>
      <th>677636</th>
      <td>DILGER, HERBERT_606464832</td>
      <td>4693</td>
      <td>48301</td>
    </tr>
    <tr>
      <th>1449376</th>
      <td>KIPPUR, SUSAN_10023</td>
      <td>4637</td>
      <td>46588</td>
    </tr>
    <tr>
      <th>1496160</th>
      <td>KRIEGER, KARL_21654</td>
      <td>4555</td>
      <td>84018</td>
    </tr>
    <tr>
      <th>1972864</th>
      <td>NEWMAN, HAROLD_331803546</td>
      <td>4541</td>
      <td>48742</td>
    </tr>
    <tr>
      <th>1976693</th>
      <td>NGUYEN, KATHERINE_21740</td>
      <td>4503</td>
      <td>3431</td>
    </tr>
    <tr>
      <th>1864627</th>
      <td>MIRABILE, LORRAINE_321622208</td>
      <td>4503</td>
      <td>45083</td>
    </tr>
    <tr>
      <th>79326</th>
      <td>ARCHER, SANDRA_605171739</td>
      <td>4497</td>
      <td>29534</td>
    </tr>
    <tr>
      <th>1921703</th>
      <td>MUELLER, JOSEPH_126015640</td>
      <td>4484</td>
      <td>49965</td>
    </tr>
    <tr>
      <th>223737</th>
      <td>BILLINGS, GREGORY_20011</td>
      <td>4463</td>
      <td>12330</td>
    </tr>
    <tr>
      <th>262198</th>
      <td>BONIN, JUDY_244016287</td>
      <td>4458</td>
      <td>58823</td>
    </tr>
    <tr>
      <th>407273</th>
      <td>CAREY, ILDIKO_183537773</td>
      <td>4457</td>
      <td>29390</td>
    </tr>
    <tr>
      <th>613688</th>
      <td>DAVENPORT, ANNE I_19147</td>
      <td>4427</td>
      <td>39420</td>
    </tr>
    <tr>
      <th>558797</th>
      <td>COX, JUNE_59327</td>
      <td>4400</td>
      <td>21771</td>
    </tr>
    <tr>
      <th>284873</th>
      <td>BOYNTON, ELIZABETH_04074</td>
      <td>4397</td>
      <td>27458</td>
    </tr>
    <tr>
      <th>602575</th>
      <td>DALY, CAROL_94087</td>
      <td>4379</td>
      <td>24765</td>
    </tr>
    <tr>
      <th>2966232</th>
      <td>WOINAROWICZ, KATHLEEN_554220545</td>
      <td>4353</td>
      <td>49397</td>
    </tr>
    <tr>
      <th>2524487</th>
      <td>SKINNER, SHARON_95816</td>
      <td>4352</td>
      <td>44663</td>
    </tr>
    <tr>
      <th>2981187</th>
      <td>WOOLARD, LINDA_481609153</td>
      <td>4346</td>
      <td>50930</td>
    </tr>
    <tr>
      <th>1010034</th>
      <td>GORE, TRINA_33325</td>
      <td>4330</td>
      <td>20115</td>
    </tr>
    <tr>
      <th>757752</th>
      <td>EICHEL, LAURENCE_80549</td>
      <td>4328</td>
      <td>82223</td>
    </tr>
    <tr>
      <th>1437582</th>
      <td>KILPATRIC, GARY_875055714</td>
      <td>4327</td>
      <td>27988</td>
    </tr>
    <tr>
      <th>1147268</th>
      <td>HAY, RICHARD_60304</td>
      <td>4228</td>
      <td>12268</td>
    </tr>
    <tr>
      <th>1049788</th>
      <td>GRISWOLD, EDSON_802315602</td>
      <td>4195</td>
      <td>98464</td>
    </tr>
    <tr>
      <th>2544999</th>
      <td>SMITH, KAREN_74008</td>
      <td>4172</td>
      <td>73982</td>
    </tr>
    <tr>
      <th>2092747</th>
      <td>PAYNE, SYLVIA_231853318</td>
      <td>4164</td>
      <td>50641</td>
    </tr>
    <tr>
      <th>2777030</th>
      <td>UHLER, ROGER_03245</td>
      <td>4105</td>
      <td>32529</td>
    </tr>
  </tbody>
</table>
</div>




```python

```

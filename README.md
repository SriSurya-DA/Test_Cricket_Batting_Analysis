# Test Cricket Batting Analysis

![Batting](https://github.com/SriSurya-DA/Test_Cricket_Battings_Analysis-Data_Cleaning-/blob/main/Test_Cricket.webp)

## Overview
This project performs a detailed analysis of Test cricket batting records using data cleaning, feature engineering, and exploratory data analysis (EDA). The goal is to uncover insights into players' performances and career trends.

---

## Steps and Insights

### 1. Data Loading and Exploration

#### Loading the Dataset
```python
import pandas as pd

df = pd.read_csv('TestMatch_Data - Test matches _ Batting records _ Highest career batting average _ ESPNcricinfo.csv')
```

- **Initial Inspection**:
  - Displayed the dataset using `df.head()` and verified dimensions with `df.shape`.
  - Checked for missing and duplicate values using `df.isnull()` and `df.duplicated()`.

#### Key Observations:
- Columns contained mixed data formats (e.g., `Span`, `Matches`).
- Player names included country affiliations.

---

### 2. Data Cleaning

#### Renaming Columns
```python
df.rename(columns={
    'Mat': 'Matches',
    'NO': 'Not_Out',
    'HS': 'Highest_Inns_Score',
    'BF': 'Ball_Faced',
    'SR': 'Batting_Strike_Rate',
    '0': 'Ducks'
}, inplace=True)
```

#### Handling Missing and Duplicate Values
```python
df.dropna(inplace=True)
df.drop_duplicates(inplace=True)
```

#### Formatting Columns
- **Split `Span` into `Debut_Year` and `Last_Year`.**
```python
df['Debut_Year'] = df['Span'].str.split('-').str[0].astype(int)
df['Last_Year'] = df['Span'].str.split('-').str[1].astype(int)
df.drop(['Span'], axis=1, inplace=True)
```
- **Extract Player Name and Country:**
```python
df['Player_Name'] = df['Player'].str.split('(').str[0]
df['Country'] = df['Player'].str.extract('\((.*?)\)')[0]
df.drop(['Player'], axis=1, inplace=True)
```
- **Convert Columns to Numeric:**
```python
df['Highest_Inns_Score'] = df['Highest_Inns_Score'].str.replace('*', '').astype(int)
df['Matches'] = df['Matches'].astype(int)
df['4s'] = df['4s'].astype(int)
df['6s'] = df['6s'].astype(int)
df['Ball_Faced'] = df['Ball_Faced'].str.split('+|-').str[0].astype(int)
```

---

### 3. Feature Engineering

#### Career Length
```python
df['Career_Length'] = df['Last_Year'] - df['Debut_Year']
```

#### New Metrics
- **Average Career Length:**
```python
df['Career_Length'].mean()
```
- **Average Strike Rate for Players with Long Careers:**
```python
df[df['Career_Length'] > 10]['Batting_Strike_Rate'].mean()
```

---

### 4. Exploratory Data Analysis (EDA)

#### Key Questions and Insights

1. **Players Who Debuted Before 1960:**
```python
df[df['Debut_Year'] < 1960]['Player_Name'].count()
```

2. **Highest Innings Scores by Country:**
```python
df.groupby('Country')['Highest_Inns_Score'].max()
```

3. **Centuries Scored by Country:**
```python
df.groupby('Country')['100'].max()
```

4. **Averages of Key Metrics by Country:**
```python
df.groupby('Country')[['100', '50', 'Ducks']].mean()
```

---

### 5. Visualizations

#### Relationships Among Metrics
```python
import seaborn as sns
sns.set_theme(style='darkgrid')
sns.scatterplot(df.groupby('Country')[['100', '50', 'Ducks']].mean())
```

---

## Conclusion
This analysis provides a comprehensive view of Test cricket batting performances. It highlights:
- Career trends, including average career lengths and strike rates for long-tenured players.
- Country-wise performance metrics like highest innings scores and averages of centuries.
- The importance of data cleaning and feature engineering in deriving meaningful insights from raw data.


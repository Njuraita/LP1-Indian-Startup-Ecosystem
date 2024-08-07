# LP1-Indian-Startup-Ecosystem
In this project our team is tasked with an assignment to assess the viability of setting up a startup in the indian Ecosystem. We have been provided with various datasets from 2018 to 2021 which will aid us in exploration, analysis and making informed data-driven decisions. The Model we will be using is the *CRISP-DM* model using the *Agile* methodology for a good flow with our project. The *CRISP-DM* stages used in this project are excludig Evaluation and Modeling as we will not be doing any Machine Learning.Therefore the stages we will be going through are Business Understanding, Data Understanding, Data Preparation and In Deployment we will be using Power Bi and Medium for Project Article write-up

## 1.0 Business understanding 
In this phase we delve deeper to understand the assignment at hand and the business requirement of the project by:
    -*Setting the project title* 
    -*Coming up with a hypothesis testing for our project*
    -*Setting up our analysis questions in this case what are we intending to find out as we navigate the data*

### 1.1 Seting the project title 
*Project Title*: 
   
    **Viability of setting up a Startup Business in the Indian Ecosystem**

### 1.2 Coming up with a Hypothesis testing 
*Hypothesis Testiing*:

**Null Hypothesis:  Null Hypothesis (H0) There is no significant difference in the amount of funding recieved by startups across different sectors and stages**

**Alternative Hypothesis (H1) There is a significant difference in the amount of funding recieved by startups across different sectors and stages**

### 1.3 Setting the Analytical Qustions 

*Analytical Questions* - This two analytical questions are among the questions we asked in this project - Kindly delve into the project to see more of the questions.

**Stage Analysis:
What is the distribution of funding across different investment stages (e.g., Pre-seed, Seed, Series A)?**

**Geographical Analysis:
Which cities or regions (HeadQuarter) have the highest concentration of funded startups?**

## 2.0 Data Understanding
*In this phase we Load our individual datasets and after each datasets we do cleaning of the loaded dataset*

### 2.1 Installation 

```
%pip install pyodbc
%pip install python-dotenv
%pip install openpyxl
``` 

### 2.2 Importations  

```dotnetcli

import pyodbc 
from dotenv import dotenv_values 
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import plotly.express as px
import plotly.graph_objs as go
import seaborn as sns
import statsmodels.api as sm
import warnings

warnings.filterwarnings ('ignore')
```
### 2.3 Loading environment variables from .env file into a dictionary and getting credentials set in the .env file

```
environment_variables = dotenv_values('C:\\Users\\Admin\\OneDrive\\OneDrive-Azubi\\CA-LP1\\LP1-Indian-Startup-Ecosystem-1\\Notebooks\\.env')

server = environment_variables.get('server')
database = environment_variables.get('database')
username = environment_variables.get('username')
password = environment_variables.get('password')
```

### 2.4 Creating a connection string
```
connection_string = f"DRIVER={{SQL Server}};SERVER={server};DATABASE={database};UID={username};PWD={password};MARS_Connection=yes;MinProtocolVersion=TLSv1.2;"

connection = pyodbc.connect(connection_string)
```
### 2.5: Accessing the 1st dataset (2020) from SQL Server

*Query and Read the dataset from the SQL Database*
```
query = '''SELECT *
FROM dbo.LP1_startup_funding2020'''

data =pd.read_sql(query, connection)
```
**2.5.1: Once our 1st Dataset is loaded we go ahead to Expore our dataset using functions such as '.head()',  '.shape', '.isnull', '.duplicated', '.desribe()', '.unique()', '.value_counts()'**

**2.5.2: '.info' to guide in some of the realisations**
```<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1055 entries, 0 to 1054
Data columns (total 10 columns):
 #   Column         Non-Null Count  Dtype  
---  ------         --------------  -----  
 0   Company_Brand  1055 non-null   object 
 1   Founded        842 non-null    float64
 2   HeadQuarter    961 non-null    object 
 3   Sector         1042 non-null   object 
 4   What_it_does   1055 non-null   object 
 5   Founders       1043 non-null   object 
 6   Investor       1017 non-null   object 
 7   Amount         801 non-null    float64
 8   Stage          591 non-null    object 
 9   column10       2 non-null      object 
dtypes: float64(2), object(8)
memory usage: 82.5+ KB
```

**2.5.3: Findings are that:**
  - There are missing values in the 'Founded', 'HeadQuarter', 'Sector', 'Founders', 'Investor', 'Amount', 'Stage', and ;'Column10' 
  - Founded Column needs to be converted from float to integer datatype
  - Sector needs to be categorised into groupings to easily work with it 
  - Stage Column needs to be categorised 
  - Column 10 has 99.8% missing values therefore needs to be droped as its impact is minimal
  - There are 3 duplicated rows that need to be dropped 
  - We will need an 'Year' Column added 

**2.5.4: The next step, we clean our 1st dataset (2020 dataset) column by column** 

**A glimpse to cleaning one of the columns ('HeadQuarter')**

```
# Find top HeadQuarters to be used randomnly in filling missing values

top_HeadQuarter= ['Bangalore','Mumbai','Gurugram','Delhi','Chennai','Pune','New Delhi','Noida','Hyderabad','Gurgaon',
'Ahmedabad','Kolkata','Haryana','Indore','Jaipur','Thane','Kochi','Gujarat','California','Singapore']

# Fill missing value with random values from top 20 Hedquarter

def  fill_missing_HeadQuarter (HeadQuarter):
    if pd.isna(HeadQuarter):
        return np.random.choice(top_HeadQuarter)
    return HeadQuarter


data['HeadQuarter']=data['HeadQuarter'].apply(fill_missing_HeadQuarter)


# Convert all enries to title
data['HeadQuarter'] = data['HeadQuarter'].str.title()
```


**2.5.5: All cleaning is done and we add an 'Year' Column to our dataset**

**We then '.info' to confirm that our data is as it should be**

```dotnetcli

<class 'pandas.core.frame.DataFrame'>
Index: 1053 entries, 0 to 1054
Data columns (total 10 columns):
 #   Column         Non-Null Count  Dtype   
---  ------         --------------  -----   
 0   Company_Brand  1053 non-null   object  
 1   Founded        1053 non-null   int32   
 2   HeadQuarter    1053 non-null   object  
 3   Sector         1053 non-null   object  
 4   What_it_does   1053 non-null   object  
 5   Founders       1053 non-null   object  
 6   Investor       1053 non-null   object  
 7   Amount         1053 non-null   float64 
 8   Stage          1053 non-null   category
 9   Year           1053 non-null   int64   
dtypes: category(1), float64(1), int32(1), int64(1), object(6)
memory usage: 80.6+ KB
```

### 2.6: Accessing the 2nd dataset (2021) from SQL Server


**2.6.1: Following the same process for the 2nd dataset (2021) as the 1st dataset**
*The realisation was that there was so much similarity with 1st dataset (2020) interms of columns names of the dataset*

**Using the same process as the 1st dataset we load, Read and Clean the dataset**

**2.6.2: what we started with**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 1209 entries, 0 to 1208
Data columns (total 9 columns):
 #   Column         Non-Null Count  Dtype  
---  ------         --------------  -----  
 0   Company_Brand  1209 non-null   object 
 1   Founded        1208 non-null   float64
 2   HeadQuarter    1208 non-null   object 
 3   Sector         1209 non-null   object 
 4   What_it_does   1209 non-null   object 
 5   Founders       1205 non-null   object 
 6   Investor       1147 non-null   object 
 7   Amount         1206 non-null   object 
 8   Stage          781 non-null    object 
dtypes: float64(1), object(8)
memory usage: 85.1+ KB
``` 
**2.6.3: Findings**
        -There are 19 dulpicated rows that need to be dropped
        -There are missing values in the 'Founded', 'HeadQuarter','Founders', Investor', 'Amount' and 'Stage' Columns
        -Founded Column need to be converted to an integer 
        -Sector column needs to be converted to category datatype 
        -Amount Column had misplcaed vales from other columns, and needs to be converted to float datatype 
        -We will add an 'Year' column


**2.6.4: After Cleaning**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
Index: 1145 entries, 0 to 1208
Data columns (total 10 columns):
 #   Column         Non-Null Count  Dtype   
---  ------         --------------  -----   
 0   Company_Brand  1145 non-null   object  
 1   Founded        1145 non-null   int32   
 2   HeadQuarter    1145 non-null   object  
 3   Sector         1145 non-null   object  
 4   What_it_does   1145 non-null   object  
 5   Founders       1145 non-null   object  
 6   Investor       1145 non-null   object  
 7   Amount         1145 non-null   float64 
 8   Stage          1145 non-null   category
 9   Year           1145 non-null   int64   
dtypes: category(1), float64(1), int32(1), int64(1), object(6)
memory usage: 87.4+ KB
```

### 2.7: Accessing the 3nd dataset (2018) from OneDrive 

*Using pd.read_excel we load our dataset which we dounloaded from OneDrive*

**2.7.1: Following the same process for the 3rd dataset (2018) as the 2nd (2021) dataset**
*The realisation was that there was so much similarity with 1st and 2nd datasets (2020 & 2021) in-terms of column names of the dataset*

**Using the same process as the 1st dataset we load, Read and Clean the dataset**

**2.7.2: what we started with**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 89 entries, 0 to 88
Data columns (total 9 columns):
 #   Column         Non-Null Count  Dtype  
---  ------         --------------  -----  
 0   Company/Brand  89 non-null     object 
 1   Founded        60 non-null     float64
 2   HeadQuarter    70 non-null     object 
 3   Sector         84 non-null     object 
 4   What it does   89 non-null     object 
 5   Founders       86 non-null     object 
 6   Investor       89 non-null     object 
 7   Amount($)      89 non-null     object 
 8   Stage          43 non-null     object 
dtypes: float64(1), object(8)
memory usage: 6.4+ KB
```

**2.7.3: Findings**
        -There are no dulpicated rows that need to be dropped
        -There are missing values in the 'Founded', 'HeadQuarter','Founders', Investor','Sector' and 'Stage' Columns
        -Founded Column need to be converted to an integer 
        -Sector column needs to be converted to category datatype 
        -Amount Column name has dollar sign unlike other column names($),has 'Undisclosed' values, and needs to be converted to float datatype 
        -We will add an 'Year' column


**2.7.4: After Cleaning**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 89 entries, 0 to 88
Data columns (total 10 columns):
 #   Column         Non-Null Count  Dtype   
---  ------         --------------  -----   
 0   Company_Brand  89 non-null     object  
 1   Founded        89 non-null     int32   
 2   HeadQuarter    89 non-null     object  
 3   Sector         89 non-null     object  
 4   What_it_does   89 non-null     object  
 5   Founders       89 non-null     object  
 6   Investor       89 non-null     object  
 7   Amount         89 non-null     float64 
 8   Stage          89 non-null     category
 9   Year           89 non-null     int64   
dtypes: category(1), float64(1), int32(1), int64(1), object(6)
memory usage: 6.8+ KB
```

### 2.8 : Accessing the 4th dataset (2019) from the GitHub Repository
*Using pd.read_csv we load our dataset which we downloaded from the GitHub Repository*


**2.8.1: Following the same process for the 4th dataset (2019) as the 1st,2nd & 3rd datasets (2020, 2021, 2018) dataset**
*The realisation was that there was not so much similarity with 1st and 2nd & 3rd datasets (2020, 2021, 2018) in-terms of column names of the dataset also the Columns in this particular year were fewer*
*This dataset also has no missing values only the datatypes need to be converted for some columns*

**Using the same process as the 1st dataset we load, Read, Explore and Clean the dataset**

**2.8.2: what we started with**
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 526 entries, 0 to 525
Data columns (total 6 columns):
 #   Column         Non-Null Count  Dtype 
---  ------         --------------  ----- 
 0   Company Name   526 non-null    object
 1   Industry       526 non-null    object
 2   Round/Series   526 non-null    object
 3   Amount         526 non-null    object
 4   Location       526 non-null    object
 5   About Company  526 non-null    object
dtypes: object(6)
memory usage: 24.8+ KB
```

**2.8.3: Findings**
         - There was need to remaname the column names to match the first 3 datasets 
         - The Round/Series which is converted to Stage Column had links and 'Undisclosed' values that need to be replaced  
         - The Industry which is converted to Sector colum, need spliting of its values to match the other 3 datasets formats and categorise them just as the above 3 datasets
         - This dataset has 1 duplicated row 
         - The Amount Colum has both values in Dollars and Rupees which need to be converted all to dollars. Non mumeric values such as ('$' ',') need to be removed and '-' values replaced 
         - Add 'Year' Column
         


**2.8.4: After Cleaning**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
Index: 525 entries, 0 to 525
Data columns (total 7 columns):
 #   Column         Non-Null Count  Dtype   
---  ------         --------------  -----   
 0   Company_Brand  525 non-null    object  
 1   Sector         525 non-null    object  
 2   Stage          525 non-null    category
 3   Amount         525 non-null    float64 
 4   HeadQuarter    525 non-null    object  
 5   What_it_does   525 non-null    object  
 6   Year           525 non-null    int64   
dtypes: category(1), float64(1), int64(1), object(4)
memory usage: 29.9+ KB
```

## 3.0: Data Preparation
*Cleaning of our data was done after each of the datasets. In this phase we concatenate our data*
```dotnetcli
# Combine DataFrames
df_combined = pd.concat([data, data2, data3, data4], ignore_index=True)

df_combined.head()
```

**3.1: Once our Dataset is concated, we go ahead to Expore our dataset using previously used functions such as '.head()',  '.shape', '.isnull', '.duplicated', '.desribe()', '.unique()', '.value_counts()'**


**3.1.1: what we had after concating**
```dotnetcli
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2811 entries, 0 to 2810
Data columns (total 10 columns):
 #   Column         Non-Null Count  Dtype         
---  ------         --------------  -----         
 0   Company_Brand  2811 non-null   object        
 1   Founded        2286 non-null   float64       
 2   HeadQuarter    2811 non-null   object        
 3   Sector         2811 non-null   object        
 4   What_it_does   2811 non-null   object        
 5   Founders       2286 non-null   object        
 6   Investor       2286 non-null   object        
 7   Amount         2811 non-null   float64       
 8   Stage          2811 non-null   object        
 9   Year           2811 non-null   datetime64[ns]
dtypes: datetime64[ns](1), float64(2), object(7)
memory usage: 219.7+ KB
```
**3.1.2: Findings**
      - There are no duplicates in our combined dataset
      - All columns concated successfully and all column names could be read with their values 
      - Founded, Founders and Investor column still had missing values. But since this are not primary inicators on the success of the project we proceed as they are. We could also clean in Power Bi 'Transform Data'
      - Datatypes in all columns are as they should be
      
## 4.0: Hypothesis Testing
*We perform a hypothesis testing using Amova in statsmodel module*

**4.1: Findings**
```dotnetcli
                 sum_sq      df         F  PR(>F)
C(Sector)  2.801213e+20   547.0  0.041725     1.0
C(Stage)   2.054703e+20    71.0  0.235794     1.0
Residual   2.692741e+22  2194.0       NaN     NaN
P-value for Sector: 0.9999999999999999
P-value for Stage: 0.9999999999961665
Fail to reject the null hypothesis for Sector: No significant difference in funding across different sectors.
Fail to reject the null hypothesis for Stage: No significant difference in funding across different stages.
```
**4.2: Interpretation**
*The output suggests that there is no significant difference in funding across different sectors and stages of startups, according to the ANOVA test performed. However, the p-value for the Sector factor (0.042076) is lower than the typical significance level of 0.05, which could indicate some potential difference in funding across sectors, but this difference is not statistically significant based on the chosen significance level*

## 5.0 Answering the analytical questions 

*Among the analytical questions we were looking to answer, was:*

*Sector Analysis:
Which sectors have received the most funding, and how does the funding distribution vary across sectors?*

![Reference Image](/Top%20Sector%20by%20Funding%20Amount)

*Finetech sector recieved the highest funding of 150 Billion*

![Reference Image](/Funding%20Amount%20Distribution%20Among%20Sector)

*While retail sector has a steady increase in funding from 2018 to 2020*

**After answering all the 8 analytical questions we converted our combined dataset to csv for deployment into Power Bi**
```dotnetcli
df_combined.to_csv('clean_combined_LP1dataset.csv', index= False)
```
## 6.0 Deployment 
*Here is a link to the Power Bi Dashboard which we used for deployment*
<https://app.powerbi.com/view?r=eyJrIjoiZjdhZTM3ZjAtZTQ0YS00MzM1LWJkMjMtYWRjNjkwNWNjZDhkIiwidCI6IjQ0ODdiNTJmLWYxMTgtNDgzMC1iNDlkLTNjMjk4Y2I3MTA3NSJ9>

*A pictoral view of the same*

![Reference Image](/LP1_PowerBi-Dahboard.PNG)

*Here is a medium write-up for puting more words to figures. Incase its your thing click below*

## 7.0 As we wrap-up :tada: 
**7.1 Summarry**
This deep and detailed README file gives a step by step understaning and guide over a project titled Viability of setting a startup in India. It has project objective, hypothesis testing, analytical questions and instructions with snipets of the project highlighted.
It goes an extra mile to give a glimpse of the deployment methods used in this project.

**7.2 Acknowledgment**
lots of appreciation to my tutors at Azubi Africa COHORT 7, My fellow students in DAP7 for being resourceful throughout this journey.
Special thanks to team Ireland and amazing, thought provoking and dedicated team to work with.

**7.3 How to Contribute to project**
pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change 

please make sure to update tests as appropriate.

**7.4 licence**
[MIT]
(https://choosealicense.com/licenses/mit/)








































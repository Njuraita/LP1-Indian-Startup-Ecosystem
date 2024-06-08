# LP1-Indian-Startup-Ecosystem
In this project our team is tasked with an assignment to assess the viability of setting up a startup in the indian Ecosystem. We have been provided with various datasets from 2018 to 2021 which will aid us in exploration, analysis and making informed data-driven decisions. The Model we will be using is the *CRISP-DM* model using the *Agile* methodology for a good flow with our project. The *CRISP-DM* stages used in this project are excludig Evaluation and Modeling as we will not be doing any Machine Learning.Therefore the stages we will be going through are Business Understanding, Data Understanding, Data Preparation and In Deployment we will be using Power Bi and Medium for Project Article write-up.

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


**2.6.1: Following the same process for the 2nd dataset (2021)**
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

















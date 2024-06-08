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
### 2.5 Accessing the 1st dataset (2020) from SQL Server

*Query and Read the dataset from the SQL Database*
```
query = '''SELECT *
FROM dbo.LP1_startup_funding2020'''

data =pd.read_sql(query, connection)
```
Once our 1st Dataset is loaded we go ahead to Expore our dataset using functions such as '.head()', '.shape','.










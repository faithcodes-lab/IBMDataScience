<p style="text-align:center">
    <a href="https://skills.network" target="_blank">
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/assets/logos/SN_web_lightmode.png" width="200" alt="Skills Network Logo">
    </a>
</p>

<h1 align=center><font size = 5>Assignment: SQL Notebook for Peer Assignment</font></h1>

Estimated time needed: **60** minutes.

## Introduction
Using this Python notebook you will:

1.  Understand the Spacex DataSet
2.  Load the dataset  into the corresponding table in a Db2 database
3.  Execute SQL queries to answer assignment questions 


## Overview of the DataSet

SpaceX has gained worldwide attention for a series of historic milestones. 

It is the only private company ever to return a spacecraft from low-earth orbit, which it first accomplished in December 2010.
SpaceX advertises Falcon 9 rocket launches on its website with a cost of 62 million dollars wheras other providers cost upward of 165 million dollars each, much of the savings is because Space X can reuse the first stage. 


Therefore if we can determine if the first stage will land, we can determine the cost of a launch. 

This information can be used if an alternate company wants to bid against SpaceX for a rocket launch.

This dataset includes a record for each payload carried during a SpaceX mission into outer space.


### Download the datasets

This assignment requires you to load the spacex dataset.

In many cases the dataset to be analyzed is available as a .CSV (comma separated values) file, perhaps on the internet. Click on the link below to download and save the dataset (.CSV file):

 <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/data/Spacex.csv" target="_blank">Spacex DataSet</a>




```python
!pip install sqlalchemy==1.3.9

```

    Collecting sqlalchemy==1.3.9
      Downloading SQLAlchemy-1.3.9.tar.gz (6.0 MB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m6.0/6.0 MB[0m [31m52.9 MB/s[0m eta [36m0:00:00[0m
      Preparing metadata (setup.py) ... [?25done
    [?25hBuilding wheels for collected packages: sqlalchemy
      Building wheel for sqlalchemy (setup.py) ...done
    [?25h  Created wheel for sqlalchemy: filename=SQLAlchemy-1.3.9-cp312-cp312-linux_x86_64.whl size=1160111 sha256=73ba8f29bde96db98db080de0d220f011bc21868256c1965d33c45a0c1dccf01
      Stored in directory: /home/jupyterlab/.cache/pip/wheels/b3/1c/42/0e26b8d512adc6bce10ff71a05229366b4ccec641cd3b42111
    Successfully built sqlalchemy
    Installing collected packages: sqlalchemy
      Attempting uninstall: sqlalchemy
        Found existing installation: SQLAlchemy 2.0.37
        Uninstalling SQLAlchemy-2.0.37:
          Successfully uninstalled SQLAlchemy-2.0.37
    [31mERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
    jupyterhub 5.2.1 requires SQLAlchemy>=1.4.1, but you have sqlalchemy 1.3.9 which is incompatible.[0m[31m
    [0mSuccessfully installed sqlalchemy-1.3.9


### Connect to the database

Let us first load the SQL extension and establish a connection with the database



```python
!pip install ipython-sql
!pip install ipython-sql prettytable
```

    Collecting ipython-sql
      Downloading ipython_sql-0.5.0-py3-none-any.whl.metadata (17 kB)
    Collecting prettytable (from ipython-sql)
      Downloading prettytable-3.16.0-py3-none-any.whl.metadata (33 kB)
    Requirement already satisfied: ipython in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (8.31.0)
    Collecting sqlalchemy>=2.0 (from ipython-sql)
      Downloading sqlalchemy-2.0.41-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (9.6 kB)
    Collecting sqlparse (from ipython-sql)
      Downloading sqlparse-0.5.3-py3-none-any.whl.metadata (3.9 kB)
    Requirement already satisfied: six in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (1.17.0)
    Requirement already satisfied: ipython-genutils in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.2.0)
    Requirement already satisfied: greenlet>=1 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (3.1.1)
    Requirement already satisfied: typing-extensions>=4.6.0 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (4.12.2)
    Requirement already satisfied: decorator in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.19.2)
    Requirement already satisfied: matplotlib-inline in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.1.7)
    Requirement already satisfied: pexpect>4.3 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (4.9.0)
    Requirement already satisfied: prompt_toolkit<3.1.0,>=3.0.41 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (3.0.50)
    Requirement already satisfied: pygments>=2.4.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (2.19.1)
    Requirement already satisfied: stack_data in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.6.3)
    Requirement already satisfied: traitlets>=5.13.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.14.3)
    Requirement already satisfied: wcwidth in /opt/conda/lib/python3.12/site-packages (from prettytable->ipython-sql) (0.2.13)
    Requirement already satisfied: parso<0.9.0,>=0.8.4 in /opt/conda/lib/python3.12/site-packages (from jedi>=0.16->ipython->ipython-sql) (0.8.4)
    Requirement already satisfied: ptyprocess>=0.5 in /opt/conda/lib/python3.12/site-packages (from pexpect>4.3->ipython->ipython-sql) (0.7.0)
    Requirement already satisfied: executing>=1.2.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (2.1.0)
    Requirement already satisfied: asttokens>=2.1.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (3.0.0)
    Requirement already satisfied: pure_eval in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (0.2.3)
    Downloading ipython_sql-0.5.0-py3-none-any.whl (20 kB)
    Downloading sqlalchemy-2.0.41-cp312-cp312-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (3.3 MB)
    [2K   [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m3.3/3.3 MB[0m [31m60.5 MB/s[0m eta [36m0:00:00[0m
    [?25hDownloading prettytable-3.16.0-py3-none-any.whl (33 kB)
    Downloading sqlparse-0.5.3-py3-none-any.whl (44 kB)
    Installing collected packages: sqlparse, sqlalchemy, prettytable, ipython-sql
      Attempting uninstall: sqlalchemy
        Found existing installation: SQLAlchemy 1.3.9
        Uninstalling SQLAlchemy-1.3.9:
          Successfully uninstalled SQLAlchemy-1.3.9
    Successfully installed ipython-sql-0.5.0 prettytable-3.16.0 sqlalchemy-2.0.41 sqlparse-0.5.3
    Requirement already satisfied: ipython-sql in /opt/conda/lib/python3.12/site-packages (0.5.0)
    Requirement already satisfied: prettytable in /opt/conda/lib/python3.12/site-packages (3.16.0)
    Requirement already satisfied: ipython in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (8.31.0)
    Requirement already satisfied: sqlalchemy>=2.0 in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (2.0.41)
    Requirement already satisfied: sqlparse in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.5.3)
    Requirement already satisfied: six in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (1.17.0)
    Requirement already satisfied: ipython-genutils in /opt/conda/lib/python3.12/site-packages (from ipython-sql) (0.2.0)
    Requirement already satisfied: wcwidth in /opt/conda/lib/python3.12/site-packages (from prettytable) (0.2.13)
    Requirement already satisfied: greenlet>=1 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (3.1.1)
    Requirement already satisfied: typing-extensions>=4.6.0 in /opt/conda/lib/python3.12/site-packages (from sqlalchemy>=2.0->ipython-sql) (4.12.2)
    Requirement already satisfied: decorator in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.1.1)
    Requirement already satisfied: jedi>=0.16 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.19.2)
    Requirement already satisfied: matplotlib-inline in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.1.7)
    Requirement already satisfied: pexpect>4.3 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (4.9.0)
    Requirement already satisfied: prompt_toolkit<3.1.0,>=3.0.41 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (3.0.50)
    Requirement already satisfied: pygments>=2.4.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (2.19.1)
    Requirement already satisfied: stack_data in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (0.6.3)
    Requirement already satisfied: traitlets>=5.13.0 in /opt/conda/lib/python3.12/site-packages (from ipython->ipython-sql) (5.14.3)
    Requirement already satisfied: parso<0.9.0,>=0.8.4 in /opt/conda/lib/python3.12/site-packages (from jedi>=0.16->ipython->ipython-sql) (0.8.4)
    Requirement already satisfied: ptyprocess>=0.5 in /opt/conda/lib/python3.12/site-packages (from pexpect>4.3->ipython->ipython-sql) (0.7.0)
    Requirement already satisfied: executing>=1.2.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (2.1.0)
    Requirement already satisfied: asttokens>=2.1.0 in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (3.0.0)
    Requirement already satisfied: pure_eval in /opt/conda/lib/python3.12/site-packages (from stack_data->ipython->ipython-sql) (0.2.3)



```python
%load_ext sql
```


```python
import csv, sqlite3
import prettytable
prettytable.DEFAULT = 'DEFAULT'

con = sqlite3.connect("my_data1.db")
cur = con.cursor()
```


```python
!pip install -q pandas
```


```python
%sql sqlite:///my_data1.db
```


```python
import pandas as pd
df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/data/Spacex.csv")
df.to_sql("SPACEXTBL", con, if_exists='replace', index=False,method="multi")
```




    101



**Note:This below code is added to remove blank rows from table**



```python
#DROP THE TABLE IF EXISTS

%sql DROP TABLE IF EXISTS SPACEXTABLE;
```

     * sqlite:///my_data1.db
    Done.





    []




```python
%sql create table SPACEXTABLE as select * from SPACEXTBL where Date is not null
```

     * sqlite:///my_data1.db
    (sqlite3.OperationalError) table SPACEXTABLE already exists
    [SQL: create table SPACEXTABLE as select * from SPACEXTBL where Date is not null]
    (Background on this error at: https://sqlalche.me/e/20/e3q8)


## Tasks

Now write and execute SQL queries to solve the assignment tasks.

**Note: If the column names are in mixed case enclose it in double quotes
   For Example "Landing_Outcome"**

### Task 1




##### Display the names of the unique launch sites  in the space mission



```python
import pandas as pd

# Load the dataset
df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DS0321EN-SkillsNetwork/labs/module_2/data/Spacex.csv")

# Print all column names to verify
print("Columns in dataframe:", df.columns)

# Use the correct column name 'Launch_Site'
unique_launch_sites = df['Launch_Site'].unique()

# Display unique launch sites
print("Unique Launch Sites:")
print(unique_launch_sites)
```

    Columns in dataframe: Index(['Date', 'Time (UTC)', 'Booster_Version', 'Launch_Site', 'Payload',
           'PAYLOAD_MASS__KG_', 'Orbit', 'Customer', 'Mission_Outcome',
           'Landing_Outcome'],
          dtype='object')
    Unique Launch Sites:
    ['CCAFS LC-40' 'VAFB SLC-4E' 'KSC LC-39A' 'CCAFS SLC-40']



### Task 2


#####  Display 5 records where launch sites begin with the string 'CCA' 



```python
# Filter rows where Launch_Site starts with 'CCA'
cca_launches = df[df['Launch_Site'].str.startswith('CCA')]

# Display the first 5 records
print(cca_launches.head(5))

```

             Date Time (UTC) Booster_Version  Launch_Site  \
    0  2010-06-04   18:45:00  F9 v1.0  B0003  CCAFS LC-40   
    1  2010-12-08   15:43:00  F9 v1.0  B0004  CCAFS LC-40   
    2  2012-05-22    7:44:00  F9 v1.0  B0005  CCAFS LC-40   
    3  2012-10-08    0:35:00  F9 v1.0  B0006  CCAFS LC-40   
    4  2013-03-01   15:10:00  F9 v1.0  B0007  CCAFS LC-40   
    
                                                 Payload  PAYLOAD_MASS__KG_  \
    0               Dragon Spacecraft Qualification Unit                  0   
    1  Dragon demo flight C1, two CubeSats, barrel of...                  0   
    2                              Dragon demo flight C2                525   
    3                                       SpaceX CRS-1                500   
    4                                       SpaceX CRS-2                677   
    
           Orbit         Customer Mission_Outcome      Landing_Outcome  
    0        LEO           SpaceX         Success  Failure (parachute)  
    1  LEO (ISS)  NASA (COTS) NRO         Success  Failure (parachute)  
    2  LEO (ISS)      NASA (COTS)         Success           No attempt  
    3  LEO (ISS)       NASA (CRS)         Success           No attempt  
    4  LEO (ISS)       NASA (CRS)         Success           No attempt  


### Task 3




##### Display the total payload mass carried by boosters launched by NASA (CRS)



```python
total_payload_nasa_crs = df[df['Customer'] == 'NASA (CRS)']['PAYLOAD_MASS__KG_'].sum()
print(f"Total payload mass carried by NASA (CRS): {total_payload_nasa_crs} kg")

```

    Total payload mass carried by NASA (CRS): 45596 kg


### Task 4




##### Display average payload mass carried by booster version F9 v1.1



```python
avg_payload_f9v11 = df[df['Booster_Version'] == 'F9 v1.1']['PAYLOAD_MASS__KG_'].mean()
print(f"Average payload mass carried by booster F9 v1.1: {avg_payload_f9v11} kg")

```

    Average payload mass carried by booster F9 v1.1: 2928.4 kg


### Task 5

##### List the date when the first succesful landing outcome in ground pad was acheived.


_Hint:Use min function_ 



```python
# Convert 'Date' to datetime first (do this before filtering)
df['Date'] = pd.to_datetime(df['Date'], errors='coerce')

# Filter successful ground pad landings
success_ground = df[
    (df['Landing_Outcome'].str.contains('Success', case=False, na=False)) &
    (df['Landing_Outcome'].str.contains('ground pad', case=False, na=False))
]

if not success_ground.empty:
    first_success_date = success_ground['Date'].min()
    if pd.isna(first_success_date):
        print("No valid dates found for successful ground pad landings.")
    else:
        print(f"First successful landing outcome on ground pad was on: {first_success_date.date()}")
else:
    print("No successful ground pad landings found in the data.")

```

    First successful landing outcome on ground pad was on: 2015-12-22


### Task 6

##### List the names of the boosters which have success in drone ship and have payload mass greater than 4000 but less than 6000



```python
filtered_boosters = df[
    (df['Landing_Outcome'].str.contains('Success', case=False, na=False)) &
    (df['Landing_Outcome'].str.contains('drone ship', case=False, na=False)) &
    (df['PAYLOAD_MASS__KG_'] > 4000) &
    (df['PAYLOAD_MASS__KG_'] < 6000)  # change 6000 to your upper limit
]

# Get unique booster names
booster_names = filtered_boosters['Booster_Version'].unique()

print("Boosters with successful drone ship landings and payload mass between 4000 and 6000:")
print(booster_names)

```

    Boosters with successful drone ship landings and payload mass between 4000 and 6000:
    ['F9 FT B1022' 'F9 FT B1026' 'F9 FT  B1021.2' 'F9 FT  B1031.2']


### Task 7




##### List the total number of successful and failure mission outcomes



```python
mission_outcomes_counts = df['Mission_Outcome'].value_counts()
print("Mission outcomes count:")
print(mission_outcomes_counts)
```

    Mission outcomes count:
    Mission_Outcome
    Success                             98
    Failure (in flight)                  1
    Success (payload status unclear)     1
    Success                              1
    Name: count, dtype: int64


### Task 8



##### List all the booster_versions that have carried the maximum payload mass. Use a subquery.



```python
max_payload = df['PAYLOAD_MASS__KG_'].max()
boosters_max_payload = df[df['PAYLOAD_MASS__KG_'] == max_payload]['Booster_Version'].unique()
print("Booster versions that carried max payload:")
print(boosters_max_payload)
```

    Booster versions that carried max payload:
    ['F9 B5 B1048.4' 'F9 B5 B1049.4' 'F9 B5 B1051.3' 'F9 B5 B1056.4'
     'F9 B5 B1048.5' 'F9 B5 B1051.4' 'F9 B5 B1049.5' 'F9 B5 B1060.2 '
     'F9 B5 B1058.3 ' 'F9 B5 B1051.6' 'F9 B5 B1060.3' 'F9 B5 B1049.7 ']


### Task 9


##### List the records which will display the month names, failure landing_outcomes in drone ship ,booster versions, launch_site for the months in year 2015.

**Note: SQLLite does not support monthnames. So you need to use  substr(Date, 6,2) as month to get the months and substr(Date,0,5)='2015' for year.**



```python
max_payload = df['PAYLOAD_MASS__KG_'].max()
boosters_max_payload = df[df['PAYLOAD_MASS__KG_'] == max_payload]['Booster_Version'].unique()
print("Booster versions that carried max payload:")
print(boosters_max_payload)
```

    Booster versions that carried max payload:
    ['F9 B5 B1048.4' 'F9 B5 B1049.4' 'F9 B5 B1051.3' 'F9 B5 B1056.4'
     'F9 B5 B1048.5' 'F9 B5 B1051.4' 'F9 B5 B1049.5' 'F9 B5 B1060.2 '
     'F9 B5 B1058.3 ' 'F9 B5 B1051.6' 'F9 B5 B1060.3' 'F9 B5 B1049.7 ']


### Task 10




##### Rank the count of landing outcomes (such as Failure (drone ship) or Success (ground pad)) between the date 2010-06-04 and 2017-03-20, in descending order.



```python
start_date = pd.to_datetime('2010-06-04')
end_date = pd.to_datetime('2017-03-20')

filtered_dates = df[(df['Date'] >= start_date) & (df['Date'] <= end_date)]

landing_outcome_counts = filtered_dates['Landing_Outcome'].value_counts().reset_index()
landing_outcome_counts.columns = ['Landing_Outcome', 'Count']

# Sort descending
landing_outcome_counts = landing_outcome_counts.sort_values(by='Count', ascending=False)

print(landing_outcome_counts)
```

              Landing_Outcome  Count
    0              No attempt     10
    1    Failure (drone ship)      5
    2    Success (drone ship)      5
    3      Controlled (ocean)      3
    4    Success (ground pad)      3
    5     Failure (parachute)      2
    6    Uncontrolled (ocean)      2
    7  Precluded (drone ship)      1


### Reference Links

* <a href ="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20String%20Patterns%20-%20Sorting%20-%20Grouping/instructional-labs.md.html?origin=www.coursera.org">Hands-on Lab : String Patterns, Sorting and Grouping</a>  

*  <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Built-in%20functions%20/Hands-on_Lab__Built-in_Functions.md.html?origin=www.coursera.org">Hands-on Lab: Built-in functions</a>

*  <a  href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Labs_Coursera_V5/labs/Lab%20-%20Sub-queries%20and%20Nested%20SELECTs%20/instructional-labs.md.html?origin=www.coursera.org">Hands-on Lab : Sub-queries and Nested SELECT Statements</a>

*   <a href="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-3-SQLmagic.ipynb">Hands-on Tutorial: Accessing Databases with SQL magic</a>

*  <a href= "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-DB0201EN-SkillsNetwork/labs/Module%205/DB0201EN-Week3-1-4-Analyzing.ipynb">Hands-on Lab: Analyzing a real World Data Set</a>




## Author(s)

<h4> Lakshmi Holla </h4>


## Other Contributors

<h4> Rav Ahuja </h4>


<!--
## Change log
| Date | Version | Changed by | Change Description |
|------|--------|--------|---------|
| 2024-07-10 | 1.1 |Anita Verma | Changed Version|
| 2021-07-09 | 0.2 |Lakshmi Holla | Changes made in magic sql|
| 2021-05-20 | 0.1 |Lakshmi Holla | Created Initial Version |
-->


## <h3 align="center"> © IBM Corporation 2021. All rights reserved. <h3/>


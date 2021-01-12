# Data Engineering Capstone Project
The objective of this project was to create an ETL pipeline for I94 immigration, the main dataset includes data on immigration to the United States, and supplementary datasets will include data on airport codes, U.S. city demographics, and temperature data. The data model will be the base of an analytical database related to immigration events to find immigration patterns to the United States.

 ### Prerequisites
The environment needed for this project:
1. [Python 3.6](https://www.python.org/downloads/release/python-360/)
2. [PySpark](https://spark.apache.org/docs/latest/api/python/index.html)
3. [Apache Spark](https://spark.apache.org/), you can use one of the following configurations:
    - Stand along installation, check [Apache Spark documentation](https://spark.apache.org/downloads.html) 
    - [Amazon EMR Cluster](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-gs-launch-sample-cluster.html)
4. [Pandas](https://pandas.pydata.org/)

### Data pipeline
The code is in the file `Capstone Project Template.ipynb`, also it has a guide of the steps performed.


1. Load data into spark dataframes
    - load `U.S. City Demographic` Data
    - load `Airport Code Table` Data
    - load `I94 Immigration` Data
    - load `World Temperature` Data
    - load `country` Data (new csv file attached)
2. clean data (check duplicates and missing values)
    - clean `U.S. City Demographic` Data
    - clean `Airport Code Table` Data
    - clean `I94 Immigration` Data
    - clean `World Temperature` Data
    - clean `country` Data (new csv file attached)   
3. create Dimension tables
    - Create immigration calendar table, this table comes from I94 immigration dataset (arrival and departure dates)
    - Create immigration visatype table, this table comes from I94 immigration dataset (visatype)
    - Create US_city_demographic table, this table comes from `U.S. City Demographic` Data
    - create immigration country table , this table has temperature and country name data and it is generated with `I94 Immigration` and `World Temperature` Data.
    - create US_airport table, Airport information for United States.
4. create fact tables
    - create immigration_fact table, this table has relation to the dimension tables to be analyzed
5. check data
    - perform quality check operation 
    
### purpose of the final data model 
The final data model is the following:
 ![dag](/images/datamodel.png)
 

The fact table is based on immigration data `immigration_fact` table; we keep this dataset's granularity (no roll-up operation performed). This will be the primary data to analyze. The data can be checked against temperature data by country and demographics by city and state to evaluate migrations in the population.  

The `calendar`  dimension table is data transformed of the date events; it will be useful to classify the data.

The US_airport table will describe `i94port` (airport code) with the airport-related data to the location and name. 

The `country` dimension table contains global temperature by country; it is also related to country code in the `immigration_fact` table.

The `US_city_demographic` dimension table has been performed a roll-up operation to get the race data and obtain the data at the city level (using state_code and city as table keys). We can even perform another roll-up (state level) to check the data against the immigration fact on this table.  The data dictionary has detailed information about each column
(check the file `Capstone Project Template.ipynb`).
    
### Explanation of the files in the repository
1. [Capstone Project Template.ipynb](./Capstone Project Template.ipynb) jupyter notebook project with the ETL
2. [us-cities-demographics.csv](./us-cities-demographics.csv) demographics dataset. This data comes from OpenSoft. You can read more about it [here](https://public.opendatasoft.com/explore/dataset/us-cities-demographics/export/).
3. [immigration_data_sample.csv](./immigration_data_sample.csv) data sample about i94 immigration dataset. This data comes from the US National Tourism and Trade Office.[This](https://travel.trade.gov/research/reports/i94/historical/2016.html) is where the data comes from.
4. [I94_SAS_Labels_Descriptions.SAS](./I94_SAS_Labels_Descriptions.SAS) description of the columns of i94 immigration dataset.
5. [country_data.csv](./country_data.csv) country data to map country columns in i94 immigration dataset.
6. [airport-codes_csv.csv](./airport-codes_csv.csv) This is a simple table of airport codes and corresponding cities. It comes from [here](https://datahub.io/core/airport-codes#data)
5. [images](./images) folder that contains the images used in this file.

### Conclusion
The data solution has been currently addressed using Spark because it allows us to scale up on larger amounts of data and work with different data formats (in this case, SAS, Parquet, and CSV files). We are handling more than three million records; however, it is not a problem to work on it locally (which was the current approach); if the data start to increase, a different approach will be needed, like using a cloud solution. some other scenarios have been considered: 

- **If the data was increased by 100x** We can consider improving the capacity of the spark cluster, using Amazon EMR and/or hosting EC2 instances (adding nodes) to get a horizontal scaling.  
- **the pipelines would be run on a daily basis by 7 am every day** We can consider using Apache Airflow to schedule and monitor the pipelines using Directed Acyclic Graph (DAG) with a scheduled interval of daily. Airflow also has the flexibility to perform quality checks and retries.
- **If the database needed to be accessed by 100+ people** Depends on the data size, a solution could have a data warehouse in the cloud; we can handle users in a specific RDBMS, supporting concurrency. If the data has increased and we are talking about big data, a data lake (having data partitioned by the specifications of how the analytics will be performed).





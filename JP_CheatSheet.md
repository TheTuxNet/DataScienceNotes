# Jupyter Pandas Cheat Sheet


## Understanding the ETL-Process
ETL (extract, transform, load) is a type of data integration that refers to the three phases (extract, transform, and load) that are used to combine data from various sources. Data is extracted from one or different source systems, transformed into an analyzeable format, and loaded into a data warehouse or other system throughout this process.

![ETL Process](/img/etl_01.png)

The ETL process consists of three main basic components:
Input -> Transform -> Output

The task of the Input is to load different data source types into the processing tool.
At the Output, the processed data is exported back to other databases, systems, or files.
The Transformation process, on the other hand, can be divided into further sub-processes, which essentially consist of the following components:
* Filtering columns or rows
* Transforming columns or rows with mathematical or text functions
* Aggregate and reshape (groupby and pivoting)
* Combining (join and concatenate)

## Jupyter Pandas Cheat Sheet
### Introduction
Libraries loading
> import pandas as pd
>
> import numpy as np

Importing a csv-File input.csv in a dataframe df
> df = pd.read_csv('path_to/input.csv' ,sep=",") 

Exporting a dataframe df to csv file output.csv
> df.to_csv ('path_to/output.csv' , index = False, header=True) 
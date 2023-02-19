# Data Cleaning Report

## Introduction
This report summarizes the data cleaning process for the NREL Charging Data from November 2016 to October 2021. Its purpose is to document the steps taken to clean the data and ensure its quality and accuracy. Please refer to [README.md](https://github.com/MantissaMr/nrel_charging/blob/c7c870ebf0d7ee3f36f57784d394258a6618908e/README.md) for more details about the data. 

## Data Profiling
The sales data set contains 40,980 records and 16 columns. The columns include:

`driverId`
`vehicle_model`
`request_entry_time`
`expected_departure`
`station`
`start_date_time`
`end_date_time`
`start_charge`
`termin_charge`
`controlled_duration`
`miles_requested`
`max_charge_power`
`kwh_requested`
`energy_charged`
`cost_for_session`
`afterPaid`	

The [provided csv file](https://github.com/MantissaMr/nrel_charging/blob/231ba1a80ff1c47360c022072f1b192025f3ee23/dataSet_Oct2021.csv) was used to create a table in Big Query. While exploring, I found several columns with null values. Moreover, most of the columns, particularly those representing DATETIME, were actually type STRING. 

## Data Cleaning
With SQL, the following steps were taken on the fields we need for analysis:

- Removed nulls in fields of interest
- Casted `start_charge` and `termin_charge` as DATETIME 
- Casted `start_charge` and `termin_charge` as DATETIME 
- Trimmed rows containing negative and null values in `miles_requested`
- Formed `duration_hrs` as [`termin_charge` - `start_charge`] 
- Extracted `floor_level`from `station`
- Ensured the data types of all fields are as appropriate

My fields of interest became:

| Field Name | Data Type |Range | Number of records |
| -------- | -------- | -------- |-------- |
|`start_charge` | DATETIME | **2016-11-03** to **2021-10-11** | 36,599 |    
|`termin_charge` | DATETIME | **2016-11-03** to **2021-10-11** | 36,599 |
`station` | STRING | **01A** to **LV32-18** |36,599 |
|`floor_level` | STRING |**Ground Floor** to **Level 3**| 36,599 |
|`duration_hrs` | FLOAT | **0.01** to **6.46** | 36,599 |
|`miles_requested` | INTEGER | **1** to **300** | 36,599 | 
|`max_charge_power` | FLOAT|  **0.001** to **7.479** | 36,599 | 
|`energy_charged` | FLOAT | **0.00** to **68.31** | 36,599 |
|`afterPaid` | BOOLEAN | **True** OR **False** | 36,599 |

## Data Validation 
The quality of our cleaned data was evaluated based on the following metrics:

**Completeness**: The dataset was complete with no missing values.
**Accuracy**: There were no misspelled values and outliers were removed. 
**Consistency**: All inconsistent values were removed.
**Range**: All data points fall under an appropriate range.
**Type** All fields are of appropriate data types. 

All in all, fields of interest were clean, accurate, and reliable.


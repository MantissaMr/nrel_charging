# Data Cleaning Report

## Introduction
This report summarizes the data cleaning process for the NREL Charging Data from November 2016 to October 2021. Its purpose is to document the steps taken to clean the data and ensure its quality and accuracy. Please refer to [README.md](https://github.com/MantissaMr/nrel_charging/blob/c7c870ebf0d7ee3f36f57784d394258a6618908e/README.md) for more details about the dataset and the project. Also, feel free to jump around (top leftmost corner). 

## Data Profiling
The dataset contains **40,980 records** and **16 columns**. The columns include:

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

The [provided csv file](https://github.com/MantissaMr/nrel_charging/blob/main/Raw.csv) was used to create a table in Big Query. While exploring, I found several columns with null values. Moreover, most of the columns, particularly those representing DATETIME, were actually type STRING. 

## Data Cleaning
With SQL, the following actions were taken on the fields we need for analysis:

- Removed nulls in fields of interest
- Casted `start_charge` and `termin_charge` as DATETIME 
- Casted `start_charge` and `termin_charge` as DATETIME 
- Trimmed rows containing negative and null values in `miles_requested`
- Formed `duration_hrs` as [`termin_charge` - `start_charge`] 
- Extracted `floor_level`from `station`
- Extracted `year` from `termin_charge`

Fields for analyes involving charging stations:

| Field Name | Data Type |Range | Number of records |
| -------- | -------- | -------- |-------- |
`station` | STRING | **01A** to **LV32-18** |36,599 |
|`start_charge` | DATETIME | **2016-11-03** to **2021-10-11** | 36,599 |    
|`termin_charge` | DATETIME | **2016-11-03** to **2021-10-11** | 36,599 |
|`floor_level` | STRING |**Ground Floor** to **Level 3**| 36,599 |
|`duration_hrs` | FLOAT | **0.01** to **6.46** | 36,599 |
|`miles_requested` | INTEGER | **1** to **300** | 36,599 | 
|`max_charge_power` | FLOAT|  **0.001** to **7.479** | 36,599 | 
|`energy_charged` | FLOAT | **0.00** to **68.31** | 36,599 |

For analyses involving pre/post COVID-19:
| Field Name | Data Type |Range | Number of records |
| -------- | -------- | -------- |-------- |
|`year`| YEAR| **2016** to **2021**|    6|
| `energy_charged` | INTEGER | **8** to **13** |  6 |
| `miles_requested` | INTEGER | **43** to **75** |  6 |

For analysis of Free/Paid Charging:
| Field Name | Data Type |Range | Number of records |
| -------- | -------- | -------- |-------- |
|`year`| YEAR| **2016** to **2021**|    7|
| `miles_requested` | INTEGER | **43** to **75** |  7 |
|`afterPaid` | BOOLEAN | **True** OR **False** | 7|

## Data Grouping 
The grouping of the dataset reflecting the above data cleaning actions is as follows: 

- `duration_hrs` by `station` 
``` sql
WITH tidy_table1 AS 
  (
    SELECT   	station,
    		/*Casting strings to DATETIME so we can perform simple calculations later
		when finding the average duration of charging time*/
                CAST(start_charge AS DATETIME) start_charge,
                CAST(termin_charge AS DATETIME) termin_charge

    FROM 	`disco-order-yeah.nrel.charging_data`
    WHERE 	start_charge != "NULL"
          	AND termin_charge != "NULL"
          	AND miles_requested IS NOT NULL
		AND miles_requested >= 0
  )

SELECT  station,
	#converting duration to hours
	ROUND (
                AVG (
                      EXTRACT(HOUR FROM (termin_charge - start_charge)) * 3600 +
                      EXTRACT(MINUTE FROM (termin_charge - start_charge)) * 60 +
                      EXTRACT(SECOND FROM (termin_charge - start_charge))
                     )/3600
              , 2) AS
         duration_hrs  
FROM     tidy_table1
GROUP BY station
ORDER BY station  
LIMIT    200
```

- `miles_requested` by `station`
``` sql
SELECT  	station,
        	CAST(ROUND(AVG(miles_requested), 0) AS INT64) AS miles_requested
FROM 		`disco-order-yeah.nrel.charging_data`
WHERE 		start_charge != "NULL"
        	AND termin_charge != "NULL"
        	AND miles_requested IS NOT NULL 
		AND miles_requested >= 0
GROUP BY 	station 
ORDER BY 	station 
LIMIT 		200
```

- `max_charge_power` by `station`
```sql 
SELECT    station, ROUND (AVG (CAST (max_charge_power AS FLOAT64)), 2) AS max_charge_power
FROM      `disco-order-yeah.nrel.charging_data`
WHERE     start_charge != "NULL"
          AND termin_charge != "NULL"		
          AND miles_requested IS NOT NULL
          AND miles_requested >= 0  
GROUP BY  station
ORDER BY  station 
LIMIT     200
```

- `floor_level` by `station` 
```sql
SELECT 		DISTINCT station,
		CASE
		  WHEN LEFT(station, 3) = 'LV1' THEN 'Level 1'
		  WHEN LEFT(station, 3) = 'LV2' THEN 'Level 2'
		  WHEN LEFT (station, 3) = 'LV3' THEN 'Level 3'
		  ELSE 'Ground Floor'
		END AS floor_level
FROM 		`disco-order-yeah.nrel.charging_data`
WHERE 		station != "LV31-08" #this station has null for all `termin_charge` and `start_charge` fields
ORDER BY 	station
LIMIT 		200
```

- `energy_charged` by `year` 
```sql 
WITH tidy_table10 AS 
  (
    SELECT      CAST (energy_charged AS FLOAT64) energy_charged,
                CAST(termin_charge AS DATETIME) termin_charge

    FROM 	`disco-order-yeah.nrel.charging_data`
    WHERE 	termin_charge != "NULL"
          	AND energy_charged IS NOT NULL
		AND miles_requested IS NOT NULL
   	    	AND miles_requested >= 0
  )

SELECT  	EXTRACT(YEAR FROM termin_charge) as year,
        	ROUND ((AVG (energy_charged)),0) as energy_charged
FROM 		tidy_table10
GROUP BY 	year
ORDER BY 	year
```

- `miles_requested` by `year` 
```sql 
WITH tidy_table10 AS 
  (
    SELECT      miles_requested,
                CAST(termin_charge AS DATETIME) termin_charge

    FROM 	`disco-order-yeah.nrel.charging_data`
    WHERE 	start_charge != "NULL"
          	AND termin_charge != "NULL"
          	AND miles_requested IS NOT NULL 
		AND miles_requested >= 0 
  )

SELECT  EXTRACT(YEAR FROM termin_charge) as year,
        ROUND (AVG (miles_requested), 0) as miles_requested
FROM	tidy_table10
GROUP BY year
ORDER BY year
```

- `miles_requested` by `afterPaid` 
```sql 
WITH tidy_table10 AS 
  (
    SELECT      afterPaid,
                miles_requested,
                CAST(termin_charge AS DATETIME) termin_charge

    FROM        `disco-order-yeah.nrel.charging_data`
    WHERE       termin_charge != "NULL"
		AND start_charge != "NULL"
                AND miles_requested IS NOT NULL
		AND miles_requested >= 0
  )

SELECT  afterPaid,
        EXTRACT (YEAR FROM termin_charge) as year,
        CAST ((ROUND ((AVG (miles_requested)),0)) AS INTEGER) as miles_requested,
FROM 	tidy_table10
GROUP BY year, afterPaid
ORDER BY year
```

- `energy_charged` by `afterPaid` 
```sql 
WITH tidy_table10 AS 
  (
    SELECT      afterPaid,
                CAST (energy_charged AS FLOAT64) energy_charged,
                CAST(termin_charge AS DATETIME) termin_charge

    FROM 	`disco-order-yeah.nrel.charging_data`
    WHERE 	start_charge != "NULL"
          	AND termin_charge != "NULL"
          	AND miles_requested IS NOT NULL 
		AND miles_requested >= 0 
  )

SELECT  afterPaid,
        EXTRACT(YEAR FROM termin_charge) as year,
        ROUND (AVG (energy_charged), 0) as energy_charged
FROM 	tidy_table10
GROUP BY year, afterPaid
ORDER BY year
```

After the above fields have been exported, and the following actions were taken using Excel:

- Fields grouped by `station` are formed into [tidy_station.csv](https://github.com/MantissaMr/nrel_charging/blob/main/tidy_station.csv).
- Fields grouped by `year` for pre/post COVID analysis are formed into [covid_19.csv](https://github.com/MantissaMr/nrel_charging/blob/main/covid_19.csv).
- Fields grouped by `year` for free/paid charging cost are formed into [freePaid.csv](https://github.com/MantissaMr/nrel_charging/blob/main/freePaid.csv).

## Data Validation 
The quality of our cleaned data was evaluated based on the following metrics:

- **Completeness**: The dataset is complete with no missing values.
- **Accuracy**: There is no misspelled values and outliers were removed. 
- **Consistency**: All inconsistent values were removed.
- **Range**: All data points fall under an appropriate range.
- **Type** All fields are of appropriate data types. 


All in all, all fields of interest are clean, accurate, and reliable.


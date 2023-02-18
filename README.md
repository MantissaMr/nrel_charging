# NREL’s Workplace EV Charging Behavior  

## Overview

This EV Charging data, gathered and maintained by the National Renewable Energy Lab (NREL), tracks nearly 400 vehicles over a 4-5 year period (2016-2021), and how EV drivers behave at a workplace charging station. The data is further enriched by having a field named "afterPaid," which is a boolean type that indicates whether a charging session is free or paid. All stations are free to use for the first 2.5 years of operation.

**Please find the full overview of the data here:** 
Neuman, Christopher, Andrew Meintz, and Myungsoo Jun. 2021. *Workplace Charging Data Collection and Behavior*. Golden, CO: National Renewable Energy Laboratory. NREL/TP-5400-77515 https://www.nrel.gov/docs/fy22osti/77515.pdf 

## Acronyms

- **NREL** ← National Renewable Energy Laboratory
- **EV** ← Electric Vehicles

## Objectives
1. Analyze how the tracked EVs interacted with the charging stations at the NREL workspace to help NREL improve its employee EV charging experience.
2. Illustrate any potential disparity between pre & post-COVID and Free & Paid charging habits.

## Assumptions
- In the prefix “LVX” for each charging station, X indicates the floor level the charging station is positioned, while those starting with 0 and 1 denotes *Ground Floor*.
- COVID-19 Lockdown began March, 2020.

## Questions
1. At which top three charging stations do employees spend the most & least time charging their EVs?
2. What is the avg. `miles_requested` per the stations in (1) above?
3. Does the avg. `max_charge_power` expended by those station has anything to do with the avg. `miles_requested` and avg. `duration_hrs` spent charging?
4. What is the relation between the avg. `max_charge_power`to the avg. `duration_hrs` spent charging per floor?
5. How does the avg. `miles_requested` and avg. `energy_charged` per year compare pre and post COVID-19 lockdown? 
6. How does the avg. `miles_requested` differ when charging is free versus when it is paid?

## Roadmap
- **Data Preparation:** Loading dataset to Big Query, drafting project roadmap, fleshing out objectives & SOW, and setting priorities & deadlines, etc.
- **Data Processing:** Deciding on exact fields (and needed field combinations) needed to answer our questions.
- **Data Cleaning:** Carefully clearing out nulls, validating the data, and implementing data security processes like hashing personal info (if not already done). Explore the [Data Cleaning Report](https://github.com/MantissaMr/nrel_charging/blob/fa5abfa8c947c7b94d45c17d5fe277bf458720ad/data-cleaning-report.md).
- **Data Analysis:** Attempting to answer our questions by figuring out which fields to plot against which in bettering how we convey insights, trying out simple aggregations, pivot tables, etc
- **Viz Creations**: Trying out simple vizzes: Tableau and ggplot lib. in R
- **Documentations**: Drafting notebook/repo/article/documentations
- **Compilations**: Editing notebooks, creating pptx, archiving relevant files, and drafting the next project


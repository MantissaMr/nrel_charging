# NREL’s Workplace EV Charging Behavior  

## Overview

This EV Charging data, gathered and maintained by the National Renewable Energy Lab (NREL), tracks nearly 400 vehicles over a 4-5 year period (2016-2021), and how EV drivers behave at a workplace charging station. The data is further enriched by having a field named "afterPaid," which is a boolean type that indicates whether a charging session is free or paid. All stations are free to use for the first 2.5 years of operation.

**Please refer to this for full overview of the data:** 
Neuman, Christopher, Andrew Meintz, and Myungsoo Jun. 2021. *Workplace Charging Data Collection and Behavior*. Golden, CO: National Renewable Energy Laboratory. NREL/TP-5400-77515 https://www.nrel.gov/docs/fy22osti/77515.pdf 

## Acronyms

- **NREL** ← National Renewable Energy Laboratory
- **EV** ← Electric Vehicles

## Objectives
1. Analyze how the tracked EVs interacted with the charging stations at the NREL workspace to help NREL improve its employee EV charging experience.
2. Illustrate any potential disparity between pre & post-COVID and Free & Paid charging habits.

## Assumptions
- In the prefix “LVX” for each charging station, X indicates the floor level the charging station is positioned, with those starting with 0 and 1 denoting G*round Floor*.
- COVID-19 Lockdown began March, 2020.

## Questions
1. Which charging stations do employees spent the most/least time charging their EVs at?
2. What is the average miles requested per the stations in (1) above? This could give us an insight into why employees spent more time at some stations and less at others.
3. Does the average energy expended by those station has anything to do with the average miles requested or time spent charging at them? The correlation or lack thereof would point us towards exploring why some station might be faster than others at expending energy.
4. Does a station’s floor level have anything to do with how fast/slow it expends energy? 
5. How does the avg. miles requested and avg. energy charged per year compare pre & post COVID-19? This could give us insight into how NREL is returning to “normal,” or how its “new normal” looks like. 
6. How does the avg. miles requested varies from when charging is free versus paid?

## Roadmap
- Loading dataset to Big Query, drafting project roadmap, fleshing out objectives & SOW, and setting priorities & deadlines, etc
- Deciding on exact fields (and needed field combinations) needed to answer our questions
- Carefully clearing out nulls, validating the data, and implementing data security processes like hashing personal info (if not already done, for the latter
- Attempting to answer our questions by figuring out which fields to plot against which in bettering how we convey insights, trying out simple aggregations, pivot tables, etc
- Trying out simple vizzes: Tableau/ggplot in R/Google Sheets
- Drafting notebook/repo/article/documentations
- Compiling notebooks, creating pptx, archiving relevant files, and drafting the next project


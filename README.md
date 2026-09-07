# Retail Revenue Intelligence

## Overview

Retail Revenue Intelligence is a data analytics portfolio project using the UCI Online Retail II dataset.

The project uses historical retail transaction data to study revenue, customers, products, and international markets. The work includes data profiling, cleaning, SQL analysis, and a completed [Tableau Public dashboard.](https://public.tableau.com/views/RetailRevenueIntelligence/RetailRevenueIntelligenceDashboard?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)


## Dataset

The project uses the UCI Online Retail II dataset, which contains transactional data from a UK based online retailer.

The dataset includes information about customer orders, products, quantities, prices, transaction dates, and customer locations.

The transaction data covers December 1, 2009 through December 9, 2011.

The original source dataset is preserved unchanged.

## Project Structure

### Data/
**Data.md** Contains the original project dataset:
* [online_retail_raw.xlsx](https://docs.google.com/spreadsheets/d/1JU7C5gqAbly0MMKGzpAiiHr3O_2bwQGb/edit?usp=sharing&ouid=111686195064814118197&rtpof=true&sd=true) - Original UCI Online Retail II dataset preserved unchanged
* [online_retail_clean.csv](https://drive.google.com/file/d/1ZWBZTWMgOQB6PCD4jGabRkFHN7g5unFq/view?usp=sharing) - Cleaned and analysis ready dataset produced from the documented profiling and cleaning process and used for downstream SQL business analysis

### Analysis/
Contains the project's analysis workspace:
* [1_online_retail_profiling.ipynb](Analysis/1_online_retail_profiling.ipynb) - Jupyter notebook containing data profiling and quality assessment
* [2_online_retail_cleaning.ipynb](Analysis/2_online_retail_cleaning.ipynb) - Jupyter notebook containing documented data cleaning, transaction classification, revenue transformations, and validation
* [3_online_retail_sql_analysis.ipynb](Analysis/3_online_retail_sql_analysis.ipynb) - SQL business analysis of the cleaned dataset covering eight business questions across revenue, product, geographic, and customer performance, including KPI measurement and documented business findings


### Documentation/
Contains supporting documentation for the project:
* [Data Dictionary](https://docs.google.com/spreadsheets/d/1cVJE4g9CotHjt43zmOT1h58f4x3OSnPP1L0T1cM4K8Q/edit?usp=sharing) - Documents the dataset's fields, definitions, and structure.
* [Data Quality & Cleaning Log](Documentation/Data%20Quality%20&%20Cleaning%20Log.md) - Documents identified data quality issues and cleaning decisions
* [Business Analysis](Documentation/Business%20Analysis.md) - Documents the eight business questions and resulting findings across revenue, product, geographic, and customer analysis
* [Dashboard Overview](Documentation/Dashboard%20Overview.md) - Explains the purpose, main dashboard sections, data, and how the dashboard should be used.

## Dashboard

The final [Tableau Public dashboard](https://public.tableau.com/views/RetailRevenueIntelligence/RetailRevenueIntelligenceDashboard?:language=en-US&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link) provides an interactive view of business performance. It includes five main KPIs: Net Revenue, Gross Sales, Orders, Average Order Value, and Return Rate.

The dashboard also includes views for monthly revenue trends, top customers, customer purchasing behavior, top products, product revenue share, international market performance, and international revenue byUsers can interact with the dashboard by selecting a customer, product, or country from the Top Customers by Revenue, Top Products by Revenue, and International Market Performance charts. These selections filter the other dashboard views, allowing users to explore performance for specific customers, products, and markets.

 month.

The dataset ends on December 9, 2011. Because of this, December 2011 is only a partial month. The final drop in the monthly revenue chart should not be treated as a full month decline.



## Tools

* Python
* Pandas
* Google Colab
* SQL
* DuckDB
* Tableau Public


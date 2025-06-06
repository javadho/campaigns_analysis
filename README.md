# campaigns_analysis
**Objective:** Compare KPIs before and after starting campaigns and the most successful campaigns. 


**Tools used:** T-SQL (SQL Server Management Studio 2.0), Python (Jupyter Notebook), Power BI, Excel

## Table of Contents

- [Project Overview](#project-overview)
- [Dataset Description](#dataset-description)
    - [transaction_data](#transaction_data)
    - [product](#product)
    - [hh_demographic](#hh_demographic)
    - [campaigns](#campaigns)
- [Key Performance Indicators (KPIs)](#key-performance-indicators-kpis)
- [ETL Process Overview](#etl-process-overview)
- [Data Cleaning and Transformation (SQL)](#data-cleaning-and-transformation-sql)
- [EDA (Python)](#eda-python)
- [Visualizations (Power BI)](#visualizations-power-bi)
- [Key Takeaways](#key-takeaways)
- [Recommendation](#recommendation)
## Project Overview
Running campaigns can be a key strategy for stakeholders to achieve their goals. Effective campaigns have the potential to attract more customers, and when executed properly, they can boost a company’s profitability and efficiency. To evaluate campaign performance, various Key Performance Indicators (KPIs) can be applied based on the company’s objectives. In this project, six KPIs are analyzed using nearly two years of transaction data from a retail business with 2,500 households. These KPIs are then used to rank the campaigns and determine which one was the most successful.

## Dataset Description
The datasets are obtained from [the Dunnhumby website (the complete Journey)](https://www.dunnhumby.com/source-files/), which is the first data science customer platform in the world. In this project, 4 tables are used, which are described as follows.

### transaction_data
[The transaction data](https://github.com/javadho/campaigns-analysis/blob/main/datasets/raw/transaction_data.csv) shows all products purchased by households within the dataset. Each row is essentially the same line that would be found on a store receipt. It is worth mentioning that sales_value in this table is the amount of dollars received by the retailer on the sale of the specific product, not the actual price paid by the customer. Below is the description of all variables in this table.
| Variable             | Description                                                        |
|----------------------|--------------------------------------------------------------------|
| `household_key`      | Uniquely identifies each household                                 |
| `basket_id`          | Uniquely identifies a purchase occasion                            |
| `day`                | Day when transaction occurred                                      |
| `product_id`         | Uniquely identifies each product                                   |
| `quantity`           | Number of the products purchased during the trip                   |
| `sales_value`        | Amount of dollars the retailer receives from the sale                  |
| `store_id`           | Identifies unique stores                                           |
| `coupon_match_disc`  | Discount applied due to retailer’s match                           |
| `coupon_disc`        | Discount applied due to manufacturer coupon                        |
| `retail_disc`        | Discount applied due to retailer’s loyalty card programme          |
| `trans_time`         | Time of day when transaction occurred                              |
| `week_no`            | Week of the transaction. Ranges from 1 to 102                      |

### product
[The product](https://github.com/javadho/campaigns-analysis/blob/main/datasets/raw/product.csv) table shows the products purchased by households with their manufacturer and their categories.
| Variable              | Description                                                                 |
|------------------------|-----------------------------------------------------------------------------|
| `PRODUCT_ID`           | Number that uniquely identifies each product                                |
| `DEPARTMENT`           | Groups similar products together                                            |
| `COMMODITY_DESC`       | Groups similar products together at a lower level                          |
| `SUB_COMMODITY_DESC`   | Groups similar products together at the lowest level                       |
| `MANUFACTURER`         | Code that links products with the same manufacturer together                   |
| `BRAND`                | Indicates Private or National label brand                                   |
| `CURR_SIZE_OF_PRODUCT` | Indicates package size (not available for all products)                    |

### hh_demographic
[The households demographic](https://github.com/javadho/campaigns-analysis/blob/main/datasets/raw/hh_demographic.csv) table shows demographic information for a portion of households. The fields have been given generic names (classification_1, classification_2, etc.)
| Variable             | Description                                                                                           |
|----------------------|-------------------------------------------------------------------------------------------------------|
| `HOUSEHOLD_KEY`      | Uniquely identifies each household                                                                     |
| `classification_1`   | Household level demographic segmentation. Ordered values: Group1 through Group6                        |
| `classification_2`   | Household level demographic segmentation. Possible values: X, Y, Z                                     |
| `classification_3`   | Household level demographic segmentation. Ordered values: Level1 through Level12                       |
| `classification_4`   | Household level demographic segmentation. Ordered values: 1 through 5+                                 |
| `classification_5`   | Household level demographic segmentation. Ordered values: Group1 through Group6                        |
| `HOMEOWNER_DESC`     |                                                                                                        |
| `KID_CATEGORY_DESC`  |                                                                                                        |

### campaigns
The campaigns table is the merge of [the campaign](https://github.com/javadho/campaigns-analysis/blob/main/datasets/raw/campaign_table.csv) and [the campaign description](https://github.com/javadho/campaigns-analysis/blob/main/datasets/raw/campaign_desc.csv) tables. The campaign table lists the campaigns received by each household in the dataset. Each household may have received a different set of campaigns. The campaign description table gives the length of time for which a campaign runs.
| Variable        | Description                                                 |
|------------------|-------------------------------------------------------------|
| `HOUSEHOLD_KEY` | Uniquely identifies each household                           |
| `CAMPAIGN`      | Uniquely identifies each campaign. Ranges from 1 to 30       |
| `DESCRIPTION`   | Type of campaign (TypeA, TypeB, or TypeC)                    |

| Variable     | Description                                          |
|--------------|------------------------------------------------------|
| `CAMPAIGN`   | Uniquely identifies each campaign. Ranges 1–30       |
| `DESCRIPTION`| Type of campaign (TypeA, TypeB, or TypeC)            |
| `START_DAY`  | Start date of campaign                               |
| `END_DAY`    | End date of campaign                                 |


## Key Performance Indicators (KPIs)
| KPI                               | Definition                                                                 |
|-----------------------------------|---------------------------------------------------------------------------|
| **Sales Per Day (SPD)**               | The total of sales in a single day.                                       |
| **Sales Per Basket (SPB)**            | The average sales amount per household transaction or basket.             |
| **Sales Per Household (SPH)**         | The average sales amount per household.                                   |
| **Units Per Basket (UPB)**            | The average number of product units included in a single household basket.|
| **Percentage of Single Quantity per Basket (%SQB)**   | The percentage of baskets that contain only one unit of a single product. |
| **Households Per Day (HPD)**          | The number of distinct households making purchases on a given day.        |

## ETL Process Overview
After downloading the CSV files for each table, they were imported into the database in SQL Server for cleaning and transformation. Then, to do Exploratory Data Analysis (EDA) in Python, a Jupyter notebook was connected to SQL Server using the sqlalchemy library. Then, to gain insights, the data was imported into Power BI by loading it from the SQL Server database.

## Data Cleaning and Transformation ([T-SQL](https://github.com/javadho/campaigns-analysis/blob/main/dashboard_and_codes/campaigns_analysis_cleaning_and_transforming.sql))
-	Remove unnecessary columns
-	Changing data types
-	Check missing values
-	Check duplicates
-	Merge tables
-	Create a new table (campaign_table) by joining campaign tables (campaign_table and campaign_desc) as denormalization because query performance will be improved by reducing joins in frequently accessed reports or dashboards, and also because these two tables store similar data, merging them into a single table simplifies queries and maintenance.
-	Check data values, which takes a lot of time by checking each column in the tables
-	Changing the name of columns and their values in the hh_demographic table
-	Add duration, date, new_customer, and store name columns
-	Create the transactions_campaigns table to see households that used the campaign offer for purchasing
-	Create KPIs
-	Check the times of visit for each household per week

## EDA ([Python](https://github.com/javadho/campaigns-analysis/blob/main/dashboard_and_codes/campaigns_analysis_EDA.ipynb))
-	**Libraries:** sqlalchemy, pandas, numpy, seaborn, and matplotlib.
-	Check num of rows and columns, data types, null values, summary, number of unique values, central tendencies of columns for each table.
-	Check data distribution.
-	Check outliers.
-	Change the names of campaigns based on their order by time and upload the adjusted campaign table to SQL Server.

## Visualizations ([Power BI](https://github.com/javadho/campaigns-analysis/blob/main/dashboard_and_codes/campaigns_analysis_dashboard.pbix))
-	**Power Query:** Join the transaction_data and campaigns tables to see transactions that happened during campaigns.
-	Creating a data model with a star schema.
-	Change data properties wherever needed.
-	Create hierarchy for department, commodity_desc, sub_commodity_desc
-	Add campaigns_start column into transaction_data table to show if the transaction occurred before or after the campaigns have started. 
-	Add a date column to the transaction_data and transactions_campaigns tables based on the day column. 
-	**DAX:**
    -	Comparing KPIs before and after campaigns dashboard:
        -	total_baskets, total_households, total_sales, total_units, single_quantity_basket_total
        -	sales_per_day_total, sales_per_household_total, sales_per_basket_total, units_per_basket_total, %single_quantity_basket_total, household_daily, avg_visits_per_household
    -	Campaigns ranking report:
        -	total_baskets_campaigns, total_sales_campaigns, single_quantity_basket_campaigns
        -	sales_per_day_campaigns, sales_per_household_campaigns, sales_per_basket_campaigns, units_per_basket_campaigns, %single_quantity_basket_campaigns, household_daily_campaigns
        -	rank_SPD, rank_SPH, rank_SPB, rank_UPB, rank_%SQB, rank_HPD, rank_total_AHP
    -	KPIs comparison table:
        -	SPD_total_display, SPD_total_color, SPH_total_display, SPH_total_color, SPB_total_display, SPB_total_color, UPB_total_display, UPB_total_color, %SQB_total_display, %SQB_total_color, households_display, households_color, Rank
        -	CampaignGroup, Metrics
-	**Visualizations:**
    -	**Dashboard:** In this interactive dashboard, users can see total sales, weekly times of visit by household, and top products before and after the campaign started by changing the values of different slicers. Also, KPIs comparison table shows the overview of the values of KPIs before and after campaign started, which if a KPI got improved, it is displayed in green, otherwise it is displayed in red. The top products tables can also be seen by changing the given KPI from the KPIs slicers.
        -	**Slicer**: store name, department, gender, and age.
        -	**Table**: Create CampaignGroup table using DAX in KPIs comparison table and the Metrics variable. 
        -	**Clustered Bar Chart**: demonstrates top products by choosing a KPI from slicers with logarithmic scale.
        -	**Line Chart**:
            -	Trends over time for total sales by comparing sales before and after campaigns started. 
            -	Average number of times of visits by household per week.
        -	Add a **text box** as a postscript to define the KPIs abbreviations.


        ![](https://github.com/javadho/campaigns_analysis/blob/main/Dashboard.png)


    -	**Report:** In this report, all 30 campaigns are ranked based on KPIs, and then the total ranking was obtained using the AHP method by AHP Decision Maker visualization in Power BI. The obtained AHP table is then exported as CSV, and then the total ranking is obtained by ordering rows in Excel, and then it is loaded into the data in Power BI. Also, conditional formatting is used to change the color of the background to red, yellow, and green colors from worst to best. 


        ![](https://github.com/javadho/campaigns_analysis/blob/main/Report.png)


**NOTE: KPIs in the report table are obtained from transactions for households that used the campaigns' offers.**

## Key Takeaways
✅ While checking outliers for sales_value and quantity in the transactions_data table, it was found that most outliers belong to the KIOSK-GAS department. This explains the high UPB values in the KPI comparison table.

✅ From the total sales line in the visualization, it can be seen that campaigns have a positive impact on total sales. The KPI comparison confirms this by evaluating each KPI before and after the campaigns started.

✅ By reviewing the product visualization, it can be seen that GASOLINE-REG UNLEADED has the most impact on SPD and UPB both before and after the campaigns, while FLUID MILK WHITE has the most impact on HPD. However, for other KPIs, different products have a positive impact before and after campaigns started.

✅ The trend line in the second line chart shows that the average number of distinct households attracted to the stores is increasing.

✅ The ranking report shows that campaigns 23, 18, and 13 have significantly higher SPD and HPD than others, making them top-performing campaigns even though their other KPIs are not the strongest.


## Recommendation

➡️ Implement A/B testing or time-series comparisons for future campaigns to prove ROI.

➡️ Invest in stores that attract more unique households, indicating higher engagement or growth potential.

➡️ What were the main reasons for the success of the 3 best campaigns?

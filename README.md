# Amazon-Sales-Analysis
Excel + Python + SQL + Power BI Project | Retail Sales Data | From Raw Data to Business Insights

## Table of Contents
* [Project Overview](#Project-Overview)
* [Tools & Technologies](#Tools--Technologies)
* [Dataset Overview](#Dataset-Overview)
* [Data Cleaning Transformation & Visualization Process](#Data-Cleaning-Transformation--Visualization-Process)
* *[Phase 1: Cleaning in Excel](#Phase-1-Cleaning-in-Excel)
* *[Phase 2: Data Integration (ETL)](#Phase-2-Data-Integration-ETL)
* *[Phase 3: Transformation with SQL (Exploratory Data Analysis EDA)](#Phase-3-Transformation-with-SQL-Exploratory-Data-Analysis-EDA)
* *[Phase 4: Transformed SQL Table](#Phase-4-Transformed-SQL-Table)
* *[Phase 5: Transformation & Visualization in Power BI](#Phase-5-Transformation--Visualization-in-Power-BI)
* **[Measures](#Measures)
* **[Visualization](#Visualization)
* [Key Insights & Recommendations](#Key-Insights--Recommendations)
* [Summary Table](#Summary-Table)
* [Data Source](#Data-Source)

### Project Overview
This project demonstrates a full data pipeline from raw data to actionable insights. Using a dataset of 50,000 Amazon sales records, I performed data cleaning in Excel, advanced transformations using SQL, and built an interactive dashboard in Power BI to track KPIs such as total revenue, regional performance, and product category trends.

### Tools & Technologies 
- Excel
- SQLite
- Python
- Power BI
- Jupyter Notebook for SQL querying

### Dataset Overview
Columns:     
order_id, order_date, product_id, product_category, price, discount_percent, quantity_sold, customer_region, payment_method, rating, review_count, discounted_price, total_revenue

Sample Preview:
| order_id | order_date | product_id | product_category | price | discount_percent | quantity_sold | customer_region | payment_method | rating | review_count | discounted_price | total_revenue |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 13/04/2022 | 2637 | Books | 128.75 | 10 | 4 | North America | UPI | 3.5 | 443 | 115.88 | 463.52 |
| 2 | 12/03/2023 | 2300 | Fashion | 302.6 | 20 | 5 | Asia | Credit Card | 3.7 | 475 | 242.08 | 1210.4 |
| 3 | 28/09/2022 | 3670 | Sports | 495.8 | 20 | 2 | Europe | UPI | 4.4 | 183 | 396.64 | 793.28 |
| 4 | 17/04/2022 | 2522 | Books | 371.95 | 15 | 4 | Middle East | UPI | 5.0 | 212 | 316.16 | 1264.64 |
| 5 | 13/03/2022 | 1717 | Beauty | 201.68 | 0 | 4 | Middle East | UPI | 4.6 | 308 | 201.68 | 806.72 |

### Data Cleaning, Transformation and Visualization Process

#### Phase 1: Cleaning in Excel
- Date Formatting-Ensured the order_date column is recognized as a short date (Select order_date column > Go to Format > Number > Date/ Short Date in the dropdown)
- Data Validation-Checked for any typos and Performed a Null Value Audit using the filter tool (Select header row > Ctrl + Shift + L)
- Outlier Detection-Used conditional formatting on total_revenue to see if there are any unrealsitic spikes (Select total_revenue column > Home tab > Conditional Formatting > Highlight Cell Rules > Greater than > 5000 > Light Red Fill with Dark Red Text > OK)
- Standardization-Used find & replace to ensure payment_method names are consistent.

#### Phase 2: Data Integration (ETL)
** To simulate a production environment, I migrated the cleaned dataset from a flat CSV file into a SQLite Relational Database
- Engine-SQLAlchemy & SQLite
```Python
import pandas as pd
from sqlalchemy import create_engine, text
import os
```
- Process-Developed a Python script to automate the ETL (Extract, Transform, Load) process
```Python
file_path = r'C:\Users\Judith\Downloads\amazon_sales_dataset1.csv'
if os.path.exists(file_path):
    print("File found! Loading data...")
    df = pd.read_csv(file_path)
    
    # Create the SQL Database
    engine = create_engine('sqlite:///amazon_data.db')
    df.to_sql('sales', con=engine, index=False, if_exists='replace')
    print("Success! Your database 'amazon_data.db' is ready.")
else:
    print("File NOT found. Please check the path and try again.")
File found! Loading data...
Success! Your database 'amazon_data.db' is ready.
```

#### Phase 3: Transformation with SQL (Exploratory Data Analysis EDA)
1. Create the Table Schema/Structure
```sql
create_table_query = """
CREATE TABLE IF NOT EXISTS amazon_sales (
    order_id INT PRIMARY KEY,
    order_date DATE,
    product_id INT,
    product_category VARCHAR(50),
    price DECIMAL(10,2),
    discount_percent INT,
    quantity_sold INT,
    customer_region VARCHAR(50),
    payment_method VARCHAR(50),
    rating DECIMAL(2,1),
    review_count INT,
    discounted_price DECIMAL(10,2),
    total_revenue DECIMAL(10,2)
);
"""
with engine.connect() as connection:
    connection.execute(text("DROP TABLE IF EXISTS amazon_sales"))
    connection.execute(text(create_table_query))

print("Success! The 'amazon_sales' table structure has been created.")
Success! The 'amazon_sales' table structure has been created.
```
2. Key Insight Queries
   
 ~ Top 5 Regions by Revenue
```sql
region_query = """
SELECT 
    customer_region, 
    ROUND(SUM(total_revenue), 2) AS total_regional_revenue
FROM amazon_sales
GROUP BY customer_region
ORDER BY total_regional_revenue DESC
LIMIT 5;
"""
top_regions = pd.read_sql(region_query, engine)
top_regions
```
  
  ~ Category Performance
```sql
category_performance_query = """
SELECT 
    product_category,
    COUNT(order_id) AS total_orders,
    SUM(quantity_sold) AS units_sold,
    ROUND(SUM(total_revenue), 2) AS total_revenue,
    ROUND(AVG(rating), 2) AS average_rating
FROM amazon_sales
GROUP BY product_category
ORDER BY total_revenue DESC;
"""
category_stats = pd.read_sql(category_performance_query, engine)
category_stats
```
#### Phase 4: Transformed SQL Table
**  Saved the transformed SQL Table back into a high-quality CSV file that Power BI can easily read.
```sql
final_df = pd.read_sql("SELECT * FROM amazon_sales", engine)
final_df.to_csv('amazon_sales_for_powerbi.csv', index=False)
print("Export Complete! Use 'amazon_sales_for_powerbi.csv' in Power BI.")
Export Complete! Use 'amazon_sales_for_powerbi.csv' in Power BI.
```
#### Phase 5: Transformation & Visualization in Power BI
##### DAX 
** Calender Table
```DAX
Calendar Table = 
VAR MinDate = MIN(amazon_sales_for_powerbi[order_date])
VAR MaxDate = MAX(amazon_sales_for_powerbi[order_date])
RETURN ADDCOLUMNS (
    CALENDAR(MinDate, MaxDate),
    "Year", YEAR([Date]),
    "Month Name1", FORMAT([Date], "MMMM"),
    "Month Number", MONTH([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "Weekday", FORMAT([Date], "dddd")
)
```
** Measures
1. Total Revenue
```DAX
Total Revenue = SUM(amazon_sales_for_powerbi[total_revenue])
```
2. Total Quantity Sold
```DAX
Total Items = SUM(amazon_sales_for_powerbi[quantity_sold])
```
3. Average Order Value (AOV)
```DAX
Average Order Value = DIVIDE([Total Revenue], COUNT(amazon_sales_for_powerbi[order_id]))
```
4. Average Customer Rating
```DAX
Avg Rating = AVERAGE(amazon_sales_for_powerbi[rating])
```
5. Average Discount Percentage
```DAX
Avg Discount % = AVERAGE(amazon_sales_for_powerbi[discount_percent])
```

##### Visualization
** The Power BI dashboard include the following visuals:
- 📇KPI Overview (Cards)
  - Total Revenue
  - Total Quantity Sold
  - Average Order Value
  - Average Customer Rating
  - Average Discount Percentage
- 🗺️Revenue by Customer Region Map
- 🍩Total Revenue by Payment Method Donut
- 📈Total Quantity Sold by Month and Year Line Chart
- 📊Sum of Quantity Sold by Product Category Column Chart
- ⏳Date Range, Customer Region and Product Category slicers

<img width="1379" height="745" alt="Screenshot 2026-02-14 152432" src="https://github.com/user-attachments/assets/ae80b83d-b64f-4a38-bd8e-d4fa6dd42e6d" />


#### Key Insights & Recommendations
1. 📇Revenue Distribution-The "Big Three" Categories.
- Insight: Beauty, Books, and Fashion are almost tied for the lead, but Beauty is the top revenue generator at £5.55M.
- Business Answer: While we have a balanced portfolio, marketing efforts should lean into the Beauty category as it currently has the highest volume of sales (25,422 units).
2. 📈Sales Trends-Stability Over Seasonality
  - Insight: Monthly revenue remains remarkably stable, hovering between  £1.3M and £1.4M consistently throughout 2022 and 2023.
  - Business Answer: There are no drastic "seasonal slumps", suggesting that the products sold are "Evergreen" (essential goods) rather than highly seasonal items. The highest peak occured in January 2023 (£1.46M).
    ** Since January is typically a strong month this suggests a "New Year, New Me" buying trend or successful post-holiday clearance sales. We should recommend increased inventory levels in late December to prepare for this surge.
3. 🗺️Regional Revenue Pillars
- Insight: The Middle East is the most profitable region with £8.30M revenue, contributing the highest share of the £32.87M total revenue.
- Business Answer: Marketing spend is currently most effective in the Middle East. For the business to grow, we should either double down on this region or analyze why the North America and Europe regions are trailing behind in total spend.
4. ⚖️Discount Efficiency-The 20% Threshold
- The Insight: The data shows diminishing returns on high discounts. Orders with a 20% discount move the most volume (3.03 units avg.), but increasing the discount to 30% actually sees a slight drop in average units sold.
- Business Answer: To maximize profit margins, the business should cap standard promotions at 20%. Steeper discounts (30%+) do not appear to drive enough additional volume to justify the loss in margin.

#### Summary Table
| Product Category | Total Revenue | Avg. Rating | Total Units Sold |
| :--- | :--- | :--- | :--- |
| Beauty | £5,550,624.97 | 2.99 | 25,422 |
| Books | £5,484,863.03 | 3.02 | 25,065 |
| Fashion | £5,480,123.34 | 2.99 | 25,089 |
| Home & Kitchen | £5,473,132.55 | 3.00 | 24,743 |
| Electronics | £5,470,594.03 | 2.99 | 24,898 |
| Sports | £5,407,235.82 | 3.00 | 24,753 |

#### Data Source
[Download Here](https://www.kaggle.com/datasets/aliiihussain/amazon-sales-dataset)























































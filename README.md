# Amazon-Sales-Analysis
Excel + SQL + Power BI Project | Retail Sales Data | From Raw Data to Business Insights

### Project Overview
This project demonstrates a full data pipeline from raw data to actionable insights. Using a dataset of 50,000 Amazon sales records, I performed data cleaning in Excel, advanced transformations using SQL, and built an interactive dashboard in Power BI to track KPIs such as total revenue, regional performance, and product category trends.

### Tools & Technologies 
- SQL
- Excel
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

#### Phase 2: Data Integration
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
#### Phase 4: Save Transformed SQL Table
**  Saved the transformed SQL Table back into a high-quality CSV file that Power BI can easily read.
```sql
final_df = pd.read_sql("SELECT * FROM amazon_sales", engine)
final_df.to_csv('amazon_sales_for_powerbi.csv', index=False)
print("Export Complete! Use 'amazon_sales_for_powerbi.csv' in Power BI.")
Export Complete! Use 'amazon_sales_for_powerbi.csv' in Power BI.
```
#### Phase 5: Visualization-Dashboard in Power BI





























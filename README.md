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

#### Phase 2: Transformation with SQL
**Loaded my cleaned Excel data into a SQL Server database.






























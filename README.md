# AtliQ Hardware Sales-Dashboard


## Problem Statement

This project demonstrates the process of importing, cleaning, analysing, and visualising sales data using MySQL and Power BI. The goal is to transform raw sales data into meaningful insights and present them in an interactive Power BI dashboard.This dashboard helps the sales director Bhavin Patel understand and track the sales better. It helps him the complete business insights and the weakest areas that need to be focused. Through different charts, he gets to know their improvement area, & thus they can improve their sales by identifying these areas. Thus by using this dashboard he has identified this problem. 

### Steps followed 

- Step 1: Imported the dump-db data into MYSQL, analysed the data and identified negative values in the transaction table. 

- Step 2: Analysed markets table and found there were 2 countries USA and Paris without zones which was completely irrelevant to the cities of India.

- Step 3: In the transactions table found negative value -1 and few transactions were in USD and the majority of other transactions were in INR. USD has to be converted to INR to avoid a problem that might occur while calculating total sum of revenue.

- Step 4: To find total records in the transaction table. The following query was written:
  
SELECT count(*) FROM sales.transactions;

Output: 150283 records

- Step 5: Checked the total number of customers. The following query was written:
  
SELECT count(*) FROM customers;

Output: 38 counts

- Step 6: Checked the transactions only for Chennai (market code for chennai is Mark001). The following query was written:
  
SELECT * FROM transactions where market_code='Mark001';

- Step 7: Checked how many transactions in the USD currency. The following query was written:
  
SELECT * FROM transactions where currency="USD";

Output: 2
  
- Step 8: Visualised transactions in 2020 joined by date table. Performed inner join between the two tables “transactions” and “date”. The following query was executed:
   
SELECT transactions.*, date.* FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020;

- Step 9: Checked total revenue in year 2020. The following query was written:
  
SELECT SUM(transactions.sales_amount) from transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020;

Output: 142235559 INR revenue

- Step 10: Checked total revenue in the year 2020 in Chennai. The following query was written:
  
SELECT SUM(transactions.sales_amount) FROM transactions INNER JOIN date ON transactions.order_date=date.date where date.year=2020 and transactions.market_code="Mark001";`

Output: 2463024 INR revenue

- Step 11: Checked distinct product codes that were sold in Chennai. The following query was written:
  
SELECT distinct product_code FROM transactions where market_code='Mark001';

In Power BI(ETL)

- Step 12: Established relationship between 2 tables manually in the data model by dragging and dropping. Sales markets(markets_code) to sales transactions(market_code) and sales date(date) to sales transactions(order_date) which is in the format of the star schema.

- Step 13: In the power query editor data cleaning was performed:
Used filter in the zone column to exclude blank which removed New York and Paris from the table.

- Step 14: In sales transaction table, filter was used on sales_amount column to exclude -1 and 0.

- Step 15: Created a conditional column renamed it as norm_sales_amount, where column name as ‘currency’ equals value as ‘USD’ Then output as ‘1’.

- Step 16: Converted USD to INR writing the following DAX expression.
  
= Table.AddColumn(#"Filtered Rows", "norm_sales_amount", each if [currency] = "USD" then [sales_amount]*75 else [sales_amount])

- Step 17: Cross verified currency column using filter in the table sales transaction found another USD. Due to some reason found extra character in USD which is "USD#(cr)".

- Step 18: Identified the second USD wasn’t converted while using the DAX expression

- Step 19: To convert the second USD, the following DAX expression was written:
  
= Table.SelectRows(#"Added Conditional Column", each ([currency] = "USD" or [currency] = "USD#(cr)"))

- Step 20: Analysed the data in MySQL for the transaction table and executed this following query:
  
Select distinct(transactions.currency) FROM transactions;

- Step 21: Identified two INR and two USD from which one was INR and another was INR\r

- Step 22: Executed the following query to check INR\r count
  
select(*) FROM transactions where transactions.currency=’INR\r’;

Output: 150000 records 

- Step 23: Checked INR counts using the following query.
  
select(*) FROM transactions where transactions.currency=’INR’;

Output: 279 records

- Step 24: Checked USD counts using the following query.
  
select(*) from transactions where transactions.currency=’USD’;

Output: 2 records

- Step 25: Checked USD\r counts using the following query.
  
select(*) from transactions where transactions.currency=’USD\r’;

Output: 2 records

- Step 26: Compared USD\r and USD to check if they are duplicated by executing the following query.
  
select(*) from transactions where transactions.currency=’USD\r’ or transactions.currency=’USD’;

- Step 27: Since majority of the record is INR\r so it was kept as it is and the rest was removed from the table using PBI.

- Step 28 : Utlised power query editor to exclude INR and USD in currency column using filter from sales transaction table  


Building the dashboard

- Step 29: Created new table called basemeasure which is used to plot different UI elements on the dashboard.

- Step 30: Created new measure out of basemeasure and the following DAX expression was written to find total revenue.
  
Revenue = SUM('sales transactions'[sales_amount])

- Step 31: Created another new measure out of basemeasure and the following DAX expression was written to find Total sales quantity.
  
Sales Qty = sum('sales transactions'[sales_qty])

- Step 32: Two cards were added to the canvas one representing revenue and another representing sales quantity
  
Snapshot of revenue

![Screenshot 2024-06-06 182439](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/d1093113-7d8c-459d-85c6-1cd0583f917c)

Snapshot of sales quantity

![Screenshot 2024-06-06 182451](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/3606dd3d-152a-4501-bd14-eb3e9dbded8d)


- Step 33: Two 2 bar charts were also added to the report design area to represent revenue by region and sales Qty by region. While creating these visuals, markets_name was added to the Y axis and revenue to the X axis for revenue by region and for sales qty by region, markets_name to the Y axis and sales Qty to the X axis.

Snapshot of revenue by region

![Screenshot 2024-06-06 182505](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/a52adde8-e5f3-49a3-a259-6aebe7860106)

Snapshot of sales Qty by region

![Screenshot 2024-06-06 182520](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/a994f11d-c747-4e27-be3b-4d19013992fd)

- Step 34: Two Visual filers(slicers) to show years and dates 

- Step 35: Two more stacked bar charts were used to show top 5 customers and top 5 products based on revenue using the filters pane. Selected filter type as Top N for both of them.

Snapshot of Top 5 customers

![Screenshot 2024-06-06 182531](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/36786c76-50ad-4ed5-bec2-ad0b8fe06c76)

Snapshot of Top 5 products

![Screenshot 2024-06-06 182541](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/fdc96341-dc1b-446f-ba74-c338d57815a2)

- Step 36: Added line chart to the canvas to represent revenue trend. While creating this visual, revenue was added to the Y axis and cy_date to the X axis.

Snapshot of revenue trend

![Screenshot 2024-06-06 182602](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/6f02d160-4139-4909-8695-133daac59bcf)


### Report Snapshot (Power BI DESKTOP)

![Screenshot 2024-06-06 184224](https://github.com/AzeemArjunan/Sales-Insight-PBI/assets/171835611/174a4a30-95bb-4538-8fa6-861b47be1eb9)

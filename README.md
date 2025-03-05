# Blinkit_Sales_Analysis_Sql
## Objective
This project aims to analyze Blinkit's sales data to uncover key insights into sales performance across various dimensions, including fat content, item type, outlet characteristics, and geographic distribution. By leveraging SQL queries, we assess how different factors influence total sales and other key performance indicators (KPIs) such as average sales, number of items sold, and customer ratings. The analysis provides a comprehensive understanding of sales trends, helping businesses optimize product offerings, improve outlet efficiency, and enhance customer satisfaction.

#### *Creating database*

```sql
CREATE DATABASE blink_it;
USE blink_it;
```

#### Data Cleaning
```sql
ALTER TABLE blinkitgrocerydata RENAME COLUMN ï»¿Item_Fat_Content TO Item_fat_content;
```


```sql
UPDATE blinkitgrocerydata
SET item_fat_content = 
CASE WHEN item_fat_content IN ('LF', 'low fat', 'lowfat') THEN 'Low Fat'
WHEN item_fat_content  = 'reg' THEN 'Regular' ELSE Item_Fat_Content END;
```

```sql
SELECT DISTINCT Item_fat_content  FROM blinkitgrocerydata;
```

####                                                      KPI

#### Total Sales
```sql 
SELECT 
    CONCAT(CAST(SUM(total_sales) / 1000000 AS DECIMAL (10 , 2 )),
            ' M') AS total_sales_million
FROM
    blinkitgrocerydata;
```

#### 2 Average sales
```sql 
SELECT ROUND(AVG(total_sales),0) AS Average_sales FROM blinkitgrocerydata;
```

#### 3 Average Rating
```sql
SELECT ROUND(AVG(rating),1) Average_rating FROM blinkitgrocerydata;
```
###                                               EDA


#### 1  Fat Content Analysis:
```sql
SELECT item_fat_content, 
	ROUND(SUM(Total_sales),2) AS Total_sales,
    ROUND(AVG(Total_sales),2) AS Average_sales,
    COUNT(*) AS No_of_items,
    ROUND(AVG(Rating),2) AS Average_Rating
FROM blinkitgrocerydata
GROUP BY item_fat_content;
```
### 2 Item Type Analysis:
#### Top 5 itemtypes by sales
```sql
SELECT Item_Type,
	ROUND(SUM(Total_sales),2) AS Total_sales,
    ROUND(AVG(Total_sales),2) AS Average_sales,
    COUNT(*) AS No_of_items,
    ROUND(AVG(Rating),2) AS Average_Rating
FROM blinkitgrocerydata
GROUP BY Item_Type
ORDER BY Total_sales DESC
LIMIT 5;
```

#### 3 Fat Content by Outlet for Total Sales
```sql
SELECT 
    Outlet_Location_Type,
    ROUND(SUM(CASE
                WHEN item_fat_content = 'Low Fat' THEN Total_Sales
            END),
            2) AS Low_Fat,
    ROUND(SUM(CASE
                WHEN item_fat_content = 'Regular' THEN Total_Sales
            END),
            2) AS Regular
FROM
    blinkitgrocerydata
GROUP BY Outlet_Location_Type;
```

#### 4 Total Sales by Outlet Establishment
```sql
SELECT 
    Outlet_Establishment_Year,
    CAST(SUM(Total_Sales) AS DECIMAL (10 , 2 )) AS Total_Sales
FROM
    blinkitgrocerydata
GROUP BY Outlet_Establishment_Year
ORDER BY Outlet_Establishment_Year;
```

#### 5. Percentage of Sales by Outlet Size:
```sql
SELECT 
	Outlet_Size, 
	ROUND(SUM(total_sales),2) AS Total_sales,
	CONCAT(ROUND(SUM(total_sales)*100/ SUM(SUM(Total_Sales)) OVER(),1),'%') AS sales_percent
FROM blinkitgrocerydata
GROUP BY 
	Outlet_Size;
```
#### 6 Total Sales by Outlet Loacation
```sql
SELECT * FROM blinkitgrocerydata;
SELECT 
    Outlet_Location_type,
    CAST(SUM(Total_Sales) AS DECIMAL (10 , 2 )) AS Total_Sales,
    CONCAT(ROUND(SUM(total_sales)*100/ SUM(SUM(Total_Sales)) OVER(),1),'%') AS sales_percent
FROM
    blinkitgrocerydata
GROUP BY Outlet_Location_type;
```
#### 7. All Metrics by Outlet Type:
```sql
SELECT Outlet_Type,
	ROUND(SUM(Total_sales),2) AS Total_sales,
	CONCAT(ROUND(SUM(total_sales)*100/ SUM(SUM(Total_Sales)) OVER(),1),'%') AS sales_percent,
    ROUND(AVG(Total_sales),2) AS Average_sales,
    COUNT(*) AS No_of_items,
    ROUND(AVG(Rating),2) AS Average_Rating
FROM blinkitgrocerydata
GROUP BY Outlet_Type;
```

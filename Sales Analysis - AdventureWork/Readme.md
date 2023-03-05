# 📈 Sales Analysis - AdventureWorks
## 🛠️ Bussiness Task

AdventureWorks want to move from static reports to visual dashboards. 
They want to focus it on how much they have sold of what products, to which clients and how it has been over time.
Each sales person works on different products and customers, it would be beneficial to be able to filter them also.

In this case study, we use SQL Server to clean and transform data. Then, we make the Sales Dashboard by Power BI that target to complete the taskes below:
  1. Analyze historical sales performance.
  2. Identify bestselling products and attributed customers and regions.

---
## Step 1: Clean and Transform data
### FACT_InternetSales Table

```TSQL
SELECT 
  [ProductKey], 
  [OrderDateKey], 
  [DueDateKey], 
  [ShipDateKey], 
  [CustomerKey], 
  [SalesOrderNumber],  
  [SalesAmount]
FROM 
  [AdventureWorksDW2019].[dbo].[FactInternetSales]
WHERE 
  LEFT (OrderDateKey, 4) >= YEAR(1/1/2021)-4 -- Ensures we always only bring 4 years of date from extraction.
ORDER BY
  OrderDateKey ASC
```
### DIM_Products Table

```TSQL
SELECT 
  p.[ProductKey], 
  p.[ProductAlternateKey] AS ProductItemCode,  
  p.[EnglishProductName] AS [Product Name], 
  ps.EnglishProductSubcategoryName AS [Sub Category], -- Joined in from Sub Category Table
  pc.EnglishProductCategoryName AS [Product Category], -- Joined in from Category Table
  p.[Size] AS [Product Size], 
  p.[ProductLine] AS [Product Line], 
  p.[ModelName] AS [Product Model Name], 
  p.[EnglishDescription] AS [Product Description], 
  ISNULL (p.Status, 'Outdated') AS [Product Status] 
FROM 
  [AdventureWorksDW2019].[dbo].[DimProduct] as p
  LEFT JOIN dbo.DimProductSubcategory AS ps ON ps.ProductSubcategoryKey = p.ProductSubcategoryKey 
  LEFT JOIN dbo.DimProductCategory AS pc ON ps.ProductCategoryKey = pc.ProductCategoryKey 
order by 
  p.ProductKey asc
```

### DIM_Customers Table

```TSQL
SELECT 
  c.customerkey AS CustomerKey, 
  c.firstname AS [First Name], 
  c.lastname AS [Last Name], 
  c.firstname + ' ' + lastname AS [Full Name], 
  -- Combined First and Last Name
  CASE c.gender WHEN 'M' THEN 'Male' WHEN 'F' THEN 'Female' END AS Gender,
  c.datefirstpurchase AS DateFirstPurchase, 
  g.city AS [Customer City], -- Joined in Customer City from Geography Table
  g.EnglishCountryRegionName AS [Country]
FROM 
  DimCustomer as c
  LEFT JOIN dbo.dimgeography AS g ON g.geographykey = c.geographykey 
ORDER BY 
  CustomerKey ASC -- Ordered List by CustomerKey
```
### DIM_Date Table

```TSQL
SELECT 
  [DateKey], 
  [FullDateAlternateKey] AS Date, 
  [EnglishDayNameOfWeek] AS Day, 
  [EnglishMonthName] AS Month, 
  Left([EnglishMonthName], 3) AS MonthShort,   -- Useful for front end date navigation and front end graphs.
  [MonthNumberOfYear] AS MonthNo, 
  [CalendarQuarter] AS Quarter, 
  [CalendarYear] AS Year 
FROM 
 [AdventureWorksDW2019].[dbo].[DimDate]
WHERE 
  CalendarYear >= 2017
 ```
---
## Step 2: Import data from SQL Server to Power BI Destop

<p align="center">
<img src="https://github.com/LotteyPham/Power-BI/blob/main/Sales%20Analysis%20-%20AdventureWork/Image/ImpData.jpg" align="center">

---
## Step 3: Build Entity Relationship Diagram

<p align="center">
<img src="https://github.com/LotteyPham/Power-BI/blob/main/Sales%20Analysis%20-%20AdventureWork/Image/EDR.png" align="center">

---
## Step 4: Create Sales Dashboard

Link view the interactive dashboard: <url = "https://bit.ly/3ZExgPF">

<p align="center">
<img src="https://github.com/LotteyPham/Power-BI/blob/main/Sales%20Analysis%20-%20AdventureWork/Image/SalesPage.jpg" align="center">

<p align="center">
<img src="https://github.com/LotteyPham/Power-BI/blob/main/Sales%20Analysis%20-%20AdventureWork/Image/ProductPage.jpg" align="center">

---
## Step 5: Publish to website by Power BI Service



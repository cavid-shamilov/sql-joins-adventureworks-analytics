cavid-shamilov/sql-joins-adventureworks-analytics
# 📊 Advanced SQL JOINs Analysis - AdventureWorks

## 📌 Project Overview
This project showcases real-world analytical SQL queries focusing on various `JOIN` operations using the **AdventureWorks** dataset in Microsoft SQL Server.

## 🛠️ Concepts Covered
* **INNER JOIN & LEFT JOIN:** Relational filtering and multi-table entity resolution.
* **FULL OUTER JOIN:** Identifying non-overlapping datasets and churn/gap analysis.
* **CROSS JOIN:** Generating product-territory coverage matrices.
* **SELF JOIN:** Uncovering internal table relationships and avoiding duplicate pairs.
* **Multi-Table Joins & Anti-Patterns:** Resolving the "Inner Join Trap" using `COALESCE`.

## 📁 Repository Structure
* `scripts/`: Production-ready T-SQL queries categorized by complexity.
* `data/`: Dataset details and relational mapping notes.

---
*Created by [Cavid] - Aspiring Data Analyst*


Scenario 1: The customer relations team requires an operational audit of purchasing customers from the 2016 fiscal year. Retrieve customer contact details (FirstName, LastName, EmailAddress) alongside their corresponding sales order numbers (OrderNumber) for all 2016 transactions.

```sql
select 
FirstName,
LastName,
EmailAddress,
OrderNumber
from AdventureWorks_Customers as AC
inner join AdventureWorks_Sales_2016 as AS2016
on AC.CustomerKey=AS2016.CustomerKey
```

Scenario 2: The finance team needs a regional sales report for the year 2017. Retrieve the order numbers, order dates, regions, and countries for all transactions recorded in 2017.

```sql
select ordernumber,
OrderDate,
Region,
Country
from AdventureWorks_Sales_2017 as AS2017
inner join AdventureWorks_Territories as AWT
on AS2017.TerritoryKey=AWT.SalesTerritoryKey
```

Scenario 3: The product management team is analyzing item-level performance for 2017 to optimize inventory allocation. Retrieve the order numbers, product names, and SKUs for all products sold during the year 2017.

```sql
select OrderNumber,
ProductName,
ProductSKU
from AdventureWorks_Sales_2017 as AS2017
inner join AdventureWorks_Products as AP
on AS2017.ProductKey=AP.ProductKey
```

Scenario 4: The category management team is conducting a detailed review of product sales across categories and subcategories for 2017. Retrieve the order numbers, product names, subcategory names, and main category names for all transactions completed in 2017.

```sql
select OrderNumber,
ProductName,
SubcategoryName,
CategoryName
from AdventureWorks_Sales_2017 as AS2017
inner join AdventureWorks_Products as AP
on AS2017.ProductKey=AP.ProductKey
inner join AdventureWorks_Product_Subcategories as APS
on AP.ProductSubcategoryKey=APS.ProductSubcategoryKey
inner join AdventureWorks_Product_Categories as APC
on APS.ProductCategoryKey=APC.ProductCategoryKey
```

Scenario 5: The marketing team is reviewing purchasing patterns within the European market. Retrieve customer first names, last names, order numbers, order dates, and email addresses for all sales transactions originating from France in 2017.

```sql
select firstname,
LastName,
OrderNumber,
EmailAddress,
Country
from AdventureWorks_Customers as AC 
inner join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
inner join AdventureWorks_Territories as AWT
on AS2017.TerritoryKey=AWT.SalesTerritoryKey
where Country='France'
```

Scenario 6: The Sales Director requires a high-level performance summary by product category for 2017. Retrieve each category name alongside the total quantity sold (TotalQuantity) and total revenue generated (TotalRevenue) for all transactions in 2017.

```sql
select CategoryName,
sum(OrderQuantity) as [Total Quantity],
SUM(AS2017.OrderQuantity * AP.ProductPrice) AS [TotalRevenue]
from AdventureWorks_Sales_2017 as AS2017
inner join AdventureWorks_Products as AP
on AS2017.ProductKey=AP.ProductKey
inner join AdventureWorks_Product_Subcategories as APS
on AP.ProductSubcategoryKey=APS.ProductSubcategoryKey
inner join AdventureWorks_Product_Categories as APC
on APS.ProductCategoryKey=APC.ProductCategoryKey
group by CategoryName
```

Scenario 7: Executive management wants to identify high-performing product subcategories in the Australian market for 2017. Retrieve the subcategory names and total quantities sold (TotalQuantity) for all subcategories in Australia that achieved a total volume of 100 units or more in 2017.

```sql
select SubcategoryName,
sum(orderquantity) as [Total Quantity]
from AdventureWorks_Territories as AWT
inner join AdventureWorks_Sales_2017 as AS2017
on AWT.SalesTerritoryKey=AS2017.TerritoryKey
inner join AdventureWorks_Products as AP
on AS2017.ProductKey=AP.ProductKey
inner join AdventureWorks_Product_Subcategories as APS
on AP.ProductSubcategoryKey=APS.ProductSubcategoryKey
where country='Australia'
group by SubcategoryName
having sum(orderquantity)>=100
```
Scenario 8: The Marketing Director wants to identify the top 5 customers who purchased the highest volume of products in 2017 for a loyalty reward program. Retrieve the first names, last names, email addresses, and total quantities purchased (TotalProducts) for the top 5 customers, sorted in descending order of total quantity.

```sql
select top 5
firstname,
LastName,
EmailAddress,
sum(orderquantity) as [Total Quantity]
from AdventureWorks_Customers as AC
inner join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
group by FirstName,LastName,EmailAddress
order by [Total Quantity] desc
```
Scenario 9: The finance team wants to analyze order volume across product categories in the United States for the fiscal year 2017. Retrieve each product category name alongside the count of unique sales orders (TotalOrders) placed in the United States during 2017.

```sql
select CategoryName,
count(distinct ordernumber) as [Count Order]
from AdventureWorks_Territories as AWT
inner join AdventureWorks_Sales_2017 as AS2017
on AWT.SalesTerritoryKey=AS2017.TerritoryKey
inner join AdventureWorks_Products as AP
on AP.ProductKey=AS2017.ProductKey
inner join AdventureWorks_Product_Subcategories as APS
on APS.ProductSubcategoryKey=AP.ProductSubcategoryKey
inner join AdventureWorks_Product_Categories as APC
on APC.ProductCategoryKey=APS.ProductCategoryKey
where country='United States'
group by CategoryName
```

Scenario 10: Executive management is evaluating high-value customer segments within the North American region (United States and Canada) for 2017. Retrieve the customer first names, last names, total quantities purchased (TotalQuantity), and total revenue generated (TotalRevenue) for customers in the United States or Canada who spent more than 2,500 in 2017. Sort the results by total revenue in descending order.

```sql
select FirstName,
lastname,
sum(orderquantity) as [Total Quantity],
sum(OrderQuantity*ProductPrice) as [Total Revenue]
from AdventureWorks_Customers as AC
inner join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
inner join AdventureWorks_Territories as AWT
on AWT.SalesTerritoryKey=AS2017.TerritoryKey
inner join AdventureWorks_Products as AP
on AP.ProductKey=AS2017.ProductKey
where Country='United States' or Country='Canada'
group by FirstName,LastName
having sum(OrderQuantity*ProductPrice)>2500
order by [Total Revenue] desc
```

Scenario 11: The CRM team is identifying inactive customers who registered in the system but placed no orders during the 2017 fiscal year. Retrieve all customers (CustomerKey, FirstName, LastName, EmailAddress) alongside their 2017 sales orders, ensuring customers with zero orders in 2017 are included.

```sql
select firstname,
lastname,
EmailAddress,
OrderNumber
from AdventureWorks_Customers as AC
left join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
```

Scenario 12: The CRM team wants to isolate strictly the inactive customer segment for a targeted re-engagement campaign. Modify your query to retrieve only those customers who placed zero orders during the entire year of 2017

```sql
select firstname,
lastname,
EmailAddress,
OrderNumber
from AdventureWorks_Customers as AC
left join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
where OrderNumber is null
```

Scenario 13: A customer analyst is evaluating order frequency across the entire customer base for 2017. Retrieve every customer's first name, last name, occupation, and total order count (TotalOrders) for 2017, ensuring customers with no purchases display a count of 0.

```sql
select firstname,
lastname,
Occupation,
count(distinct OrderNumber) as [Total Order]
from AdventureWorks_Customers as AC
left join AdventureWorks_Sales_2017 as AS2017
on AC.CustomerKey=AS2017.CustomerKey
group by FirstName,LastName,Occupation
```

Scenario 14: The product management team is analyzing product performance for 2017 across the entire product catalog. Retrieve the product SKU, product name, model name, and total quantity sold (TotalQuantitySold) for all products in the catalog, ensuring products with zero sales in 2017 are included.

```sql
select ProductSKU,
productname,
modelname,
isnull(sum(orderquantity), 0) as [Total Quantity]
from AdventureWorks_Products as AP
left join AdventureWorks_Sales_2017 as AS2017
on AP.ProductKey=AS2017.ProductKey
group by productsku,ProductName,ModelName
```

Scenario 15: The quality control and returns department is analyzing product defect rates across the entire catalog. Retrieve the product name, color, list price, and total return quantity (TotalReturnQuantity) for all products, ensuring products with no return history display a count of 0.

```sql
select productname,
ProductColor,
ProductPrice,
isnull(sum(returnquantity), 0) as[Total Return Quantity]
from AdventureWorks_Products as AP
left join AdventureWorks_Returns as AR
on AP.ProductKey=AR.ProductKey
group by ProductName,ProductColor,ProductPrice
```

Scenario 16: The regional strategy team wants to audit territory activity for 2017 to identify underperforming or inactive regions. Retrieve the region name, country, territory group, and total order count (TotalOrders) for 2017 across ALL territories, ensuring regions with zero sales display 0.



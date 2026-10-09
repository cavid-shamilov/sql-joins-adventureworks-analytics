cavid-shamilov/sql-joins-adventureworks-analytics# 
📊 Advanced SQL JOINs Analysis - AdventureWorks

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


Scenario 2: The customer relations team requires an operational audit of purchasing customers from the 2016 fiscal year. Retrieve customer contact details (FirstName, LastName, EmailAddress) alongside their corresponding sales order numbers (OrderNumber) for all 2016 transactions.

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






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

```sql
select continent,
Country,
Region,
isnull(count (distinct OrderNumber), 0) as [Total Order]
from AdventureWorks_Territories as AWT
left join AdventureWorks_Sales_2017 as AS2017
on AWT.SalesTerritoryKey=AS2017.TerritoryKey
group by continent,country,region
```

Scenario 17: The category manager wants to audit the 2017 sales performance of a specific bike model/product from the 'Bikes' category (e.g., 'Mountain-200 Black, 42'). Retrieve the product name, model name, and total quantity sold for 2017, ensuring zero sales are displayed as 0 using a left join approach.

```sql
select
ProductName,
ModelName,
isnull(sum(OrderQuantity), 0) as [Total Quantity Sold] 
from  AdventureWorks_Products as AP 
left join AdventureWorks_Sales_2017 as AS2017 
on AS2017.ProductKey=AP.ProductKey 
where ProductName='Mountain-200 Black, 42'
group by productname,ModelName
```

Scenario 18: The customer analytics team wants to evaluate overall customer purchasing activity. Retrieve all customers' first names, last names, annual incomes, and total order quantities in 2017, ensuring that customers with no orders display 0.

```sql
select FirstName,
LastName,
AnnualIncome,
isnull(sum(orderquantity), 0) as [Total Quantity]
from AdventureWorks_Customers as AC
left join AdventureWorks_Sales_2017 as AS2017
on  AC.CustomerKey=AS2017.CustomerKey
group by FirstName,LastName,AnnualIncome
```

Scenario 19: The category manager wants to audit customer purchasing performance for the 'Bikes' category in 2017. Retrieve all customers' first names, last names, and the total quantity of bikes purchased in 2017 (Total Bikes Bought), ensuring customers with zero bike purchases display 0.

```sql
select
firstname,
lastname,
isnull(sum(orderquantity), 0) as [Total Bikes]
from Adventureworks_customers as AC
left join  AdventureWorks_Sales_2017 as AS2017
on AC.customerkey=As2017.customerkey
left join AdventureWorks_Products as AP
ON AS2017.ProductKey = AP.ProductKey
left join AdventureWorks_Product_Subcategories as APS
ON AP.ProductSubcategoryKey = APS.ProductSubcategoryKey
left join AdventureWorks_Product_Categories as APC
ON APS.ProductCategoryKey = APC.ProductCategoryKey AND APC.CategoryName = 'Bikes'
group by FirstName,LastName
```

Scenario 20: The management team requires a comprehensive regional report combining overall customer profiles, geographical territories, and specific purchasing behavior for the 'Category' (Bikes) in 2017.

```sql
select
firstname,
lastname,
continent,
region,
isnull(sum(orderquantity), 0) as [Total Bikes]
from Adventureworks_customers as AC
left join  AdventureWorks_Sales_2017 as AS2017
on AC.customerkey=As2017.customerkey
left join AdventureWorks_Territories as AWT
on AWT.SalesTerritoryKey=AS2017.TerritoryKey
left join AdventureWorks_Products as AP
ON AS2017.ProductKey = AP.ProductKey
left join AdventureWorks_Product_Subcategories as APS
ON AP.ProductSubcategoryKey = APS.ProductSubcategoryKey
left join AdventureWorks_Product_Categories as APC
ON APS.ProductCategoryKey = APC.ProductCategoryKey AND APC.CategoryName = 'Bikes'
group by FirstName,LastName,continent,region
```
Scenario 21: The commercial strategy team wants to audit customer retention and year-over-year trading patterns. Retrieve all unique customers who engaged in transactions during either 2016 (AdventureWorks_Sales_2016) or 2017 (AdventureWorks_Sales_2017), capturing customers exclusive to 2016, exclusive to 2017, and active in both years without omitting any records.

```sql
select
AC.FirstName,
AC.LastName,
AS2016.OrderQuantity as [2016_Qty],
AS2017.OrderQuantity as [2017_Qty]
from AdventureWorks_Sales_2016 as AS2016
full outer join AdventureWorks_Sales_2017 as AS2017
on AS2016.CustomerKey = AS2017.CustomerKey
left join AdventureWorks_Customers as AC
on AC.CustomerKey = coalesce(AS2016.CustomerKey, AS2017.CustomerKey)
```

Scenario 22: The product management team wants to analyze product performance shifts between 2016 and 2017. Retrieve all unique product keys from both AdventureWorks_Sales_2016 and AdventureWorks_Sales_2017 along with their total order quantities for each respective year, ensuring that products sold exclusively in 2016, exclusively in 2017, or in both years are fully displayed.

```sql
with Sales2016 as (
select
ProductKey, 
sum(OrderQuantity) as TotalQty2016
from AdventureWorks_Sales_2016
group by ProductKey
),

Sales2017 as (
select 
ProductKey, 
sum(OrderQuantity) as TotalQty2017
from AdventureWorks_Sales_2017
group by ProductKey
)

select 
AP.ProductName,
isnull(S16.TotalQty2016, 0) as [2016_Qty],
isnull(S17.TotalQty2017, 0) as [2017_Qty]
from Sales2016 as S16
full outer join Sales2017 as S17
on S16.ProductKey = S17.ProductKey
left join AdventureWorks_Products as AP
on AP.ProductKey = coalesce(S16.ProductKey, S17.ProductKey);
```

Scenario 23: The regional management team wants to evaluate sales territory performance shifts between 2016 and 2017. Retrieve all unique territory keys from both AdventureWorks_Sales_2016 and AdventureWorks_Sales_2017 along with their total order quantities for each respective year, ensuring that territories active exclusively in 2016, exclusively in 2017, or in both years are fully displayed.

```sql
with Sales2016 as (

select TerritoryKey,
sum(orderquantity) as TotalQty2016
from AdventureWorks_Sales_2016 as AS2016
group by TerritoryKey
),

Sales2017 as (
select TerritoryKey,
sum(orderquantity) as TotalQty2017
from AdventureWorks_Sales_2017 as AS2017
group by TerritoryKey
)

select region,
isnull(S16.TotalQty2016, 0) as [2016_Qty],
isnull(S17.TotalQty2017, 0) as [2017_Qty]
from Sales2016 as S16
full outer join sales2017 as S17
on S16.TerritoryKey=S17.TerritoryKey
left join AdventureWorks_Territories as AWT
on AWT.SalesTerritoryKey=coalesce(S16.TerritoryKey, S17.territorykey)
```

Scenario 24:  The merchandising executive team wants to audit high-level category performance across consecutive years. Retrieve all product categories along with their total order quantities for both 2016 and 2017 sales cycles, ensuring categories exclusive to 2016, exclusive to 2017, or present in both years are fully captured.

```sql
with Sales2016 as (

select ProductKey,
sum(orderquantity) as TotalQty2016
from AdventureWorks_Sales_2016 as AS2016
group by ProductKey
),

Sales2017 as (
select ProductKey,
sum(orderquantity) as TotalQty2017
from AdventureWorks_Sales_2017 as AS2017
group by ProductKey
)

select categoryname,
isnull(sum(S16.TotalQty2016), 0) as [2016_Qty],
isnull(sum(S17.TotalQty2017), 0) as [2017_Qty]
from Sales2016 as S16
full outer join sales2017 as S17
on S16.ProductKey=S17.ProductKey
left join AdventureWorks_Products as AP
on AP.ProductKey=coalesce(S16.productkey, S17.productkey)
left join AdventureWorks_Product_Subcategories as APS
on APS.ProductSubcategoryKey=AP.ProductSubcategoryKey
left join AdventureWorks_Product_Categories as APC
on APC.ProductCategoryKey=APS.ProductCategoryKey
group by CategoryName
```

Scenario 25:  The inventory and product management team wants a granular breakdown of product subcategories to identify shifting trends between 2016 and 2017. Retrieve all product subcategory names along with their total order quantities for both years, ensuring that subcategories active exclusively in 2016, exclusively in 2017, or in both years are fully captured and properly zero-filled.

```sql
with Sales2016 as (

select ProductKey,
sum(orderquantity) as TotalQty2016
from AdventureWorks_Sales_2016 as AS2016
group by ProductKey
),

Sales2017 as (
select ProductKey,
sum(orderquantity) as TotalQty2017
from AdventureWorks_Sales_2017 as AS2017
group by ProductKey
)

select SubcategoryName,
isnull(sum(S16.TotalQty2016), 0) as [2016_Qty],
isnull(sum(S17.TotalQty2017), 0) as [2017_Qty]
from Sales2016 as S16
full outer join sales2017 as S17
on S16.ProductKey=S17.ProductKey
left join AdventureWorks_Products as AP
on AP.ProductKey=coalesce(S16.productkey, S17.productkey)
left join AdventureWorks_Product_Subcategories as APS
on APS.ProductSubcategoryKey=AP.ProductSubcategoryKey
group by SubcategoryName
```

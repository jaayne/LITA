# LITA

#### Project Description
---

This Project covers analysis done on sales and customers data indicating insights and recommendations about this analysis.Measuring the performance of the products and performance of the retail stores while the customer data analysing the subscription trwends between customers and most preferred subscribtion plan.

#### Data Sources
This primary data was gotten from the incubator hub facilitators 

#### Tools Used
Three tools were used in analysing this project starting with:
- Microsoft excel
- SQL
- Powerbi

#### Data Processes 
After receiving the data , and checking for duplicates then went ahead to tabularize this data to enable easier formatting and a well constructed pivot table this dataset was then imported into sql as csv file and also taken to powerbi for visiualization

#### Data Analysis
###### Sales Data
```Sql
--Checking my dataset.
select *
from [dbo].[Lita sales Data  Csv]

--Retrieving the  total for each product category.
SELECT Product,Sum(Quantity) as Total_Sales
from [dbo].[Lita sales Data  Csv]
GROUP by product;

--Number of sales transactions by region
SELECT distinct Region,COUNT(*) as Number_of_transactions
from [dbo].[Lita sales Data  Csv]
GROUP BY Region;
 
--The highest -selling product by total-sales value(Revenue).
SELECT TOP 1 product,SUM(quantity * UnitPrice) as Total_sales_Value
from [dbo].[Lita sales Data  Csv]
GROUP BY Product
ORDER BY Total_sales_Value Desc;

--Total Revenue per product
SELECT product,SUM(quantity * UnitPrice) as Total_Revenue
from [dbo].[Lita sales Data  Csv]
GROUP BY Product

--Monthly sales total for the current year (2024)
select sum(quantity) as Total_sales,Month(orderdate) as Month_number, DATENAME(month, OrderDate) as Month_Name
from [dbo].[Lita sales Data  Csv]
where year(OrderDate) = year(getdate())
GROUP BY Month(orderdate), DATENAME(month, OrderDate)
ORDER BY month(OrderDate)

--The top 5 customers by total purchased amount (revenue)
SELECT TOP 5 Customer_Id,SUM(quantity * UnitPrice) as Total_Revenue
from [dbo].[Lita sales Data  Csv]
GROUP BY Customer_Id
ORDER BY Total_Revenue Desc;

--The percentage of total sales contributed by each region
SELECT Region,Sum(Quantity) as Total_sales,
	Round((SUM(Quantity)/Cast((select SUM(Quantity) from  [dbo].[Lita sales Data  Csv]) as float )*100),2) as Percentage
from [dbo].[Lita sales Data  Csv]
group by region
order by Percentage desc;

--products with sales in the last quarter.
select distinct product, orderid, Quantity
from dbo.[Lita sales Data  Csv]
where OrderDate between '2024-07-01' AND '2024-09-30'

--products with no sales in the last quarter.
select distinct product, orderid,Quantity
from dbo.[Lita sales Data  Csv]
Where Product Not IN 
	(select distinct Product
	from dbo.[Lita sales Data  Csv]
	where OrderDate between '2024-07-01' AND '2024-09-30'
);
```

###### CUstomerData
```Sql
select *
from dbo.['Lita Customer Data Csv$']

--retrieve the total number of customers from each region.
Select Region, COUNT(DISTINCT(CustomerID))
From dbo.['Lita Customer Data Csv$']
group by region 

--find the most popular subscription type by the number of customers.
SELECT TOP 1 SubscriptionType, COUNT(DISTINCT(CustomerID)) as No_of_customers
from dbo.['Lita Customer Data Csv$']
group by SubscriptionType

--find customers who canceled their subscription within 6 months.
Select canceled, customername
from dbo.['Lita Customer Data Csv$']
where Canceled = 1
and DATEDIFF(month,subscriptionstart,subscriptionend)<=6

--calculate the average subscription duration for all customers.
Select (AVG(cast((Duration)as float))) as Avg_Sub_duration
from dbo.['Lita Customer Data Csv$']

--customers with subscriptions longer than 12 months.
Select customerid, Customername
from dbo.['Lita Customer Data Csv$']
where DATEDIFF(MONTH,subscriptionstart,subscriptionend) >12

--customers with subscriptions longer than 12 months using the pre calculated duration column.
Select customerid, Customername
from dbo.['Lita Customer Data Csv$']
where Duration>365

--The total revenue by subscription type.
SELECT SubscriptionType,  Sum(Revenue) as Total_Revenue
from [dbo].['Lita Customer Data Csv$']
Group by SubscriptionType;

--The top 3 regions by subscription cancellations.
Select Top 3 Region, COUNT(*) as Subscription_cancelled
from dbo.['Lita Customer Data Csv$']
where Canceled = 1
group by region;

--Top Region with No Cancellation
Select Top 3 Region, COUNT(*) as NC
from dbo.['Lita Customer Data Csv$']
where Canceled = 0
group by region

--Indicating the active and cancelled subscription
SELECT 
    CASE 
        WHEN canceled = 1 THEN 'Canceled'
        ELSE 'Active'
    END AS subscription_status
from dbo.['Lita Customer Data Csv$']



--The total number of active and canceled subscriptions
select
	sum(case when canceled = 1 Then 1 else 0 End) as Canceled_SUb, 
	sum(case when canceled = 0 then 1 else 0 End) as Active_Sub
from [dbo].['Lita Customer Data Csv$']
```

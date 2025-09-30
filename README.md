# Retail_Sales_Analysis sql query
use projectOne;
go
SELECT * FROM dbo.Retail_Sales;

--Total Quantity sold and Total revenue for each category.
SELECT 
    Product_Category, 
    SUM(CAST(Total_Amount AS FLOAT)) AS Total_Sales, SUM(CAST(Quantity AS INT)) AS Total_Quantity_Sold
FROM dbo.Retail_Sales
GROUP BY Product_Category
ORDER BY Total_Sales DESC;
--The most sold category was Clothing but Electronics generated more revenue.


--Total Sales per Month
SELECT  
    DATENAME(MONTH, Date) AS MonthName,
    SUM(CAST(Total_Amount AS FLOAT)) AS Total_Sales
FROM dbo.Retail_Sales
GROUP BY DATENAME(MONTH, Date), MONTH(Date)
ORDER BY MONTH(Date);


--Total per age group
SELECT 
Case
when Age Between 18 AND 27 Then '18-27'
when Age Between 28 AND 37 Then '28-37'
when Age Between 38 AND 47 Then '38-47'
when Age Between 48 AND 57 Then '48-57'
when Age Between 58 AND 67 Then '58-67'
when Age> 67 Then '67+'
else 'Unknown'
end as  Age_Group,
SUM(CAST(Total_Amount AS FLOAT)) AS Total_Sales
FROM dbo.Retail_Sales
GROUP BY 
Case
when Age Between 18 AND 27 Then '18-27'
when Age Between 28 AND 37 Then '28-37'
when Age Between 38 AND 47 Then '38-47'
when Age Between 48 AND 57 Then '48-57'
when Age Between 58 AND 67 Then '58-67'
when Age> 67 Then '67+'
else 'Unknown'
end
ORDER BY Total_Sales DESC;
--Age group between 18 and 27 spends the most whereas 58 and 67 group spends less


--Total per age group per category
SELECT 
Case
when Age Between 18 AND 27 Then '18-27'
when Age Between 28 AND 37 Then '28-37'
when Age Between 38 AND 47 Then '38-47'
when Age Between 48 AND 57 Then '48-57'
when Age Between 58 AND 67 Then '58-67'
when Age> 67 Then '67+'
else 'Unknown'
end as  Age_Group, Product_Category,
SUM(CAST(Total_Amount AS FLOAT)) AS Total_Sales
FROM dbo.Retail_Sales
GROUP BY 
Case
when Age Between 18 AND 27 Then '18-27'
when Age Between 28 AND 37 Then '28-37'
when Age Between 38 AND 47 Then '38-47'
when Age Between 48 AND 57 Then '48-57'
when Age Between 58 AND 67 Then '58-67'
when Age> 67 Then '67+'
else 'Unknown'
end, Product_Category;

--further pivoting
SELECT Age_Group,
       ISNULL([Beauty], 0) AS Beauty_Sales,
       ISNULL([Clothing], 0) AS Clothing_Sales,
       ISNULL([Electronics], 0) AS Electronics_Sales
FROM (
    SELECT
        CASE 
            WHEN Age BETWEEN 18 AND 27 THEN '18-27'
            WHEN Age BETWEEN 28 AND 37 THEN '28-37'
            WHEN Age BETWEEN 38 AND 47 THEN '38-47'
            WHEN Age BETWEEN 48 AND 57 THEN '48-57'
            WHEN Age BETWEEN 58 AND 67 THEN '58-67'
            ELSE '68+'
        END AS Age_Group,
        Product_Category,
        SUM(CAST(Total_Amount AS FLOAT)) AS Total_Sales
    FROM dbo.Retail_Sales
    GROUP BY 
        CASE 
            WHEN Age BETWEEN 18 AND 27 THEN '18-27'
            WHEN Age BETWEEN 28 AND 37 THEN '28-37'
            WHEN Age BETWEEN 38 AND 47 THEN '38-47'
            WHEN Age BETWEEN 48 AND 57 THEN '48-57'
            WHEN Age BETWEEN 58 AND 67 THEN '58-67'
            ELSE '68+'
        END,
        Product_Category
) AS SourceTable
PIVOT (
    SUM(Total_Sales)
    FOR Product_Category IN ([Beauty], [Clothing], [Electronics])
) AS PivotTable
ORDER BY Age_Group;


--Total spend by each gender per each category
SELECT Gender, Product_Category, SUM(CAST(Total_Amount AS FLoat)) as Total_Sales 
From dbo.Retail_Sales
Group by Gender, Product_Category
Order by Total_Sales DESC;
--pivoting
SELECT Gender, ISNULL([Beauty], 0) AS Beauty_Sales,
       ISNULL([Clothing], 0) AS Clothing_Sales,
       ISNULL([Electronics], 0) AS Electronics_Sales
FROM (
SELECT Gender, Product_Category, SUM(CAST(Total_Amount AS FLoat)) as Total_Sales 
from dbo.Retail_Sales 
GROUP BY Gender, Product_Category
) AS SourceTable
PIVOT (
    SUM(Total_Sales)
    FOR Product_Category IN ([Beauty], [Clothing], [Electronics])
) AS PivotTable
ORDER BY Gender;









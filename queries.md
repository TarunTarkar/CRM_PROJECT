# CRM System Queries

## Queries for Customer Management

Retrieve all customers' names and email addresses:

```sql
SELECT First_Name, Last_Name, Email
FROM Customers;
```

Find customers who have not placed any orders yet.

```sql
SELECT c.*
FROM Customers c
LEFT JOIN Orders o ON c.Customer_ID = o.Customer_ID
WHERE o.Customer_ID IS NULL;
```

Calculate the total number of customers in the database.

```sql
SELECT COUNT(Customer_ID) AS Total_Customers
FROM Customers;
```

Identify the customer(s) with the highest total order amount.

```sql
SELECT c.Customer_ID, c.First_Name, c.Last_Name, c.Email, SUM(o.Total_Amount) AS Total_Order_Amount
FROM Customers c
JOIN Orders o ON c.Customer_ID = o.Customer_ID
GROUP BY c.Customer_ID, c.First_Name, c.Last_Name, c.Email
ORDER BY Total_Order_Amount DESC
LIMIT 1;
```
## Queries for Lead Tracking:

List all leads along with their sources and statuses.
```sql
SELECT *
FROM Leads;
```
Find leads with a specific source (e.g., website, referral).

```sql
SELECT *
FROM Leads
WHERE Source = 'Website';
```

Count the number of leads assigned to each sales representative.
```sql
SELECT sr.First_Name, sr.Last_Name, COUNT(l.Lead_ID) AS Total_Leads
FROM SalesRepresentatives sr
LEFT JOIN Leads l ON sr.First_Name = l.First_Name AND sr.Last_Name = l.Last_Name
GROUP BY sr.First_Name, sr.Last_Name;
```

Determine the conversion rate from leads to customers.

```sql
SELECT COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email;
```

## Queries for Sales Representatives:

Display the names and email addresses of all sales representatives.
```sql
SELECT First_Name, Last_Name, Email
FROM SalesRepresentatives;
```

Find sales representatives in a specific department (e.g., Sales, Marketing).
```sql
SELECT *
FROM SalesRepresentatives
WHERE Department = 'Sales';
```
Calculate the total number of interactions logged by each sales representative.
```sql
SELECT sr.First_Name, sr.Last_Name, 
       COUNT(DISTINCT c.Customer_ID) AS Estimated_Interactions
FROM SalesRepresentatives sr
LEFT JOIN Customers c ON sr.SalesRep_ID = c.Assigned_SalesRep_ID
GROUP BY sr.First_Name, sr.Last_Name;
```

Identify the sales representative with the highest number of completed tasks.
```sql
SELECT sr.First_Name, sr.Last_Name, COUNT(DISTINCT o.Order_ID) AS Total_Completed_Tasks
FROM SalesRepresentatives sr
LEFT JOIN Customers c ON sr.SalesRep_ID = c.Assigned_SalesRep_ID
LEFT JOIN Orders o ON c.Customer_ID = o.Customer_ID
LEFT JOIN Order_Items oi ON o.Order_ID = oi.Order_ID
LEFT JOIN Products p ON oi.Product_ID = p.Product_ID
WHERE p.Product_Name IS NOT NULL
  AND o.Order_Date BETWEEN '2023-01-01' AND '2023-12-31'
GROUP BY sr.First_Name, sr.Last_Name
ORDER BY Total_Completed_Tasks DESC
LIMIT 1;
```

## Queries for Product Management:

List all products along with their prices and quantities in stock.
```sql
SELECT *
FROM Products;
```

Find products with low stock levels (e.g., less than 10 units).
```sql
SELECT *
FROM Products
WHERE Quantity_in_Stock < 10;
```

Calculate the total revenue generated from product sales.
```sql
SELECT SUM(o.Total_Amount) AS Total_Revenue
FROM Orders o;
```

Identify the most expensive product in the inventory.
```sql
SELECT *
FROM Products
ORDER BY Price DESC
LIMIT 1;
```
## Queries for Order Processing:

Retrieve all orders placed within a specific date range.
```sql
SELECT *
FROM Orders
WHERE Order_Date BETWEEN '2024-04-01' AND '2024-04-30';
```

Calculate the average order amount.
```sql
SELECT AVG(Total_Amount) AS Average_Order_Amount
FROM Orders;
```

Find orders with a total amount exceeding a certain threshold.
```sql
SELECT *
FROM Orders
WHERE Total_Amount > 100;
```

Determine the most frequently purchased product.
```sql
SELECT p.Product_ID, p.Product_Name, COUNT(oi.Order_Item_ID) AS Total_Orders
FROM Products p
JOIN Order_Items oi ON p.Product_ID = oi.Product_ID
GROUP BY p.Product_ID, p.Product_Name
ORDER BY Total_Orders DESC
LIMIT 1;
```

## Queries for Interactions and Tasks:
List all interactions logged by a specific sales representative.
```sql
SELECT *
FROM Interactions
WHERE SalesRep_ID = 101;
```

Find tasks assigned to a particular sales representative due before a certain date.
```sql
SELECT *
FROM Tasks
WHERE Assigned_To = 101
AND Due_Date < '2024-04-10';
```

Calculate the average time taken to complete tasks.
```sql
SELECT AVG(DATEDIFF(Due_Date, CASE WHEN Status = 'Completed' THEN CURDATE() END)) AS Average_Task_Completion_Time
FROM Tasks
WHERE Status = 'Completed';
```

Identify the most common interaction type (e.g., call, email) among leads.
```sql
SELECT Interaction_Type, COUNT(Interaction_ID) AS Total_Interactions
FROM Interactions
GROUP BY Interaction_Type
ORDER BY Total_Interactions DESC
LIMIT 1;
```

## Analytical Queries:

Analyze the trend of customer acquisition over time.
```sql
SELECT YEAR(Order_Date) AS Year, MONTH(Order_Date) AS Month, COUNT(DISTINCT Customer_ID) AS New_Customers
FROM Orders
GROUP BY YEAR(Order_Date), MONTH(Order_Date)
ORDER BY Year, Month;
```

Calculate the conversion rate from leads to customers for each sales representative.
```sql
SELECT sr.SalesRep_ID, sr.First_Name, sr.Last_Name, COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM SalesRepresentatives sr
LEFT JOIN Interactions i ON sr.SalesRep_ID = i.SalesRep_ID
LEFT JOIN Leads l ON i.Lead_ID = l.Lead_ID
LEFT JOIN Customers c ON l.Email = c.Email
GROUP BY sr.SalesRep_ID, sr.First_Name, sr.Last_Name;
```

Identify the most successful lead source in terms of conversion rate.
```sql
SELECT Source, COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email
GROUP BY Source
ORDER BY Conversion_Rate DESC
LIMIT 1;
```
Determine the busiest day of the week for interactions and tasks.
```sql
SELECT DAYNAME(Interaction_Date) AS Busiest_Day, COUNT(Interaction_ID) AS Total_Interactions
FROM Interactions
GROUP BY DAYNAME(Interaction_Date)
ORDER BY Total_Interactions DESC
LIMIT 1;
```
## Real-Life Situations Questions:

How many new customers did we acquire last month?
```sql
ALTER TABLE Customers
ADD COLUMN Customer_Join_Date DATE;
```
```sql
UPDATE Customers
SET Customer_Join_Date = CASE Customer_ID
                            WHEN 1 THEN '2024-01-15'  -- John Doe's join date
                            WHEN 2 THEN '2024-02-20'  -- Alice Smith's join date
                            WHEN 3 THEN '2024-03-10'  -- Bob Johnson's join date
                            WHEN 4 THEN '2024-04-05'  -- Mary Johnson's join date
                            WHEN 5 THEN '2024-05-12'  -- James Smith's join date
                            WHEN 6 THEN '2024-06-25'  -- Emma Brown's join date
                            ELSE NULL  -- Handle other cases if needed
                        END
WHERE Customer_ID IN (1, 2, 3, 4, 5, 6);
```

```sql
SELECT COUNT(DISTINCT Customer_ID) AS New_Customers_Last_Month
FROM Customers
WHERE MONTH(CURDATE()) - MONTH(Customer_Join_Date) = 1;
```

What is the average response time for following up with leads?
```sql
ALTER TABLE Leads
ADD COLUMN Lead_Creation_Date DATE;
```

```sql
UPDATE Leads
SET Lead_Creation_Date = CASE Lead_ID
                            WHEN 1 THEN '2024-01-01'  -- Lead_ID 1 creation date
                            WHEN 2 THEN '2024-02-15'  -- Lead_ID 2 creation date
                            -- Add more cases for other lead IDs as needed
                            ELSE NULL  -- Handle other cases if needed
                        END;
```
```sql
SELECT AVG(DATEDIFF(Interaction_Date, Lead_Creation_Date)) AS Average_Response_Time
FROM Interactions i
JOIN Leads l ON i.Lead_ID = l.Lead_ID;
```

Which sales representative has the highest customer retention rate?
```sql
SELECT sr.First_Name, sr.Last_Name, COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Customer_Retention_Rate
FROM SalesRepresentatives sr
LEFT JOIN Interactions i ON sr.SalesRep_ID = i.SalesRep_ID
LEFT JOIN Leads l ON i.Lead_ID = l.Lead_ID
LEFT JOIN Customers c ON l.Email = c.Email
GROUP BY sr.First_Name, sr.Last_Name
ORDER BY Customer_Retention_Rate DESC
LIMIT 1;
``` 
What are the top-selling products this quarter?
```sql
SELECT p.Product_Name, SUM(oi.Quantity) AS Total_Sales
FROM Products p
JOIN Order_Items oi ON p.Product_ID = oi.Product_ID
JOIN Orders o ON oi.Order_ID = o.Order_ID
WHERE YEAR(o.Order_Date) = YEAR(CURDATE()) AND QUARTER(o.Order_Date) = QUARTER(CURDATE())
GROUP BY p.Product_Name
ORDER BY Total_Sales DESC
LIMIT 5;
```

How many interactions were logged for leads coming from referrals?
```sql
SELECT COUNT(Interaction_ID) AS Referral_Interactions
FROM Interactions
WHERE Lead_ID IN (SELECT Lead_ID FROM Leads WHERE Source = 'Referral');
```

What percentage of leads have been converted into customers so far?
```sql
SELECT COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email;
```

How many tasks are overdue, and what is their total priority level?
```sql
SELECT COUNT(Task_ID) AS Overdue_Tasks, SUM(CASE WHEN Status = 'High' THEN 3 WHEN Status = 'Medium' THEN 2 ELSE 1 END) AS Total_Priority_Level
FROM Tasks
WHERE Due_Date < CURDATE() AND Status != 'Completed';
```

These queries cover various scenarios and analysis that you might encounter in managing a CRM 
database system for a sales team. You can run these queries in your MySQL Workbench environment 
to retrieve the required information. Adjust the conditions and parameters according to your 
specific business requirements.

## Views
### View to Retrieve Customer Details with Assigned Sales Representative:
#### This view joins the Customers table with SalesRepresentatives to retrieve customer information along with their assigned sales representative.
```sql
CREATE VIEW CustomerSalesRepView AS
SELECT c.Customer_ID, c.First_Name, c.Last_Name, c.Email, c.Phone_Number, c.Address,
       sr.First_Name AS SalesRep_FirstName, sr.Last_Name AS SalesRep_LastName, sr.Email AS SalesRep_Email
FROM Customers c
LEFT JOIN SalesRepresentatives sr ON c.Assigned_SalesRep_ID = sr.SalesRep_ID;
```

### View to Show Interactions with Lead Details:
#### This view combines Interactions with Leads to display interaction details along with lead information.
```sql
CREATE VIEW InteractionLeadView AS
SELECT i.Interaction_ID, i.Interaction_Date, i.Interaction_Type, i.Outcome, i.Notes,
       l.First_Name AS Lead_FirstName, l.Last_Name AS Lead_LastName, l.Email AS Lead_Email
FROM Interactions i
LEFT JOIN Leads l ON i.Lead_ID = l.Lead_ID;
```

## Functions
### Function to Calculate Average Response Time for Lead Follow-ups:
#### This function calculates the average response time for following up with leads based on interaction and lead creation dates.
```sql
--Change the delimiter to //
DELIMITER //
CREATE FUNCTION AvgLeadResponseTime()
RETURNS DECIMAL(10, 2)
DETERMINISTIC
READS SQL DATA
BEGIN
    DECLARE avg_response DECIMAL(10, 2);
    
    SELECT AVG(DATEDIFF(i.Interaction_Date, l.Lead_Creation_Date))
    INTO avg_response
    FROM Interactions i
    JOIN Leads l ON i.Lead_ID = l.Lead_ID;
    
    RETURN avg_response;
END //
-- Reset the delimiter to ;
DELIMITER ;
```

## Stored Procedures
### Stored Procedure to Update Lead Creation Dates:
#### This procedure updates the Lead_Creation_Date column in the Leads table based on lead ID.
```sql
DELIMITER //
CREATE PROCEDURE UpdateLeadCreationDates()
BEGIN
    UPDATE Leads
    SET Lead_Creation_Date = CASE Lead_ID
                                WHEN 1 THEN '2024-01-01'
                                WHEN 2 THEN '2024-02-15'
                                -- Add more cases for other lead IDs as needed
                                ELSE NULL
                            END;
END //
DELIMITER ;
```

### Stored Procedure to Retrieve Top Selling Products:
#### This procedure returns the top selling products of the quarter.
```sql
DELIMITER //
CREATE PROCEDURE TopSellingProducts()
BEGIN
    SELECT p.Product_Name, SUM(oi.Quantity) AS Total_Sales
    FROM Products p
    JOIN Order_Items oi ON p.Product_ID = oi.Product_ID
    JOIN Orders o ON oi.Order_ID = o.Order_ID
    WHERE YEAR(o.Order_Date) = YEAR(CURDATE()) AND QUARTER(o.Order_Date) = QUARTER(CURDATE())
    GROUP BY p.Product_Name
    ORDER BY Total_Sales DESC
    LIMIT 5;
END //
DELIMITER ;
```
## Usage Examples
### Use Views:
SELECT * FROM CustomerSalesRepView;
SELECT * FROM InteractionLeadView;
```

### Call Functions:
```sql
SELECT AvgLeadResponseTime();
```

### Execute Stored Procedures:
```sql
CALL UpdateLeadCreationDates();
CALL TopSellingProducts();
```
These examples demonstrate how to create views, functions, and stored procedures tailored to your CRM database system. You can further customize and expand these database objects based on your specific requirements and business logic. Use them to streamline data retrieval, analysis, and maintenance tasks within your CRM application. Adjust the SQL queries and procedures as needed to align with your project's data modeling and operational needs.


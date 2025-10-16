# Usage Guide for Customer Relationship Management System
This guide provides instructions on setting up and using the Customer Relationship Management (CRM) database system using MySQL. It includes schema definition, data insertion, sample queries, views, functions, and stored procedures.

## Schema Setup
The schema defines the structure of the CRM database, including tables and relationships.

### Creating Schema and Tables
```sql
  -- Create Schema
CREATE SCHEMA IF NOT EXISTS `CustomerRelationshipManagementSystem` DEFAULT CHARACTER SET utf8 ;
USE `CustomerRelationshipManagementSystem`;

-- Table: Customers
CREATE TABLE IF NOT EXISTS `Customers` (
  `Customer_ID` INT NOT NULL AUTO_INCREMENT,
  `First_Name` VARCHAR(45) NULL,
  `Last_Name` VARCHAR(45) NULL,
  `Email` VARCHAR(45) NULL,
  `Phone_Number` VARCHAR(15) NULL,
  `Address` VARCHAR(255) NULL,
  PRIMARY KEY (`Customer_ID`)
) ENGINE = InnoDB;

-- Table: Leads
CREATE TABLE IF NOT EXISTS `Leads` (
  `Lead_ID` INT NOT NULL AUTO_INCREMENT,
  `First_Name` VARCHAR(45) NULL,
  `Last_Name` VARCHAR(45) NULL,
  `Email` VARCHAR(45) NULL,
  `Phone_Number` VARCHAR(15) NULL,
  `Address` VARCHAR(255) NULL,
  `Source` VARCHAR(100) NULL,
  `Status` VARCHAR(50) NULL,
  `Notes` MEDIUMTEXT NULL,
  PRIMARY KEY (`Lead_ID`)
) ENGINE = InnoDB;


-- Table: SalesRepresentatives
CREATE TABLE IF NOT EXISTS `SalesRepresentatives` (
  `SalesRepresentatives_ID` INT NOT NULL AUTO_INCREMENT,
  `First_Name` VARCHAR(45) NULL,
  `Last_Name` VARCHAR(45) NULL,
  `Email` VARCHAR(100) NULL,
  `Phone_Number` VARCHAR(15) NULL,
  `Department` VARCHAR(100) NULL,
  PRIMARY KEY (`SalesRepresentatives_ID`)
) ENGINE = InnoDB;

-- Table: Products
CREATE TABLE IF NOT EXISTS `Products` (
  `Product_ID` INT NOT NULL AUTO_INCREMENT,
  `Product_Name` VARCHAR(45) NULL,
  `Description` MEDIUMTEXT NULL,
  `Price` DECIMAL(10,2) NULL,
  `Quantity_in_Stock` INT NULL,
  PRIMARY KEY (`Product_ID`)
) ENGINE = InnoDB;

-- Table: Orders
CREATE TABLE IF NOT EXISTS `Orders` (
  `Order_ID` INT NOT NULL AUTO_INCREMENT,
  `Customer_ID` INT NULL,
  `Order_Date` DATE NULL,
  `Total_Amount` DECIMAL(10,2) NULL,
  PRIMARY KEY (`Order_ID`),
  CONSTRAINT `FK_Customer_ID`
    FOREIGN KEY (`Customer_ID`)
    REFERENCES `Customers` (`Customer_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION
) ENGINE = InnoDB;

-- Table: Order_Items
CREATE TABLE IF NOT EXISTS `Order_Items` (
  `Order_Items_ID` INT NOT NULL AUTO_INCREMENT,
  `Order_ID` INT NULL,
  `Product_ID` INT NULL,
  `Quantity` INT NULL,
  `Unit_Price` DECIMAL(10,2) NULL,
  PRIMARY KEY (`Order_Items_ID`),
  CONSTRAINT `FK_Order_ID`
    FOREIGN KEY (`Order_ID`)
    REFERENCES `Orders` (`Order_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_Product_ID`
    FOREIGN KEY (`Product_ID`)
    REFERENCES `Products` (`Product_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION
) ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `Interactions` (
  `Interaction_ID` INT NOT NULL AUTO_INCREMENT,
  `Lead_ID` INT NULL,
  `SalesRep_ID` INT NULL,
  `Interaction_Date` DATE NULL,
  `Interaction_Type` VARCHAR(50) NULL,
  `Outcome` VARCHAR(100) NULL,
  `Notes` TEXT NULL,
  PRIMARY KEY (`Interaction_ID`),
  CONSTRAINT `FK_Lead_ID`
    FOREIGN KEY (`Lead_ID`)
    REFERENCES `Leads` (`Lead_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `FK_SalesRep_ID`
    FOREIGN KEY (`SalesRep_ID`)
    REFERENCES `SalesRepresentatives` (`SalesRep_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION
) ENGINE = InnoDB;

CREATE TABLE IF NOT EXISTS `Tasks` (
  `Task_ID` INT NOT NULL AUTO_INCREMENT,
  `Description` TEXT NULL,
  `Due_Date` DATE NULL,
  `Status` VARCHAR(50) NULL,
  `Priority` VARCHAR(50) NULL,
  `Assigned_To` INT NULL,
  PRIMARY KEY (`Task_ID`),
  CONSTRAINT `FK_Assigned_To`
    FOREIGN KEY (`Assigned_To`)
    REFERENCES `SalesRepresentatives` (`SalesRep_ID`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION
) ENGINE = InnoDB;
```
## Data Insertion
Populate the tables with sample data to start using the CRM system.

### Inserting Data into Tables
```sql
-- Insert data into Customers table
INSERT INTO Customers (Customer_ID, First_Name, Last_Name, Email, Phone_Number, Address)
VALUES
    (1, 'John', 'Doe', 'john@example.com', '123-456-7890', '123 Main St, City'),
    (2, 'Alice', 'Smith', 'alice@example.com', '456-789-0123', '456 Elm St, Town'),
    (3, 'Bob', 'Johnson', 'bob@example.com', '789-012-3456', '789 Oak St, Village'),
    (4, 'Mary', 'Johnson', 'mary@example.com', '234-567-8901', '234 Oak St, Town'),
    (5, 'James', 'Smith', 'james@example.com', '567-890-1234', '567 Maple St, City'),
    (6, 'Emma', 'Brown', 'emma@example.com', '890-123-4567', '890 Pine St, Village');

ALTER TABLE Customers
ADD COLUMN Customer_Join_Date DATE;

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

-- Insert data into Leads table
INSERT INTO Leads (Lead_ID, First_Name, Last_Name, Email, Phone_Number, Address, Source, Status, Notes)
VALUES
    (1, 'Sarah', 'Brown', 'sarah@example.com', '987-654-3210', '987 Pine St, County', 'Website', 'New', 'Interested in product X'),
    (2, 'Michael', 'Lee', 'michael@example.com', '654-321-0987', '654 Oak St, City', 'Referral', 'Contacted', 'Scheduled demo for product Y'),
    (3, 'Emily', 'Davis', 'emily@example.com', '321-098-7654', '321 Maple St, Town', 'Advertisement', 'Converted', 'Purchased product Z, ongoing support needed'),
    (4, 'Mark', 'Wilson', 'mark@example.com', '765-432-1098', '765 Elm St, County', 'Advertisement', 'New', 'Interested in product Y'),
    (5, 'Olivia', 'Martinez', 'olivia@example.com', '543-210-9876', '543 Cedar St, Village', 'Website', 'Contacted', 'Requested pricing for product Z'),
    (6, 'Ethan', 'Anderson', 'ethan@example.com', '321-654-0987', '321 Birch St, City', 'Referral', 'Converted', 'Purchased product X, happy with service');

ALTER TABLE Leads
ADD COLUMN Lead_Creation_Date DATE;

UPDATE Leads
SET Lead_Creation_Date = CASE Lead_ID
                            WHEN 1 THEN '2024-01-01'  -- Lead_ID 1 creation date
                            WHEN 2 THEN '2024-02-15'  -- Lead_ID 2 creation date
                            -- Add more cases for other lead IDs as needed
                            ELSE NULL  -- Handle other cases if needed
                        END;

-- Insert data into SalesRepresentatives table
ALTER TABLE salesrepresentatives
RENAME COLUMN SalesRepresentatives_ID TO SalesRep_ID;

INSERT INTO SalesRepresentatives (SalesRep_ID, First_Name, Last_Name, Email, Phone_Number, Department)
VALUES
    (101, 'David', 'Miller', 'david@example.com', '111-222-3333', 'Sales'),
    (102, 'Jennifer', 'Wilson', 'jennifer@example.com', '444-555-6666', 'Marketing');

-- Insert data into Products table
INSERT INTO Products (Product_ID, Product_Name, Description, Price, Quantity_in_Stock)
VALUES
    (201, 'Product X', 'High-quality widget for various uses', 50.00, 100),
    (202, 'Product Y', 'Cutting-edge gadget with advanced features', 100.00, 75),
    (203, 'Product Z', 'Essential tool for everyday tasks', 30.00, 150);

-- Insert data into Orders table
INSERT INTO Orders (Order_ID, Customer_ID, Order_Date, Total_Amount)
VALUES
    (301, 1, '2024-04-01', 150.00),
    (302, 2, '2024-04-02', 100.00),
    (303, 3, '2024-04-03', 120.00),
    (304, 4, '2024-04-04', 75.00);

-- Insert data into Order_Items table
ALTER TABLE order_items
RENAME COLUMN Order_Items_ID TO Order_item_ID;

INSERT INTO Order_Items (Order_Item_ID, Order_ID, Product_ID, Quantity, Unit_Price)
VALUES
    (401, 301, 201, 2, 50.00),
    (402, 302, 202, 1, 100.00),
    (403, 303, 202, 1, 100.00),
    (404, 303, 203, 2, 10.00),
    (405, 304, 201, 1, 25.00);

-- Insert data into Interactions table
INSERT INTO Interactions (Interaction_ID, Lead_ID, SalesRep_ID, Interaction_Date, Interaction_Type, Outcome, Notes)
VALUES
    (501, 1, 101, '2024-04-03', 'Call', 'Scheduled meeting', 'Discussed product features and benefits'),
    (502, 2, 102, '2024-04-01', 'Email', 'Follow-up', 'Sent product brochure and pricing details'),
    (503, 3, 101, '2024-03-30', 'Meeting', 'Closed sale', 'Signed contract for product Z'),
    (504, 4, 101, '2024-04-02', 'Call', 'Follow-up', 'Discussed product features and benefits'),
    (505, 5, 102, '2024-04-01', 'Meeting', 'Sent quotation', 'Discussed pricing and customization options'),
    (506, 6, 101, '2024-03-30', 'Email', 'Closed sale', 'Sent contract for product X');

-- Insert data into Tasks table
INSERT INTO Tasks (Task_ID, Description, Due_Date, Status, Priority, Assigned_To)
VALUES
    (601, 'Follow up with lead #1', '2024-04-05', 'Pending', 'High', 101),
    (602, 'Send proposal to lead #2', '2024-04-04', 'In progress', 'Medium', 102),
    (603, 'Prepare demo for customer meeting', '2024-04-06', 'Pending', 'High', 101),
    (604, 'Send follow-up email to lead #4', '2024-04-05', 'Pending', 'High', 101),
    (605, 'Schedule product demo for lead #5', '2024-04-06', 'Pending', 'Medium', 102),
    (606, 'Prepare contract for lead #6', '2024-04-07', 'In progress', 'High', 101);

INSERT INTO Interactions (Lead_ID, SalesRep_ID, Interaction_Date, Interaction_Type, Outcome, Notes)
VALUES
    (1, 101, '2024-04-03', 'Call', 'Scheduled meeting', 'Discussed product features and benefits'),
    (2, 102, '2024-04-01', 'Email', 'Follow-up', 'Sent product brochure and pricing details'),
    (3, 101, '2024-03-30', 'Meeting', 'Closed sale', 'Signed contract for product Z'),
    (4, 101, '2024-04-02', 'Call', 'Follow-up', 'Discussed product features and benefits'),
    (5, 102, '2024-04-01', 'Meeting', 'Sent quotation', 'Discussed pricing and customization options'),
    (6, 101, '2024-03-30', 'Email', 'Closed sale', 'Sent contract for product X');

INSERT INTO Tasks (Description, Due_Date, Status, Priority, Assigned_To)
VALUES
    ('Follow up with lead #1', '2024-04-05', 'Pending', 'High', 101),
    ('Send proposal to lead #2', '2024-04-04', 'In progress', 'Medium', 102),
    ('Prepare demo for customer meeting', '2024-04-06', 'Pending', 'High', 101),
    ('Send follow-up email to lead #4', '2024-04-05', 'Pending', 'High', 101),
    ('Schedule product demo for lead #5', '2024-04-06', 'Pending', 'Medium', 102),
    ('Prepare contract for lead #6', '2024-04-07', 'In progress', 'High', 101);
```
## Basic Queries
Execute basic SQL queries to retrieve information from the CRM database.

### Sample Queries
Retrieve all customers' names and email addresses.

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
## Advanced Queries
Perform more complex SQL queries for analytics and reporting.

### Advanced Analytics

Identify the customer(s) with the highest total order amount.
```sql
SELECT c.Customer_ID, c.First_Name, c.Last_Name, c.Email, SUM(o.Total_Amount) AS Total_Order_Amount
FROM Customers c
JOIN Orders o ON c.Customer_ID = o.Customer_ID
GROUP BY c.Customer_ID, c.First_Name, c.Last_Name, c.Email
ORDER BY Total_Order_Amount DESC
LIMIT 1;
```

Count the number of leads assigned to each sales representative.
```sql
SELECT sr.First_Name, sr.Last_Name, COUNT(l.Lead_ID) AS Total_Leads
FROM SalesRepresentatives sr
LEFT JOIN Leads l ON sr.First_Name = l.First_Name AND sr.Last_Name = l.Last_Name
GROUP BY sr.First_Name, sr.Last_Name;
```

Calculate the conversion rate from leads to customers.
```sql
SELECT COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email;
```
## Views
Create and utilize views to simplify data retrieval tasks.

### Example Views
View to Retrieve Customer Details with Assigned Sales Representative:
```sql
CREATE VIEW CustomerSalesRepView AS
SELECT c.Customer_ID, c.First_Name, c.Last_Name, c.Email, c.Phone_Number, c.Address,
       sr.First_Name AS SalesRep_FirstName, sr.Last_Name AS SalesRep_LastName, sr.Email AS SalesRep_Email
FROM Customers c
LEFT JOIN SalesRepresentatives sr ON c.Assigned_SalesRep_ID = sr.SalesRep_ID;
```

View to Show Interactions with Lead Details:
```sql
CREATE VIEW InteractionLeadView AS
SELECT i.Interaction_ID, i.Interaction_Date, i.Interaction_Type, i.Outcome, i.Notes,
       l.First_Name AS Lead_FirstName, l.Last_Name AS Lead_LastName, l.Email AS Lead_Email
FROM Interactions i
LEFT JOIN Leads l ON i.Lead_ID = l.Lead_ID;
```
## Functions and Stored Procedures
Define and use functions and stored procedures for automation and complex operations.

### Functions
Function to Calculate Average Response Time for Lead Follow-ups:
```sql
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
DELIMITER ;
```

## Stored Procedures
 
Stored Procedure to Update Lead Creation Dates:
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

Stored Procedure to Retrieve Top Selling Products:
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
## Real-Life Scenarios
Apply SQL queries and operations to real-life business scenarios.

### Business Use Cases

#### Customer Acquisition and Analysis

- Analyze the trend of customer acquisition over time.
```sql
SELECT YEAR(Order_Date) AS Year, MONTH(Order_Date) AS Month, COUNT(DISTINCT Customer_ID) AS New_Customers
FROM Orders
GROUP BY YEAR(Order_Date), MONTH(Order_Date)
ORDER BY Year, Month;
```

- Determine how many new customers were acquired last month.
```sql
SELECT COUNT(DISTINCT Customer_ID) AS New_Customers_Last_Month
FROM Customers
WHERE MONTH(CURDATE()) - MONTH(Customer_Join_Date) = 1;
```
#### Lead Management and Conversion

- Calculate the conversion rate from leads to customers.
```sql
SELECT COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email;
```

- Identify the most successful lead source in terms of conversion rate.
```sql
SELECT Source, COUNT(DISTINCT c.Customer_ID) * 100.0 / COUNT(DISTINCT l.Lead_ID) AS Conversion_Rate
FROM Leads l
LEFT JOIN Customers c ON l.Email = c.Email
GROUP BY Source
ORDER BY Conversion_Rate DESC
LIMIT 1;
```

#### Sales Performance Analysis

- Determine the top-selling products this quarter.
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

- Identify the sales representative with the highest number of completed tasks.
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

#### Task Management and Analysis

- Determine the number of tasks that are overdue and their total priority level.
```sql
SELECT COUNT(Task_ID) AS Overdue_Tasks, SUM(CASE WHEN Status = 'High' THEN 3 WHEN Status = 'Medium' THEN 2 ELSE 1 END) AS Total_Priority_Level
FROM Tasks
WHERE Due_Date < CURDATE() AND Status != 'Completed';
```

- Calculate the average time taken to complete tasks.
```sql
SELECT AVG(DATEDIFF(Due_Date, CASE WHEN Status = 'Completed' THEN CURDATE() END)) AS Average_Task_Completion_Time
FROM Tasks
WHERE Status = 'Completed';
```

#### Interactions and Engagement Analysis

_ Determine the most common interaction type among leads.
```sql
SELECT Interaction_Type, COUNT(Interaction_ID) AS Total_Interactions
FROM Interactions
GROUP BY Interaction_Type
ORDER BY Total_Interactions DESC
LIMIT 1;
```
- Identify the busiest day of the week for interactions and tasks.
```sql
SELECT DAYNAME(Interaction_Date) AS Busiest_Day, COUNT(Interaction_ID) AS Total_Interactions
FROM Interactions
GROUP BY DAYNAME(Interaction_Date)
ORDER BY Total_Interactions DESC
LIMIT 1;
```
## Usage Examples
Demonstrate how to use the CRM database system with the provided data and scripts.

### Example Usage

Use Views:
```sql
SELECT * FROM CustomerSalesRepView;
SELECT * FROM InteractionLeadView;
```
Call Functions:
```sql
SELECT AvgLeadResponseTime();
```
Execute Stored Procedures:
```sql
CALL UpdateLeadCreationDates();
CALL TopSellingProducts();
```

These categorized SQL queries, views, functions, and stored procedures cover a range of operations and analytical tasks commonly performed in CRM systems. You can use them as building blocks to implement specific functionalities and optimize data retrieval and analysis within your database environment. Adjust and expand these examples based on your application's requirements and business logic.









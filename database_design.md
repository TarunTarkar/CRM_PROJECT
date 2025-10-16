# Database Schema

This document describes the database schema used in our CRM system.

## Entity-Relationship Diagram (ERD)

![CRM database system](https://github.com/1Elaigwu/CRM-MySQL-Database/assets/85877218/16a8baa4-8a1f-4165-bf1c-7e849e1e7e8b)

## Entities and Attributes

### Customers
- **Attributes**:
  - Customer_ID (Primary Key): Unique identifier for each customer.
  - First_Name (VARCHAR, 50): First name of the customer.
  - Last_Name (VARCHAR, 50): Last name of the customer.
  - Email (VARCHAR, 100): Email address of the customer.
  - Phone_Number (VARCHAR, 15): Phone number of the customer.
  - Address (VARCHAR, 255): Address of the customer.

### Leads
- **Attributes**:
  - Lead_ID (Primary Key): Unique identifier for each lead.
  - First_Name (VARCHAR, 50): First name of the lead.
  - Last_Name (VARCHAR, 50): Last name of the lead.
  - Email (VARCHAR, 100): Email address of the lead.
  - Phone_Number (VARCHAR, 15): Phone number of the lead.
  - Address (VARCHAR, 255): Address of the lead.
  - Source (VARCHAR, 100): Source of the lead (e.g., website, referral).
  - Status (VARCHAR, 50): Current status of the lead (e.g., new, contacted).
  - Notes (TEXT): Additional notes related to the lead.

### SalesRepresentatives
- **Attributes**:
  - SalesRep_ID (Primary Key): Unique identifier for each sales representative.
  - First_Name (VARCHAR, 50): First name of the sales representative.
  - Last_Name (VARCHAR, 50): Last name of the sales representative.
  - Email (VARCHAR, 100): Email address of the sales representative.
  - Phone_Number (VARCHAR, 15): Phone number of the sales representative.
  - Department (VARCHAR, 100): Department in which the sales representative works.

### Products
- **Attributes**:
  - Product_ID (Primary Key): Unique identifier for each product.
  - Product_Name (VARCHAR, 100): Name of the product.
  - Description (TEXT): Description of the product.
  - Price (DECIMAL, 10, 2): Price of the product.
  - Quantity_in_Stock (INT): Current quantity of the product in stock.

### Orders
- **Attributes**:
  - Order_ID (Primary Key): Unique identifier for each order.
  - Customer_ID (Foreign Key referencing Customers(Customer_ID)): Identifies the customer who placed the order.
  - Order_Date (DATE): Date when the order was placed.
  - Total_Amount (DECIMAL, 10, 2): Total amount of the order.

### Order_Items
- **Attributes**:
  - Order_Item_ID (Primary Key): Unique identifier for each order item.
  - Order_ID (Foreign Key referencing Orders(Order_ID)): Identifies the order to which the item belongs.
  - Product_ID (Foreign Key referencing Products(Product_ID)): Identifies the product included in the order item.
  - Quantity (INT): Quantity of the product ordered.
  - Unit_Price (DECIMAL, 10, 2): Price per unit of the product in the order item.

### Interactions
- **Attributes**:
  - Interaction_ID (Primary Key): Unique identifier for each interaction.
  - Lead_ID (Foreign Key referencing Leads(Lead_ID)): Identifies the lead involved in the interaction.
  - SalesRep_ID (Foreign Key referencing SalesRepresentatives(SalesRep_ID)): Identifies the sales representative involved in the interaction.
  - Interaction_Date (DATE): Date when the interaction took place.
  - Interaction_Type (VARCHAR, 50): Type of interaction (e.g., call, meeting).
  - Outcome (VARCHAR, 100): Outcome or result of the interaction.
  - Notes (TEXT): Additional notes related to the interaction.

### Tasks
- **Attributes**:
  - Task_ID (Primary Key): Unique identifier for each task.
  - Description (TEXT): Description of the task.
  - Due_Date (DATE): Deadline for completing the task.
  - Status (VARCHAR, 50): Current status of the task (e.g., pending, completed).
  - Priority (VARCHAR, 50): Priority level of the task.
  - Assigned_To (Foreign Key referencing SalesRepresentatives(SalesRep_ID)): Identifies the sales representative assigned to the task.

## Relationships

- **Customers and Orders**: One-to-Many relationship. Each customer can place multiple orders, but each order is placed by one customer.
- **Orders and Order_Items**: One-to-Many relationship. Each order can contain multiple order items (products), but each order item belongs to only one order.
- **Leads and Interactions**: One-to-Many relationship. Each lead can have multiple interactions, but each interaction is associated with one lead.
- **SalesRepresentatives and Interactions/Tasks**: One-to-Many relationship. Each sales representative can have multiple interactions and tasks, but each interaction or task is assigned to one sales representative.

Understanding these relationships helps in designing efficient queries and maintaining data integrity within the database schema.



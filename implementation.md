# Implementation Details

## Technologies Used
- Database Management System: MySQL (InnoDB engine)
- Programming Language: SQL for schema creation, data insertion, and query execution

## Schema Creation
- Schema Name: CustomerRelationshipManagementSystem
  - Created to encapsulate all tables related to the CRM system.

## Tables Created
- Customers
Stores customer information such as name, email, phone number, and address.

- Leads
Manages leads with associated details like source, status, and interaction notes.

- SalesRepresentatives
Contains information about sales representatives, including department affiliation.

- Products
Catalogs products available for sale with details such as name, description, price, and stock quantity.

- Orders
Records customer orders including order date, total amount, and linked customer ID.

- Order_Items 
Represents line items within orders, detailing products, quantities, and unit prices.

- Interactions
Logs interactions between leads and sales representatives with outcomes and notes.

- Tasks
Manages tasks assigned to sales representatives with descriptions, due dates, and statuses.

## Foreign Key Relationships
- Established between tables to maintain data integrity and enforce referential integrity constraints.

## Data Insertion
- Initial data populated into tables for customers, leads, sales representatives, products, orders, interactions, and tasks.

## SQL Queries
- Demonstrated usage of SQL queries for various CRM-related tasks, including customer management, lead tracking, sales representative analysis, product management, order processing, interaction logging, and task management.
 
## Views, Functions, and Stored Procedures
- Views created to simplify complex queries and provide a structured view of customer-sales representative relationships and interaction details.
- Functions and stored procedures designed to calculate average response times, update lead creation dates, and retrieve top-selling products.

## Usage Examples
- Presented examples showcasing how to use views, functions, and stored procedures within the CRM system for data retrieval and analysis purposes.

## Conclusion
The implementation details highlight the creation of a robust relational database schema tailored for managing customer relationships, sales activities, and task assignments within a CRM context. The use of MySQL, SQL queries, and database objects (views, functions, procedures) demonstrates the versatility and efficiency of managing CRM data and operations. Additional customization and expansion can be applied based on specific business requirements and operational needs.



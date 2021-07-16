# Database design
This repository is a step-by-step guide to designing a relational database using PostgreSQL.

## Table of Contents
[Introduction](#introduction)

[Data to be used](#data-to-be-used)

[Purpose of the database](#purpose-of-the-database)

[Understand the information required](#understand-the-information-required)

[Divide the information into tables](#divide-the-information-into-tables)

[Specify primary keys and generate other keys](#specify-primary-keys-and-generate-other-keys)

[Set up table relationships](#set-up-table-relationships)


<a name="headers"/>

### Introduction

Databases are at the core of businesses because they help store and communicate information related to various business processes. Such information could include sales transactions, product inventory, customer profile and much more. And through the use of database management systems (DBMS), the data stored can be queried, manipulated, structured and restricted. The stored data can later be analyzed to drive business decisions. 

Within this repository, I will carry out the following:
1) Migrate a sample data into a PostgreSQL database
2) Normalize the database table
3) Model relationships between the resulting tables

The structure of this guide will be as follows:
a) Data to be used
b) Determine the Purpose of the database
c) Understand the information required
d) Specify primary keys and generate other keys
e) Set up table relationships
f) Database schema
g) Access rights and restrictions
H) Other considerations

All along the way, I will be modeling the table relationships and showing the SQL code used.

### Data to be used
The data being used is possibly a fictional dataset for certain superstore for a period of 4 years. The data is in a .csv format. See the attached link to the source file: [here](https://www.kaggle.com/rohitsahoo/sales-forecasting) . I will include this file within the repository.

In the "Understand the information required" section, I will briefly describe the various columns and the possible relationships between them. Next we will need to determine the purpose of this database being built.
### Purpose of the database
I will be role-playing here a bit to determine the purpose of this database. This is because the purpose of the database will determine the design specifications of this database or warehouse being built. 
The purpose of this database is to collect and store orders and sales data. We would not be storing this data for the purpose of analytics. Hence, the design considerations for this particular database will be suited for an Online Transaction Processing (OLTP) configuration. The database will be write intensive and normalized to reduce redundencies and save space. 
If we were more concerned with analyzing or visualizing the sales data showed here, and Online Analytical Process (OLAP) will be suitable, and denormalized tables within the database will be more appropriate. 
### Understand the information required



| Column name | Description | Datatype |
| ----------- | ----------- | ---------|
| Row ID | Unique row number for each record | int|
| Order ID | Order id for the items being shipped|varchar|
|Order Date| Date the order was made|Date|
|Ship Date| Date the product was shipped| Date|
|Ship Mode| Class of shipping| char|
|Customer ID| Unique ID of customer who orders items| varchar|
|Customer Name| Name of customer|string|
|Segment| Class/types of customer| char|
|Country| Country customer lives in| char|
|City| City customer lives in| char|
|State| State the customer lives in| char|
|Postal Code| Zip code the customer lives in| int|
|Region| Region where the customer lives in| string|
|Product ID| Unique ID of product being ordered| varchar|
|Category| Category of product being ordered| string|
|Sub-category|Sub-category of the product being ordered| string|
|Product Name| Actual name of the product| string|
|Sales| Amount the product was sold for at the order date | float|

From the above table, we can notice certain relationships between various columns. It appears that some column information are dependent on each other. To futher drive home this point, we would define various relationships that exists between columns within this table. 

i) **Functional dependency**: A relationship between 2 attributes, usually between a primary key and non-key attributes. For any relation, attribute Y is functionally dependent on X (PK) if for every instance of X, that value of X uniquely determines the value of Y. This is a relationship between 2 attributes (X,Y) for every instance X, the value of X uniquely determines the value of Y. This is represented as X >> Y. For instance in the above table, the attribue "Sales" is functionally dependent on "Row ID". This is because the Row ID uniquely identifies the Sales amount.

ii) **Transitive dependency**: This exists when the following occurs: X >> Y >> Z. This denotes that Y is functionally dependent on X and Z is functionally dependent on Y. Hence, we can conclude that Z is transitively dependent on X as long as Y is not a candidate key. From our current data, "Sales" amount is functionally depended on the "Product name" (Sales amount is uniquely determined by the product number), while the "Product ID" uniquely determines the "Product Name". Therefore this can be denoted as: Product ID >> Product Name >> Sales. Sales amount can be said to have a transitive dependency with Product ID as the product name is not considered as a candidate key.

Other relationships between columns or tables include the following

iii) **One to One relationships**: Relationship between 2 entities (A & B) where an element in A is linked to an element in B.

iv) **One to many**: A relationship between 2 entities (A & B) where an element of A is linked with many element in B.

V) **Many to many**: A relationship between 2 entities (A & B) where multiple records within an entity A is linked with many records within entity B.


### Migrate the data

First we will need to create a table in the empty database. 
Then we will need to migrate the data from the csv to the database. See the SQL code in the window below:


Also see the entity relationship diagram in the image below. Please note that the retangular shape represents the table name, while the oval shapes represent the column names.

![entity relationship diagram 1](https://user-images.githubusercontent.com/83844773/125898140-67d77900-3292-4bb4-8095-86bdfc96890c.png)

In line with our requirements, we will have to break up this wide table into smaller tables in order to reduce redundancy within the database. 

### Divide the information into tables

There are generally 2 methods/ rule of thumbs to approaching normalization (breaking up the large table). And they are discussed below:

1) Translating entity ralationship (ER) diagram into relations. This can be done by transforming all of the many-to-many relationships within our wide table into one-to-many relationships.
2) Functional dependencies: By ensuring that there are no transitive dependencies within individual tables 

The entity relationship method will be used to simplify the wide table which will be accomplished by splitting the wide table into various entities. The resulting entities will be in various one to many relationships with other entities. With our wide table, I broke down the data into 10 various entities. See the entity tables in the screenshots below:

![normalized tables 2](https://user-images.githubusercontent.com/83844773/125912577-c8cd9b9a-4901-475c-9c7d-4edc0e2dd06f.png)
![normalized tables 1](https://user-images.githubusercontent.com/83844773/125912579-3574cde4-15aa-4c3e-96fc-d7f84d6d2f9a.png)

There will be no SQL code for this step as it can be seen as an intermediate step.

Up next, we will figure out how to assign primary keys to select columns and generate surrogate and foreign keys. These keys are fundamental to the relational database design as we will find out soon.

### Specify primary keys and generate other keys
### Set up table relationships

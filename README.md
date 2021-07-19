# Database design
This repository is a step-by-step guide to designing a relational database using PostgreSQL.

## Table of Contents
[Introduction](#introduction)

[Data to be used](#data-to-be-used)

[Purpose of the database](#purpose-of-the-database)

[Understand the information required](#understand-the-information-required)

[Migrate the data into the database](#migrate-the-data-into-the-database)

[Normalize the table](#normalize-the-table)

[Specify primary keys and generate other keys](#specify-primary-keys-and-generate-other-keys)

[Set up table relationships](#set-up-table-relationships)

[Database schema](#database-schema)

[Assign access rights and restrictions](#assign-access-rights-and-restrictions)

[Other considerations](#other-considerations)



<a name="headers"/>

### Introduction

Databases are at the core of businesses because they help store and communicate information related to various business processes. Such information could include sales transactions, product inventory, customer profile and much more. And through the use of database management systems (DBMS), the data stored can be queried, manipulated, structured and restricted. The stored data can later be analyzed to drive business decisions. 

Within this repository, I will carry out the following:
1) Migrate a sample data into a PostgreSQL database
2) Normalize the database table
3) Model relationships between the resulting tables
4) Restrict access to the data

The roadmap of this guide will be according to the steps below:

a) Data to be used

b) Determine the Purpose of the database

c) Understand the information required

d) Migrate the data into the database

e) Normalize the table

f) Specify primary keys and generate other keys

g) Set up table relationships

h) Database schema

i) Assign access rights and restrictions

j) Other considerations

All along the way, I will showing the current models of the table relationships and also SQL code used.

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


### Migrate the data into the database

First we will need to create a table in the empty database. 
Then we will need to migrate the data from the csv to the database. See the SQL code in the window below:


Also see the entity relationship diagram in the image below. Please note that the retangular shape represents the table name, while the oval shapes represent the column names.

**Figure1**

![entity relationship diagram 1](https://user-images.githubusercontent.com/83844773/125898140-67d77900-3292-4bb4-8095-86bdfc96890c.png)

In line with our requirements, we will have to break up this wide table into smaller tables in order to reduce redundancy within the database. 

### Normalize the table

There are generally 2 methods/ rule of thumbs to approaching normalization (breaking up the large table). And they are discussed below:

1) Translating entity relationship (ER) diagram into relations. This can be done by transforming all of the many-to-many relationships within our wide table into one-to-many relationships.
2) Functional dependencies: By ensuring that there are no transitive dependencies within individual tables 

The entity relationship method will be used to simplify the wide table which will be accomplished by splitting the wide table into various entities. The resulting entities will be in various one to many relationships with other entities. With our wide table, I broke down the data into 10 various entities. See the entity tables in the screenshots below:

**Figure2**

![normalized tables 2](https://user-images.githubusercontent.com/83844773/125912577-c8cd9b9a-4901-475c-9c7d-4edc0e2dd06f.png)
![normalized tables 1](https://user-images.githubusercontent.com/83844773/125912579-3574cde4-15aa-4c3e-96fc-d7f84d6d2f9a.png)

There will be no SQL code for this step as it can be seen as an intermediate step.

Up next, we will figure out how to assign primary keys to select columns and generate surrogate and foreign keys. These keys are fundamental to the relational database design as we will find out soon.

### Specify primary keys and generate other keys

Primary keys represent a unique key for each record within an entity table.  These keys are naturally present within the data being migrated into the database. All entity tables should have a primary key. In selecting primary keys within the various tables, the following criteria was considered:
1) The primary key should consist of one column whenever possible
2) The data type should be either an integer or a short, fixed-width character.
3) No null values
4) If you're using a character data type, the primary key should exclude differential capitalization, spaces, and special characters, which might be difficult to remember.
5) The primary key should not change over time


From the new entity tables, we would select the following keys. Refer to figure 2 for the tables' diagrams.

"Product" table - Product ID

"Sales" table - Row ID

"Order" table - Order ID

"Customer" table - Customer ID

"Postal code" table - Postal code

However, the follow tables do not have adequate candidate keys.

"State" table

"City" table

"Region" table

"Sub-category" table

"Category" table

Due to this, we would generate surrogate keys for the tables without suitable primary keys. Surrogate keys are basically artificial keys generated by the system. After generating these keys on the specific, they will be selected as the primary keys. As a result, the tables without primary keys will have the following keys.

"State" table - State ID

"City" table - City ID

"Region" table - Region ID

"Sub-category" table - Sub-category ID

"Category" table - Category ID

Next, we assign foreign keys to particular tables. Foreign keys are columns that point to other entity tables. They are at the heart of relational databases as they enable us create a link with other entity tables. It is important to note the following:
1) Foreign keys are not actually keys because they can contain duplicates and null values.
2) Only foreign keys that are primary keys of the reference table are actually allowed (Referential integrity).

We would select foreign keys based on the relationships the entity tables have with one another. For instance, the entity "State" has a one-to-many relationship with "City" as multiple cities make up a single state. In order to create a reference between both tables, we will include a column with the foreign key called "State-ID" in the City table referencing the states where the cities belong to. State ID is a primary key within the state table, therefore this creates a link between between both tables.

See figure 3 below the various primary keys and foreign keys on the different entity tables. Please note that the primary key columns are in orange color, while assigned foreign keys are in light blue:


**Figure 3**

![PK and FK 1](https://user-images.githubusercontent.com/83844773/126193482-5f17f803-d4d5-44fb-ab08-839f4d70679d.png)
![PK and FK 2](https://user-images.githubusercontent.com/83844773/126193484-8a6e491f-5683-463a-89f4-c02a2164ca14.png)

See the postgre SQL code required to assign all the specified keys below: 



Up next, we will glue up the tables using the foreign keys.

### Set up table relationships

Using the below code, we would link the entity tables together using the various foreign keys. The entity relationship of the end result will described by figure 4:




**Figure 4**:

![Full er diagram](https://user-images.githubusercontent.com/83844773/126207487-76fd3bac-dbee-42bd-a4a6-c0eed776f7cc.png)


### Database schema

### Assign access rights and restrictions

### Other considerations

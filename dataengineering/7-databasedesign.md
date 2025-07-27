#Database Design
##Ch1 - Processing Storing and Organizing Data

### OLTP and OLAP (Online Transactional Processing & Online Analytical Processing)

The fundamental question here is how we should organize and manage data. 
- _**Schemas**_ How shhould data be logically organized. 
- _**Normalization**_ Should my data have minimal dependency or redundancy. 
- _**Views**_ what joins will be used most often. 
- _**Access control**_ should all data users have the same level of access? 
- _**DBMS**_ How do I chose between all the SQL and noSQL options. 
The answers to all of these questions depends on the intended use of the data.

OLTP and OLAP are approaches to data processing.
They help define the way data is going to flow, be structured and stored.

- OLTP is **Online Transaction Processing** 
	This is oriented around transactions.
- OLAP is **Online Analytical Processing** 
	This is oriented around analytics

[[!OLTPvsOLAP](OLPTvsOLAP.png)]

OLAP and OLTP systems work together. They need each other.

[[!OLTPOLAP](OLTPOLAP.png)]

OLTP data is usually stored in an operational database that is pulled and cleaned to create an OLAP data warehouse

**Takeaways**
Before implementing anything,
- Figure out the business requirements.
- Do you need an OLAP or OLTP approach?
- OLAP or OLTP or something else.

### Storing Data

Data can be stored in three different ways.

1. Structured data

- This follows a schema.
- Data types and relationships are defined.
- e.g., Usually stored in relational databases, SQL.

2. Unstructured data

- It is schemaless.
- It is not clean. e.g, photos, chat logs, MP3

3. Semi-Structured data

- This does not follow large schema.
- Self-describing structure.
- Can be stored in noSQL, JSON, XML

Because it is clean an organized structured data is easier to analyze.

#### _Storing data beyond traditional database_

- **Traditional databases**: For storing realtime relational structured data. OLTP
- **Data warehouses**: For analyzing archived structured data. OLAP
- **Data lakes** : 
	For storing data of all structures.
	For analyzing big data.

#### _Data warehouses_ 

- Data warehouses are optimize for readonly analytics.
- Organized for reading/aggregating data.
- Combined data from multiple sources and use Massively Parallel Processing (MPP)
- They used dimensional modelling and denormalized schema.

Amazon, google and MS all offer data warehouse solutions known respectively as redshift, BigQuery and Azure SQL data Warehouse.

**A Data Mart** is a subset of a data warehouse dedicated to a specific topic.

#### _Data Lakes_
- Stores all types of data at a lower cost.
- Need to be cataloged else it become a data swamp
- Schema-on-read instead of schema-on-write.
- Analytics can now be run on data lakes. Analytics could be run on Apache Spark and Hadoop.
- Useful for deep learning and data discovery because activities require much data.

#### _Extract, Transform, Load & Extract, Load, Transform_

When thinking of how to store data, it is necessary to think of how the data will get to the storage 'facility' and in what form.
ETL and ELT are two different approaches for describing dataflows.

- In ETL, data is transformed before it is being loaded into storage. Usually following the storage's schema.
- In ELT, the data is stored in its native form in a storage solution like a data lake



#### Database design
- Database designs refer to how data is logically stored. It determines how data will be read and updated. 

There are two important concepts when it comes to database designs: Database models and database schemas.

- Database designs use **Data Models** which are high level specifications for data structure.Popular models are Relational database Models. Others are NoSQL data model, Object Oriented models and Network models. 
- A data schema is the blueprint of the database. 
It is how the data model is implemented. It defines tables, fields, relationships, idexes and views. When updating a database, the schema must be respected. In essence, it takes the structure more granularly.

The first stage of a database design is Data Modelling.

**Data Modeling**
It is the process of creating the data model for a database.

1. _Conceptual data model: This describes entities, relqtionships and attributes_:
- Tools: Data sctucture diagrams: Entity relationship diagrams.

2. _Logical data model: Defines tables, columns and relationships._
- Tools: Data models and schemas

2. Physical data model: Defines how data will be physically stored at the lowest level of abstraction.
- Tools: Partitions, CPUs, Indexes, backup systems and tablespaces.

These three levels of data modeling ensure consitency and provide a plan for implementation.


**Dimensional Modeling**

It is an adaptation of relational model for warehouse design. 

- It is optimized for OLAP queries: Aggregate data not update (OLTP). 
- Dimensional models are made up of two types of tables: Facts and dimentions.

***Fact Tables***
- What the fact table holds is decisded by the business use case.
- It holds metric records.
- Changes regularly adn connects to dimensions via foreign keys.

***Dimension Tables**
- Holds decriptions of attributes.
- Does not change as often.



**Exercise**

Deciding fact and dimension tables
Imagine that you love running and data. It's only natural that you begin collecting data on your weekly running routine. You're most concerned with tracking how long you are running each week. You also record the route and the distances of your runs. 
You gather this data and put it into one table called `Runs` with the following schema:

runs
duration_mins - float
week - int
month - varchar(160)
year - int
park_name - varchar(160)
city_name - varchar(160)
distance_km - float
route_name - varchar(160)
After learning about dimensional modeling, you decide to restructure the schema for the database. Runs has been pre-loaded for you.

_Instructions 2/2_

Create a dimension table called route that will hold the route information.
Create a dimension table called week that will hold the week information.

```sql
-- Create a route dimension table
CREATE TABLE route(
	route_id INTEGER PRIMARY KEY,
    route_name VARCHAR(160) NOT NULL,
    city_name VARCHAR(160) NOT NULL,
    distance_km float NOT NULL,
    park_name VARCHAR(160) NOT NULL
);
-- Create a week dimension table
CREATE TABLE week(
	week_id INTEGER PRIMARY KEY,
    week INTEGER NOT NULL,
    month VARCHAR(160) NOT NULL,
    year INTEGER NOT NULL
);
```




**Exercise 2**

Querying the dimensional model
Here it is! The schema reorganized using the dimensional model: 

Let's try to run a query based on this schema. How about we try to find the number of minutes we ran in July, 2019? We'll break this up in two steps. 
First, we'll get the total number of minutes recorded in the database. Second, we'll narrow down that query to week_id's from July, 2019.

Instructions 1/2

Calculate the sum of the duration_mins column.

```sql
SELECT 
	-- Select the sum of the duration of all runs
	SUM(duration_mins)
FROM 
	runs_fact;
```

Instructions 2/2

Join week_dim and runs_fact.
Get all the week_id's from July, 2019.


[[!Ch1Summary](Ch1Summary.png)]


## Ch2 - Database Schemas and Normalization

### Star and Snowflake Schema

**The Star Schema**

The star schema is the simplest form of dimensional models to the extent that **star** and **dimensional models** are are usually used interchangeably.
The star schema is made up of two tables.

1. The Fact table: This holds records of a metric, changes regularly and connects to the dimension tables via foreign keys.
2. The Dimension tables: This holds records that further describe records on the fact table. In essence, it holds descrioptions of attributes and doesn't change as much.

The start schema got it's name because it tends to look like a star with different extention points.

**The Snowflake schema**

- It is an extension of the star schema.
- The fact table is the same, as it is in the previous example. But the dimension tables extend more.
- The star schema extends one dimension whereas the snowflake schme extends more than one dimension. This is because the dimension tables are normalized.

[[!star vs Dim Schema](starvsdim.png)]

**What then is Normalization**?
- It is a database design technique.
- It is a technique that divides tables into smaller tables and connects them via relationships.
- The goal is to reduce redundancy and increase data integrity.

How is this done?

- Identify repeating groups of data and create smaller tables for them.


**Exercise**

Adding foreign keys
Foreign key references are essential to both the snowflake and star schema. When creating either of these schemas, correctly setting up the foreign keys is vital because they connect dimensions to the fact table. 
They also enforce a one-to-many relationship, because unless otherwise specified, a foreign key can appear more than once in a table and primary key can appear only once.

The fact_booksales table has three foreign keys: book_id, time_id, and store_id. In this exercise, the four tables that make up the star schema below have been loaded. However, the foreign keys still need to be added. 

_Instructions_

In the constraint called sales_book, set book_id as a foreign key.
In the constraint called sales_time, set time_id as a foreign key.
In the constraint called sales_store, set store_id as a foreign key.


```sql
-- Add the book_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_book
    FOREIGN KEY (book_id) REFERENCES dim_book_star (book_id);
    
-- Add the time_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_time
    FOREIGN KEY (time_id) REFERENCES dim_time_star (time_id);
    
-- Add the store_id foreign key
ALTER TABLE fact_booksales ADD CONSTRAINT sales_store
    FOREIGN KEY (store_id) REFERENCES dim_store_star (store_id);
```


**Exercise 2**

Extending the book dimension
In the video, we saw how the book dimension differed between the star and snowflake schema. The star schema's dimension table for books, dim_book_star, has been loaded and below is the snowflake schema of the book dimension. 

In this exercise, you are going to extend the star schema to meet part of the snowflake schema's criteria. Specifically, you will create dim_author from the data provided in dim_book_star.

_Instructions 1/2_

Create dim_author with a column for author.
Insert all the distinct authors from dim_book_star into dim_author.

```sql
-- Create dim_author with an author column
CREATE TABLE dim_author (
    author VARCHAR (256) NOT NULL
);

-- Insert authors 
INSERT INTO dim_author
SELECT DISTINCT author FROM dim_book_star;
```

Instructions 2/2

Alter dim_author to have a primary key called author_id.
Output all the columns of dim_author.

```sql
-- Create a new table for dim_author with an author column
CREATE TABLE dim_author (
    author varchar(256)  NOT NULL
);

-- Insert authors 
INSERT INTO dim_author
SELECT DISTINCT author FROM dim_book_star;

-- Add a primary key 
ALTER TABLE dim_author ADD COLUMN author_id SERIAL PRIMARY KEY;

-- Output the new table
SELECT * FROM dim_author;
```

### Normalized and Denormalized databases

1. Normalization saves space
2. It reduces data redundancy, hence less records to update.
3. Enforces data consistency.
4. Safer updating, removing and inserting.
5. Easier to redesign by extending.


Disadvantages:
- More complex queries, hence more CPU

**Exercise: Querying the star schema**
The novel genre hasn't been selling as well as your company predicted. To help remedy this, you've been tasked to run some analytics on the novel genre to find which areas the Sales team should target.
To begin, you want to look at the total amount of sales made in each state from books in the novel genre.

Luckily, you've just finished setting up a data warehouse with the following star schema:


The tables from this schema have been loaded. Note that you should not use aliases in FROM and JOIN statements.

Instructions

Select state from the appropriate table and the total sales_amount.
Complete the JOIN on book_id.
Complete the JOIN to connect the dim_store_star table
Conditionally select for books with the genre novel.
Group the results by state.

```sql
-- Output each state and their total sales_amount
SELECT dim_store_star.state, SUM(sales_amount)
FROM fact_booksales
	-- Join to get book information
    JOIN dim_book_star ON fact_booksales.book_id = dim_book_star.book_id
	-- Join to get store information
    JOIN dim_store_star ON fact_booksales.store_id = dim_store_star.store_id
-- Get all books with in the novel genre
WHERE  
    dim_book_star.genre = 'novel'
-- Group results by state
GROUP BY
    dim_store_star.state;
```

**Exercise: Querying the snowflake schema**
Imagine that you didn't have the data warehouse set up. Instead, you'll have to run this query on the company's operational database, which means you'll have to rewrite the previous query with the following snowflake schema:



The tables in this schema have been loaded. Remember, our goal is to find the amount of money made from the novel genre in each state.

Instructions

Select state from the appropriate table and the total sales_amount.
Complete the two JOINS to get the genre_id's.
Complete the three JOINS to get the state_id's.
Conditionally select for books with the genre novel.
Group the results by state.

```sql
-- Output each state and their total sales_amount
SELECT dim_state_sf.state, SUM(sales_amount)
FROM fact_booksales
    -- Joins for genre
    JOIN dim_book_sf on fact_booksales.book_id = dim_book_sf.book_id
    JOIN dim_genre_sf on dim_book_sf.genre_id = dim_genre_sf.genre_id
    -- Joins for state 
    JOIN dim_store_sf on dim_store_sf.store_id = fact_booksales.store_id 
    JOIN dim_city_sf on dim_city_sf.city_id = dim_store_sf.city_id
	JOIN dim_state_sf on  dim_state_sf.state_id = dim_city_sf.state_id
-- Get all books with in the novel genre and group the results by state
WHERE  
    dim_genre_sf.genre = 'novel'
GROUP BY
    dim_state_sf.state;
```
**Exercise: Updating countries**
Going through the company data, you notice there are some inconsistencies in the store addresses. 
These probably occurred during data entry, where people fill in fields using different naming conventions. 
This can be especially seen in the country field, and you decide that countries should be represented by their abbreviations. The only countries in the database are Canada and the United States, which should be represented as USA and CA.

In this exercise, you will compare the records that need to be updated in order to do this task on the star and snowflake schema. dim_store_star and dim_country_sf have been loaded.

Instructions 1/2

Output all the records that need to be updated in the star schema so that countries are represented by their abbreviations.


```sql
-- Output records that need to be updated in the star schema
SELECT * FROM dim_store_star
WHERE country != 'USA' AND state !='CA';
```
Instructions 2/2
How many records would need to be updated in the snowflake schema?



**Exercise: Extending the snowflake schema**

The company is thinking about extending their business beyond bookstores in Canada and the US. Particularly, they want to 
expand to a new continent. In preparation, you decide a continent field is needed when storing the addresses of stores.

Luckily, you have a snowflake schema in this scenario. As we discussed in the video, the snowflake schema is typically 
faster to extend while ensuring data consistency. Along with dim_country_sf, a table called dim_continent_sf has been loaded. 
It contains the only continent currently needed, North America, and a primary key. In this exercise, you'll need to extend dim_country_sf to reference dim_continent_sf.

Instructions

Add a continent_id column to dim_country_sf with a default value of 1. Note thatNOT NULL DEFAULT(1) constrains a value from being null and defaults its value to 1.
Make that new column a foreign key reference to dim_continent_sf's continent_id.


```sql
-- Add a continent_id column with default value of 1
ALTER TABLE dim_country_sf
ADD continent_id int NOT NULL DEFAULT(1);

-- Add the foreign key constraint
ALTER TABLE dim_country_sf ADD CONSTRAINT country_continent
   FOREIGN KEY (continent_id) REFERENCES dim_continent_sf(continent_id);
   
-- Output updated table
SELECT * FROM dim_country_sf;
```


### Normal forms

Normal forms are the extent to which data can be normalized. 

[[!Normal forms](Normal forms.png)]

As seen from the diagram, there are normal forms up to the 6th level. But we'd deal with just the 1st 3.

1. First Normal Form (1NF).

Rules:

- Each record must be unique
- Each cell must hold one value.

2. 2NF

- Must satisfy 1NF and
- If Primary key is one column then automatically satisfies 2NF.
- If there is a composite primary key, then each non-key column must be dependent on all keys. (A composite primary key is a primary key made up of two or more columns.

3. Requires 2NF to be satisfied.
- Satisfies 2NF
- No transitive dependencies. Non-key columns can't depend on other non key columns.


**Data Anomalies**

- A database not normalized enough is prone to three types of anomaly errors.
	UPDATE, INSERTION and DELETION

**Exercise: Converting to 1NF**

In the next three exercises, you'll be working through different tables belonging to a car rental company. Your job is to explore different schemas and gradually 
increase the normalization of these schemas through the different normal forms. At this stage, we're not worried about relocating the data, but rearranging the tables.

A table called customers has been loaded, which holds information about customers and the cars they have rented.

Instructions 1/2

Question
Does the customers table meet 1NF criteria?

Possible answers


Yes, all the records are unique.

No, because there are multiple values in cars_rented and invoice_id

No, because the non-key columns don't depend on customer_id, the primary key.

Instructions 2/2

cars_rented holds one or more car_ids and invoice_id holds multiple values. Create a new table to hold individual car_ids and invoice_ids of the customer_ids who've rented those cars.
Drop two columns from customers table to satisfy 1NF

```sql
-- Create a new table to hold the cars rented by customers
CREATE TABLE cust_rentals (
  customer_id INT NOT NULL,
  car_id VARCHAR(128) NULL,
  invoice_id VARCHAR(128) NULL
);

-- Drop two columns from customers table to satisfy 1NF
ALTER TABLE customers
DROP COLUMN cars_rented,
DROP COLUMN invoice_id;
```

**Exercise: Converting to 2NF**

Let's try normalizing a bit more. In the last exercise, you created a table holding customer_ids and car_ids. This has been expanded upon and the resulting table, customer_rentals, has been loaded for you. Since you've got 1NF down, it's time for 2NF.

Instructions 1/2

Question
Why doesn't customer_rentals meet 2NF criteria?

Answers

	Because there are non-key attributes describing the car that only depend on one primary key, car_id.


Instructions 2/2

Create a new table for the non-key columns that were conflicting with 2NF criteria.
Drop those non-key columns from customer_rentals.


```sql
-- Create a new table to satisfy 2NF
CREATE TABLE cars (
  car_id VARCHAR(256) NULL,
  model VARCHAR(128),
  manufacturer VARCHAR(128),
  type_car VARCHAR(128),
  condition VARCHAR(128),
  color VARCHAR(128)
);

-- Insert data into the new table
INSERT INTO cars
SELECT DISTINCT
  car_id,
  model,
  manufacturer,
  type_car,
  condition,
  color
FROM customer_rentals;

-- Drop columns in customer_rentals to satisfy 2NF
ALTER TABLE customer_rentals
DROP COLUMN car_id,
DROP COLUMN model, 
DROP COLUMN manufacturer,
DROP COLUMN condition,
DROP COLUMN color;
```


**Exercise: Converting to 3NF** 
Last, but not least, we are at 3NF. In the last exercise, you created a table holding car_idss and car attributes. This has been expanded upon. For example, car_id is now a primary key. The resulting table, rental_cars, has been 
loaded for you.

Instructions 1/2

Question
Why doesn't rental_cars meet 3NF criteria?

Answers

Because there are two columns that depend on the non-key column, model.

Instructions 2/2

Create a new table for the non-key columns that were conflicting with 3NF criteria.
Drop those non-key columns from rental_cars.

```sql
-- Create a new table to satisfy 3NF
CREATE TABLE car_model(
  model VARCHAR(128),
  manufacturer VARCHAR(128),
  type_car VARCHAR(128)
);

-- Drop columns in rental_cars to satisfy 3NF
ALTER TABLE rental_cars
DROP COLUMN manufacturer, 
DROP COLUMN type_car;
```

[[!Ch 2 Review](Review Ch2.png)]

## Ch3: Database Views

### Database Views

Wikipedia: A **VIEW** is a resulkt of a stored query of a database which users can query just as they would a normal database.
**Views are virtual tables that are not part of the database physical schema**
- The Query, not the data in the view is stored.
- The Data is aggregated from the same database.
- Data from the view can be queried like a regular table in the database.
- No need to retype queries or alter schemas.

*Creating a view*: Take the query, add a line before it to create a view.

```sql
CREATE VIEW view_name AS
SELECT col1, col2
FROM table_name
WHERE condition;
```

After exiting the code, the view can be queried.

It it important to keep track of all the views. All views can be viewed by running the code `SELECT * FROM INFORMATION_SCHEMA.views`. Running this code results in a long list of views given that 
DBMS have their own views. To exclude all that and get only what you created, run this;

```sql
SELECT * FROM INFORMATION_SCHEMA.views
WHERE table_schema NOT in ('pg_catalog', 'Information_schema')
```

This excludes built-in view categories.

*Benefits of views*
- They don't take up storage, except for the query which is minimal.
- They are a form of access control. Instead of giving access to a full sensitive roll, you can limit access only to the view.
- It masks the complexity of queries.

**Exercise: Viewing views**

Because views are very useful, it's common to end up with many of them in your database. It's important to keep track of them so that database users know what is available to them.

The goal of this exercise is to get familiar with viewing views within a database and interpreting their purpose. This is a skill needed when writing database documentation or organizing views.

Instructions 1/3

Query the information schema to get views.
Exclude system views in the results.

```sql
SELECT * FROM INFORMATION_SCHEMA.views
WHERE table_schema NOT in ('pg_catalog', 'Information_schema')
```

Instructions 2/3

Question
What does view1 do?

Possible answers

- Returns the content records that have reviews of more than 4000 characters.

Instructions 3/3

Question
What does view2 do?

answers

Returns the top 10 highest scored reviews published in 2017.

**Exercise: Creating and querying a view**

Have you ever found yourself running the same query over and over again? Maybe, you used to keep a text copy of the query in your desktop notes app, but that was all before you knew about views!

In these Pitchfork reviews, we're particularly interested in high-scoring reviews and if there's a common thread between the works that get high scores. In this exercise, you'll make a view to 
help with this analysis so that we don't have to type out the same query often to get these high-scoring reviews.

Instructions 1/2

Create a view called high_scores that holds reviews with scores above a 9.


```sql
-- Create a view for reviews with a score above 9
CREATE VIEW high_scores AS
SELECT * FROM reviews
WHERE score > 9;
```

Instructions 2/2

Count the number of records in high_scores that are self-released in the label field of the labels table.

```sql
-- Create a view for reviews with a score above 9
CREATE VIEW high_scores AS
SELECT * FROM REVIEWS
WHERE score > 9;

-- Count the number of self-released works in high_scores
SELECT COUNT(*) FROM high_scores
INNER JOIN labels ON labels.reviewid = high_scores.reviewid
WHERE label = 'self-released';
```
### Managing Views

- Granting access and revoking a view
`GRANT privilege(s)` or `REVOKE privilege(s)`
`ON OBJECT`
`TO role` or `FROM role`

- Privileges: `SELECT`, `INSERT`,`UPDATE`,`DELETE`, etc
- Objects: table, view, schema, etc
- Roles; a database user or a group of users.


**Exercise: Creating a view from other views**
views can be created from queries that include other views. This is useful when you have a complex schema, potentially due to normalization, because it helps reduce the JOINS needed. 
The biggest concern is keeping track of dependencies, specifically how any modifying or dropping of a view may affect other views.

In the next few exercises, we'll continue using the Pitchfork reviews data. There are two views of interest in this exercise. 
top_15_2017 holds the top 15 highest scored reviews published in 2017 with columns reviewid,title, and score. artist_title returns a list of all reviewed titles and their respective 
artists with columns reviewid, title, and artist. From these views, we want to create a new view that gets the highest scoring artists of 2017.

Instructions 1/2

Create a view called top_artists_2017 with artist from artist_title.
To only return the highest scoring artists of 2017, join the views top_15_2017 and artist_title on reviewid.
Output top_artists_2017.

```sql
```

Instructions 2/2

Question
Which is the DROP command that would drop both top_15_2017 and top_artists_2017?

`DROP VIEW top_15_2017 CASCADE;`

**Exercise: Granting and revoking access**

Access control is a key aspect of database management. Not all database users have the same needs and goals, from analysts, clerks, data scientists, to data engineers. 
As a general rule of thumb, write access should never be the default and only be given when necessary.

In the case of our Pitchfork reviews, we don't want all database users to be able to write into the long_reviews view. Instead, the editor should be the only user able to edit this view.

Instructions

Revoke all database users' update and insert privileges on the long_reviews view.
Grant the editor user update and insert privileges on the long_reviews view.

```sql
-- Revoke everyone's update and insert privileges
REVOKE UPDATE, INSERT ON long_reviews FROM PUBLIC; 

-- Grant the editor update and insert privileges 
GRANT UPDATE, INSERT ON long_reviews TO editor; 
```

Exercise
Updatable views
In a previous exercise, we've used the information_schema.views to get all the views in a database. If you take a closer look at this table, you will notice a column that indicates whether the view is updatable.

Which views are updatable?

Instructions

Answers

long_reviews

```sql
SELECT table_name, is_updatable FROM information_schema.views
WHERE table_schema NOT IN ('pg_catalog', 'information_schema')
```
The query above can then be edited to include `AND is_updatable = 'True'`


Exercise
Redefining a view
Unlike inserting and updating, redefining a view doesn't mean modifying the actual data a view holds. Rather, it means modifying the underlying query that makes the view. 
In the last video, we learned of two ways to redefine a view: (1) CREATE OR REPLACE and (2) DROP then CREATE. CREATE OR REPLACE can only be used under certain conditions.

The artist_title view needs to be appended to include a column for the label field from the labels table.

Instructions 1/2

Question
Can the CREATE OR REPLACE statement be used to redefine the artist_title view?

Answers

Yes, as long as the label column comes at the end.

Instructions 2/2

Use CREATE OR REPLACE to redefine the artist_title view.
Respecting artist_title's original columns of reviewid, title, and artist, add a label column from the labels table.
Join the labels table using the reviewid field.

```sql
-- Redefine the artist_title view to have a label column
CREATE OR REPLACE VIEW artist_title AS
SELECT reviews.reviewid, reviews.title, artists.artist, labels.label
FROM reviews
INNER JOIN artists
ON artists.reviewid = reviews.reviewid
INNER JOIN labels
ON labels.reviewid = artists.reviewid;

SELECT * FROM artist_title;
```


### Materialized Views

Two tyupes of views:
1. Views or Non-materialized views.
2. Materialized views

Materialized views are physically materialized while non-materialized remain virtual.

In stead of storing a query, a materialized view stored the query results.
Querying a materialized view accesses the results, rather than the query like a non-materialized view.
Materialized views are refreshed or materialized when promted. That is, the query is run and the stored query results are updated. This can be scheduled depending on how frequent the data is updated.

Xtics of Materialized views:

- Long running queries.
- Useful for data warehouses.
- Underlying results don't change often.

Creating a materialized view is similar to the creation of non-materialized views.

`CREATE MATERIALIZED VIEW my_mv AS SELECT * FROM existing_table;`

To refresh;

`REFRESH MATERIALIZED VIEW my_mv;`

There is not specific `sql` command to schedule a refresh but cron jobs can be used. It is a unique space job scheduler.

**Managing dependencies**

- Materialized views have other dependencies.
- Not most efficient to schedule refresh at the same time.

**Tools for managing MATERIALIZED views**
- Use Directed Acyclic Graphs  (DAG).
- Pipeline Scheduler tools.


**Exercise**
Creating and refreshing a materialized view
The syntax for creating materialized and non-materialized views are quite similar because they are both defined by a query. One key difference is that we can refresh materialized views, 
while no such concept exists for non-materialized views. It's important to know how to refresh a materialized view, otherwise the view will remain a snapshot of the time the view was created.

In this exercise, you will create a materialized view from the table genres. A new record will then be inserted into genres. To make sure the view has the latest data, it will have to be refreshed.

Instructions

Create a materialized view called genre_count that holds the number of reviews for each genre.
Refresh genre_count so that the view is up-to-date.

```sql
-- Create a materialized view called genre_count 
CREATE MATERIALIZED VIEW genre_count AS
SELECT genre, COUNT(*) 
FROM genres
GROUP BY genre;

INSERT INTO genres
VALUES (50000, 'classical');

-- Refresh genre_count
REFRESH MATERIALIZED VIEW genre_count;

SELECT * FROM genre_count;
```

[[!Ch3 Revision](Ch3 Revision.png)]

## Ch4 Database Management

### Database Roles and Access Controls

*What are Database roles*? 
A database Role is an entity containing information that defines privileges. 
- Can you log in? 
- Can you create databases? 
- Can you write to tables?
- It also interacts with client authentication system: Password.
- They are assigned to one or more users.
- Roles are global across a database cluster installation. You ca reference roles across all individual databases in your cluster.


CREATING ROLES

- You can create roles with the CREATE role command. Eg., you are about to hire data analysts `CREATE ROLE data_analyst`. This is an empty ROLE.
- You can also set the ROLE attributes. E.g. you are about hiring an intern whose role ends and the end of the year; `CREATE ROLE intern WITH PASSWORD 'PasswordForIntern' VALID UNTIL '2026-01-01'`
One second into 2026, the password is no longer valid.
- If you want to CREATE a role with the ability to create databases `CREATE ROLE admin CREATEDB;`

Generally, these attributes define some but not all ROLES can do.

To change an attribute for an already created role, use the ALTER keyword. `ALTER ROLE admin CREATEROLE;`. This allows the admin role to create roles too.


#### GRANT and REVOKE privileges from roles.

To grant specific privileges on OBJECTs like database tables, views and schemas. yuou use `GRANT` and `REVOKE`
- E.g., If you want all data analyst to be able to update the ratings table, `GRANT UPDATE ON ratings TO data_analysts;`, if you don't need anymore, revoke it with `REVOKE UPDATE ON ratings FROM data_analysts;`

List of priivleges for ROLES in Postgresql are `SELECT`, `INSERT`,`UPDATE`,`DELETE`,`TRUNCATE`,`REFERENCES`,`TRIGGER`,`CREATE`,`CONNECT`,`TEMPORARY`,`EXECUTE`,and `USAGE`.

#### Users and Groups (Are both Roles)
- A role can function as a User and/or as a group.
	User Roles
	Group Roles
- GROUP Role: `CREATE ROLE data data_analyst;` You want all data analyst to have the same level of access.
- USER ROLE: The Intern role is a User Role. `CREATE ROLE intern WITH PASSWORD 'PasswordForIntern' VALID UNTIL '2026-01-01'`. Sometimes, you can actually use the user's name.

You may hire another staff to assist data analysts. You may want to grant them all the privileges of the data_analyst role, so you use `GRANT data_analyst TO alex;`.
Alex should be able to use the data analyst role. If Alex no longer need to do that type of work, use `REVOKE data_analyst FROM Alex;`

#### Common PostGresql roles

PostGreSQL has a set of default roles which provide acces to commonly needed privileged capabilities and information. These are in the shot below.

[[!Common PostGreSQL Roles](cmmonpgsqlroles.png)]

#### Benefits and Pitfalls of roles

*Benefits*
- Roles live after Users are deleted.
- They can even be created before the user accounts.
- They save DBAs' time.

*Pitfalls*

- Some users may be given too much access.

**Exercise: Create a role**
A database role is an entity that contains information that define the role's privileges and interact with the client authentication system. 
Roles allow you to give different people (and often groups of people) that interact with your data different levels of access.

Imagine you founded a startup. You are about to hire a group of data scientists. You also hired someone named Marta who needs to be able to login to your database. You're also about to hire a database administrator. In this exercise, you will create these roles.

Instructions 2/3

1. Create a role called data_scientist.

```sql
-- Create a data scientist role
CREATE ROLE data_scientist;
```

2. Create a role called marta that has one attribute: the ability to login (LOGIN).

```sql
-- Create a role for Marta
CREATE ROLE marta WITH LOGIN;
```

3. Create a role called admin with the ability to create databases (CREATEDB) and to create roles (CREATEROLE).

```sql
-- Create an admin role
CREATE ROLE admin WITH CREATEDB CREATEROLE;
```



**Exercise: GRANT privileges and ALTER attributes**

Once roles are created, you grant them specific access control privileges on objects, like tables and views. Common privileges being SELECT, INSERT, UPDATE, etc.

Imagine you're a cofounder of that startup and you want all of your data scientists to be able to update and insert data in the long_reviews view. In this exercise, you will enable those soon-to-be-hired data scientists by granting their role (data_scientist) those privileges. Also, you'll give Marta's role a password.

Instructions

Grant the data_scientist role update and insert privileges on the long_reviews view.
Alter Marta's role to give her the provided password.


```sql
-- Grant data_scientist update and insert privileges
GRANT UPDATE, INSERT ON long_reviews TO data_scientist;

-- Give Marta's role a password
ALTER ROLE marta WITH PASSWORD 's3cur3p@ssw0rd';
```


**Exercise: Add a user role to a group role**

There are two types of roles: user roles and group roles. By assigning a user role to a group role, a database administrator can add complicated levels of access to their databases with one simple command.

For your startup, your search for data scientist hires is taking longer than expected. Fortunately, it turns out that Marta, your recent hire, has previous data science experience and she's willing to chip in the interim. 
In this exercise, you'll add Marta's user role to the data scientist group role. You'll then remove her after you complete your hiring process.

Instructions

Add Marta's user role to the data scientist group role.
Celebrate! You hired multiple data scientists.
Remove Marta's user role from the data scientist group role.

```sql
-- Add Marta to the data scientist group
GRANT data_scientist TO Marta;

-- Celebrate! You hired data scientists.

-- Remove Marta from the data scientist group
REVOKE data_scientist FROM Marta;
```

### Table Partitioning

*Why Partition?*

- When tables grow, queries and updates can become slower because indices don't fit memory.
- The solution is split tables into smaller parts (=Partitioning).

Partitioning fits into **Physical data modeling**.

There are two types of partitioning:
1. Vertical Partitioning: In this case, you split a table, even fully normalized and link the table through a common key. See example below.

[[!Vertical partitioning](verticalPart.png)]

In this case, the long_description is retrieved very rarely. This could then be stored on a slower medium. Doing this will reduce query time for the first table.

2. Horizontal Partitioning: In this case, instead of splitting tables by columns, we split them by rows. We use the `PARTITION BY` statement and split by timestamp.

You Use declarative partitioning.
- Add the `PARTITION BY` clause to the table statement and pass the column you want to partition by `timestamp`, in our case.
- Then you create the partitions by Using the `PARTITION OF` clause.
- It is advisable to add an index to the column used for partitioning.

[[!Ex Horizontal Partitioning](horizontalPart.png)]

Pros:
- Indices of heavily used partitions fit in memory.\
- Move into specific medium Slower Vs. Faster.
- Used for both OLAP and OLTP.

Cons:

- Partioning existing tables vcan be a hasle.
- Some constraints cannot be set e.g. the Primary Key Constraints.

**Sharding**
- When Horizontal partitioning is applied to spread a table over several machines, it is called SHARDING.


**Exercise: Creating vertical partitions**
In the video, you learned about vertical partitioning and saw an example.

For vertical partitioning, there is no specific syntax in PostgreSQL. You have to create a new table with particular columns and copy the data there. 
Afterward, you can drop the columns you want in the separate partition. If you need to access the full table, you can do so by using a JOIN clause.

In this exercise and the next one, you'll be working with the example database called pagila. It's a database that is often used to showcase PostgreSQL features. The database contains several tables. We'll be working with the film table. In this exercise, we'll use the following columns:

`film_id`: the unique identifier of the film
`long_description`: a lengthy description of the film

Instructions 1/2

- Create a new table `film_descriptions` containing 2 fields: `film_id`, which is of type `INT`, and `long_description`, which is of type `TEXT`.
- Occupy the new table with values from the `film` table.

```sql
-- Create a new table called film_descriptions
CREATE TABLE film_descriptions (
    film_id INT,
    long_description TEXT
);

-- Copy the descriptions from the film table
INSERT INTO film_descriptions
SELECT film_id, long_description FROM film;
```

Instructions 2/2

Drop the field long_description from the film table.
Join the two resulting tables to view the original table.


```sql
-- Create a new table called film_descriptions
CREATE TABLE film_descriptions (
    film_id INT,
    long_description TEXT
);

-- Copy the descriptions from the film table
INSERT INTO film_descriptions
SELECT film_id, long_description FROM film;
    
-- Drop the descriptions from the original table
ALTER TABLE film DROP COLUMN long_description;

-- Join to view the original table
SELECT * FROM film 
JOIN film_descriptions USING(film_id);
```


**Exercise: Creating horizontal partitions**
In the video, you also learned about horizontal partitioning.

The example of horizontal partitioning showed the syntax necessary to create horizontal partitions in PostgreSQL. If you need a reminder, you can have a look at the slides.

In this exercise, however, you'll be using a list partition instead of a range partition. For list partitions, you form partitions by checking whether the partition key is in a list of values or not.

To do this, we partition by `LIST` instead of `RANGE`. When creating the partitions, you should check if the values are `IN` a list of values.

We'll be using the following columns in this exercise:

`film_id`: the unique identifier of the film
`title`: the title of the film
`release_year`: the year it's released

Instructions 1/3

Create the table `film_partitioned`, partitioned on the field `release_year`.


```sql
-- Create a new table called film_partitioned
CREATE TABLE film_partitioned (
  film_id INT,
  title TEXT NOT NULL,
  release_year TEXT
)
PARTITION BY RANGE (release_year);
```
Instructions 2/3

Create three partitions: one for each release year: 2017, 2018, and 2019. Call the partition for 2019 film_2019, etc.

```sql
-- Create a new table called film_partitioned
CREATE TABLE film_partitioned (
  film_id INT,
  title TEXT NOT NULL,
  release_year TEXT
)
PARTITION BY LIST (release_year);

-- Create the partitions for 2019, 2018, and 2017
CREATE TABLE film_2019
	PARTITION OF film_partitioned FOR VALUES IN ('2019');
    
CREATE TABLE film_2018
	PARTITION OF film_partitioned FOR VALUES IN ('2018');
    
CREATE TABLE film_2017
	PARTITION OF film_partitioned FOR VALUES IN ('2017');
```

Instructions 3/3

Occupy the new table, film_partitioned, with the three fields required from the film table.

```sql
-- Create a new table called film_partitioned
CREATE TABLE film_partitioned (
  film_id INT,
  title TEXT NOT NULL,
  release_year TEXT
)
PARTITION BY LIST (release_year);

-- Create the partitions for 2019, 2018, and 2017
CREATE TABLE film_2019
	PARTITION OF film_partitioned FOR VALUES IN ('2019');

CREATE TABLE film_2018
	PARTITION OF film_partitioned FOR VALUES IN ('2018');

CREATE TABLE film_2017
	PARTITION OF film_partitioned FOR VALUES IN ('2017');

-- Insert the data into film_partitioned
INSERT INTO film_partitioned
SELECT film_id, title, release_year FROM film;

-- View film_partitioned
SELECT * FROM film_partitioned;
```

#### Data Integration

Data integration combines data from different sources, formats, technologies to provide users with a translated and unified view of that data

#### Picking a Database Management System (DBMS)

- This is responsible for the creation and maintainance of databases. This manages three important aspects.
	The Data
	The database schema
	Database engine.
- DBMS serves as an interface between the database and the end users.

Choice of DBMS is informed by the choice of database needed which depends largely on the kind of data you have and how you intend to use it.
There are two types of DBMSs;
- SQL DBMSs: Relational Database e.g. SQL Server, PostGRESQL, ORACLE SQL 
- NoSQL DBMSs: Non rlational. Document structured rather than table scheme. Offered more flexibility.
	Types: Key Value Store, document store, columnar database and graph database.

Key Value database stores a combination ods keys and values
- Key: Unique Identifier to retrieve a value.
- Value: Anything.
- Eaxmple - REDIS
Document store: Example- MongoDB
Columnar Store: Used for big data analytics where speed is important. An example is Cassandra.

The choice of database depends on the business need.

- If the application need fix structure and does not need frequent modifications, SQL DBMS is preferable.
- If you have applications where data is changing frequently and growing rapidly, like in Big Data analytics, then NoSQL is best option.


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



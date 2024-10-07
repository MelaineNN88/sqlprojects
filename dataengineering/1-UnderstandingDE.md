# Understanding Data Engineering

## Chapter 1: What is data Engineering

### _Data Engineering and Big Data_

- There are four(4) general steps through which data flows withing an organization (**Data Workflows**)
1. First collection and storage
2. Cleaning and converting in an organized format (Data preparation).
3. Data Exploration and visualization.
4. Experimenting and Predicting.

Data engineers are responsible for the first step of the process. Ingesting collected data and stroring it. They lay the groundwork for data analyst, scientist and ML engineers.

In essence, data engineers' job is to;

- Deliver the right data
- In the correct form
- to the right people.
- As efficiently as possible.

Data Engineers;

- Ingest data from different sources
- Optimize the databases for analysis and 
- Remove corrupted data.
- They develop, construct, test and maintain data architechture such as databases and large scale processing systems.

The demand for big data has increased the demain for big data.

Big data is xterized by 5 Vs

- *Volume* (How much)
- *Variety* (What kind)
- *Velocity* (how fast the data is collected and processed).
- *Veracity* (How accurate the data is)
- *Value* (How useful the data is)

Data engineers need to take into conideration all of these.

### _Data Engineers Vs. Data Scientists_

While data engineers work on the first part of the data workflow, the data scientist work on  the remaining 3;
- Cleaning and formatting
- Exploration & Vizualization
- Experimenting and predicting

Data engineers lay the groundwork that make the work of Data Scientist is easy.

**Data Engineers**			**Data Scientists**
- Ingest and store data			- Exploit data
- Set up databases			- Access databases
- Build data pipelines			- Use pipeline outputs
- Strong software experts		- Strong analytical skills

### _The data pipeline_

Data engineers ingest, process and store data. In order to do that, data pipelines are needed to  automate the flow from one step to the other in oder to provide up-to-date, accurate and relevant data.
This isn't a simple task, reason why data engineers are so important.

Data pipelines ensure an efficient flow of data;  In essence, they automate;
- Extraction
- Transforming
- Combing
- Validating and loading data to reduce human intervention and errors and reducing the time it takes for data to flow through the organization.

** ETL and data pipelines**

*ETL*								*Data Pipelines*
- ETL is a popular framework for designing data pipelines	- Move data from one system to another
- **Extract** data						- May follow ETL
- **Transform** the extracted data.				- Data may not be transformed.
- **Load** transformed data to another database			- Data may be direcctly loaded into applications


## Chapter 2: Stroring data

### _Data Structures


**Structured data** is;
- Easy to search and organize.
- Consistent model, rows and columns.
- Defined data types (text or decimal)
- Can be grouped to form relations.
- Stored in relational databases.
- About 20% of data is structured.
- It is created and queried using SQL which stands for Structured Query Language
See example of structured data below;

[[!Structured Data](structuredData.png)]

**Semi-Structured data** is smilar to stuctured data but holds more freedom;
- It is relatively easy to search and organize.
- Consistent model; less rigid implementation. Different observations have different sizes.
- Different types.
- Can be grouped, but needs more work.
- Stored in NoSQL databases or JSON, XML, or YAML
See example of semi-structured data below

[[!Semi Structured data](semistructuredData.png)]

**Unstructured data** is data that 
- does not follow a model hence cannot be contained in rows and columns.
- Difficult to search and organize
- Usually text, sound, pictured and videos.
- Usually stored in data lakes, can appear in data warehouses and databases.
- MOst of the data around us is unstructured.
- It could be extremely valuable, H

### _SQL Databases_

SQL

- It stands for Structured Query Language.
- It is the industry standard for Relational Database Management System (RDBMS)
- It allows you to access many records and group filter or aggregate them.
- It is close to English, making it easy to understand.
- Data engineers use SQL to create and maintain databses.
- Data scientist use SQL to query databses.

e.g. Data Engineers use SQL to Create Tables

```sql
CREATE TABLE employees(
	employee_ID INT,
	first_name VARCHAR(255),
	last_name VARCHAR(255),
	role VARCHAR(255),
	team VARCHAR(255),
	full_time BOOLEAN,
	office VARCHAR(255)
);
```

Data Scientist will then use SQL to query the table
e.g.

```sql
SELECT first_name, last_name
	FROM employees
WHERE role LIKE '%Data%';
```
**Database Schema**
- This shows how the tables in a database relate to each other See diagram below.
[[!Schema](schema.png)]

There are several implementations of SQL
- SQL Lite
- MySQL
- PostgreSQL
- Oracle SQL
- SQl Server

### _Data Warehouses and Data Lakes_

*Data Lake* 					*Data Warehouse*
- Stores all the raw data			- Stores specific data and their specific use
- Can be petabyte (1 million GB)		- Relatively small.
- Stores all data stuctures			- Stores mainly structured data.
- Cost effective				- More costly to update.
- Dfficult to analyse				- optimized for analysis.
- Requires an up-to-date data catalog.		- Also used by data analysts and business analysts.
- Used by data scientists.			- Ad-hoc, read-only queries.
- Big data, real time anallytics.

A **Data Catalog** is a source of truth that compensates for the lack of structure in a data lake. It keeps track of 
- Where the data comes from.
- Where it is used.
- Who owns the data.
- How often the data is updated.
- Good practice in terms of data governance.
- ensures reproducibility.
- No catalog --> Data swamp
- It is good practice for any data storage soluttion. (** Reliability, Autonomy, scalability, speed**)

**Database**
- It is a general term.
- Loosely defined as organized data stored and accessed on a computer.
**Datawarehouse** is a type of database.

## Chapter 3: Moving and Procssing data

### _Processing Data_

- Data processing consist in coonverting raw data into meaningful information.

Why do we need to processing data?

- To remove unwanted data.
- Optimize memory, process and network cost.
- Convert data from one form to another.
- Organize data to make it easy for analysis.
- To fit a certain schema.
- To increase productivity.

How do data engineers process data?

- Data Manipulation, cleaning and tidying tasks
	That can be automated and 
	That always need to be done
- Store data in a sanely structured format.
- Create table views on top of the data base tables.
- Optimize the performance of databases.

### _Scheduling data_

- Scheduling is the glue of the data engineering system.

Another thing that matters is how data is ingested;

*Batches and Streams*
- Batches
	Group records at intervals
	Often cheaper.
- Streams
	Send individual records right away.

### _Parallel computing_
- Consult lecture notes and domore research.

### _Cloud computing_


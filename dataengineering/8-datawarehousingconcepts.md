# Data Warehousing Concepts

## _Ch1: Data Warehouse Basics_

_What is a data warehouse?_

- A data warehouse is a computer system designed to store and analyze large amount of data for an organization.
- It gathers data from different areas of an org (legal, engineering, sales, etc), integrates the data and stores the data and makes it available for analysis.

_Why is it valuable?_

- To support biz interlligence activities.
- Everyone needs the data warehouse as input for their analysis.

In essence, a data warehouse provides a repository for clean and organized data  for the organization. It soes this by gathering data from different sections of the org, integrating
the data, storing it and making it available for integration.

### _Data Warehouse and Data Lakes: The difference_

_Databases_: Databases use tables to store information in a structured way with rows and columns. Databases are used to store different transactions that occur with an org. In this scenario, these are transactional databases.
_Data Warehouse_: These gather data from dt areas in an org, integrate it and make it available for analysis. This implies there are many different sources of data in a data warehouse including multiple databases or even
non databases such as log files.All data is collected, transformed if needed and then integrated into a structured format into the data warehouse in an ETL process.

It is worth noting that data is stored in tables with rows and colummns in data warehouses.
This structure makes it difficult to update bc of up-stream and down-stream effects.

_Data Mart_: Data Mart is like a data warehouse but for the fact that it holds data only for one specific issue. E.g, data warehouse can hold data from different departments but data marts can only hold data for one
departmetn such as e.g., finance.
- Data marts have only a few input sources but data warehouse have many.
- Data marts are typically a subset of a data warehouse.
- Data mart is typically <100GB. Less than data warehouses.

_Data Lakes_: Similar to warehouses are built as central store of data.
- They have both structured and unstructured data.
- It is easier to make changes to data lakes compared to data warehouses, given the flexibility to hold unstructured data.

[[!Summary](summary_dldwdm.png)]

### _How data warehouses support organizational analysis_

_Planning_
- Business requirements: Understand the org needs. Who and how will the data be used.
- Data modeling: How team transforms data from input sources and integrate it into the data warehouse.

_Implementation_

- Build the data warehouse.
- Desing the ETL process. Build data pipelines that extract, transform and load data into the data warehouse.

_Support/Maintenance_
- BI Application development.
- Set up BI tools.
- Testing and deployment.

[[!Matrix](personamatrix.png)]


## _Ch2: Warehouse Architectures and Properties_

### _Different layers of a data warehouse_

Layer overview 

_Data Source_
- The first layer contains the data sources of the data warehouse.

_Data Staging_
- The data is extracted, transformed and loaded. The ETL process stages the data or temporarily puts it in tables so that it is ready for use.
	storage: Storage in warehouses.
_Data Presentation_
- The data is made available to the end user in the data presentation layer. BI and other analysis tools connect to the warehouse and allow end users to interact with the data.


[[!Layers of Data warehousing](layersofwarehousing.png)]


### _The Presentation Layer_

Users interact with the data in the presentation layer.

_Presentation layer groups_

- Automated reporting / dashboarding tools.
- BI/data analytics 
- Direct queries.

### _Data Warehouse Architectures_

_Inmon-top-down_
- Must decide all definitions, cleaning and business rules before it enters the warehouse.
- It recommends data in a normalized form in the warehouse.

_Kimball-bottom-up_
- Once data has been brought in, it is denormalized into a star schema.
- Focus here is getting from data to reporting as fast as possible. This is forcussing on departmental data mart.
- Various attributes connect the data mart to to the warehouse.
- Here the mart is created first before the data warehouse.

### _OLAP and OLTP_

_OLAP Systems_
- OLAP stands for Online Analytical Processing.
- It is a tool used for performaing multi-dimensional analysis at high speeds on large volumes of data from a data warehouse, data mart or any other data storage system.
- OLAPs system takes stored data in rows and columns (two dimensions), and organizes them into multiple dimensions for fact processing. This is referred to as slicing and dicing.

OLAP Data Cube; - At the core of the OLAP system is the OLAP Data cube.
- It has faster data processing speed than a traditional relational databases.

_OLPT_
- OLTP stands for online Transaction Systems.
- Designed for processing simple database queries.
- This processing simple queries like `INSERT`ing, `UPDATE`ing, and `DELETE`ing rows.
- Queries for OLTP affect few rows in the database.

## _Ch3: Data Warehouse Data Modeling_

### _Data Warehouse Data Modeling_








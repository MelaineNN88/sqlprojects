# _Ch1: Snowflake SQL and Key Concepts_

## _Snowflake SQL_

**Connecting to Snowflake**
- There are different ways to connect to snowflake. One of the most user-friendly options is the Web interface called *Snowsight*.
- For those looking to execute SQL queries, Snowflake provides *Worksheets*, an interface for creating and running SQL queries.

- Worksheets can be created which contain the Query, results and execution details such as query duration etc.
- Workbooks can also be created. These can be used to run `SLQ` and `Python` code. A powerful combination for tasks such as creating and maintaining data pipelines.

Beyond its web interface, Snowflake can be used via *Drivers*, which allow communication between its databases and external applications and tools.

Two common drivers include;

1. ODBC (Open Database Connectivity)
2. JDBC (Java Database Connectivity)

Additionally, there are specialized connectors like Python, Spark and more.

The Snowflake CLI (Command Line Interface) is another way to directly connect to snowflake account using the command line.
For the CLI, Snowflake offers versions for Linux, windows, and Mac.

Regardless of how we connect to snowflake, we'd mostly use the Snowflake SQL. 

Snowflake SQL is another flavour of SQL similar to Postgresql, T-SQL, MySQL.

These have differences and similarities in terms of data types, functions, and general syntax.

THe majority of Snowflake SQL and PostgreSQL are the Same like

[[!Similar syntax](similar syntax.png)]

**Exercise**: Querying using Snowflake SQL
You are consulting as a Data Engineer at Pissa, an expanding pizza delivery company. Their database has four tables: pizzas, pizza_types, orders, and order_details. You can view each table from the SQL console:

SQL console

As part of their transition from PostgreSQL to Snowflake, you're now examining the different columns in the pizzas table. Are you ready to refresh your SQL skills?

*Instructions*
Select pizza_type_id, pizza_size, and price from the pizzas table.

```sql
-- Select pizza_type_id, pizza_size, and price from pizzas table
SELECT pizza_type_id,
	pizza_size,
    price
FROM pizzas;
```

*Exercise*
Filtering in Snowflake SQL
As part of Pissa's migration from PostgreSQL to Snowflake, ensuring data integrity and correctness of the data after migration is crucial.

You're specifically working with the pizza_type table, which contains the following columns: pizza_type_id, name, category, and ingredients.

Let's apply your SQL skills to Snowflake!

*Instructions 1/2*
Count the total number of rows in the pizza_type table.


```sql
-- Count all pizza entries
SELECT COUNT(*) AS count_all_pizzas
FROM pizza_type;
```
*Instructions 2/2*
Filter the query to count only the pizzas categorized as 'Classic'.
```sql
-- Count all pizza entries
SELECT COUNT(*) AS count_all_pizzas
FROM pizza_type
-- Apply filter on category for Classic pizza types
WHERE category = 'Classic';
```



# Introduction to Relational Databases in SQL

## Chapter 1: Your First Database

### _Introduction to relational databases_
- In this course, we'd use data that comes from the investigation of university professors who work in different companies.
- We'd create relational databases from scratch.
- We'd llearn three main concepts;
	- Constraints
	- Keys
	- referential identity.
have a look at the following table;
```sql
SELECT table_schema, table_name
FROm information_schema.tables;
```
You can also hae a look at the column names of a certain table. 
Once you know the name of a table, you can query its columns by accessing the "columns" table.
```sql
SELECT table_name, column_name, data_type
FROM information_schema.columns
WHERE table_name ='pg_config';
```

**Excercise: Query information_schema with SELECT**

`information_schema` is a meta-database that holds information about your current database. 
`information_schema`	 has multiple tables you can query with the known `SELECT * FROM` syntax:

`tables`: information about all tables in your current database
`columns`: information about all columns in all of the tables in your current database
…
In this exercise, you'll only need information from the `'public'` schema, which is specified as the column `table_schema` of the `tables` 
and `columns` tables. The `'public'` schema holds information about user-defined tables and databases. 
The other types of `table_schema` hold system information – for this course, you're only interested in user-defined stuff.

*Instructions*
1. Get information on all table names in the current database, while limiting your query to the `'public'` `table_schema`.

```sql
-- Query the right table in information_schema
SELECT table_name 
FROM information_schema.tables
-- Specify the correct table_schema value
WHERE table_schema = 'public';
```
2. Now have a look at the columns in `university_professors` by selecting all entries in `information_schema.columns` that correspond to that table.
```sql
-- Query the right table in information_schema to get columns
SELECT column_name, data_type 
FROM information_schema.columns 
WHERE table_name = 'university_professors' AND table_schema = 'public';
```
### _Tables: At the core of every database_

- An entity relationship model. 
- The squares are called **entity types** while circles denote **entity attributes** or colunms.
- Click to see the graphical representation of [[!Entity Relationship table](entityrelationship.png)]


**Exercise: CREATE your first few TABLEs**
You'll now start implementing a better database model. For this, you'll create tables for the `professors` and `universities` entity types. The other tables will be created for you.

The syntax for creating simple tables is as follows:

```sql
CREATE TABLE table_name (
 column_a data_type,
 column_b data_type,
 column_c data_type
);
```
Attention: Table and columns names, as well as data types, don't need to be surrounded by quotation marks.

*Instructions*
1. Create a table professors with two text columns: firstname and lastname.
```sql
-- Create a table for the professors entity type
CREATE TABLE professors (
 firstname text,
 lastname text
);

-- Print the contents of this table
SELECT * 
FROM professors
```
2. Create a table universities with three text columns: `university_shortname`, `university`, and `university_city`.

```sql
-- Create a table for the universities entity type
CREATE TABLE universities(
    university_shortname text,
    university text,
    university_city text
);

-- Print the contents of this table
SELECT * 
FROM universities
```

**Exercise: ADD a COLUMN with ALTER TABLE**
Oops! We forgot to add the `university_shortname` column to the `professors` table. You've probably already noticed:
[[!professors](professors.png)]
In chapter 4 of this course, you'll need this column for connecting the professors table with the universities table.

However, adding columns to existing tables is easy, especially if they're still empty.

To add columns you can use the following SQL query:

```sql
ALTER TABLE table_name
ADD COLUMN column_name data_type;
```
*Instructions*
Alter professors to add the text column university_shortname.
```sql
-- Add the university_shortname column
ALTER TABLE professors
ADD COLUMN university_shortname text;

-- Print the contents of this table
SELECT * 
FROM professors
```
**Summary of lessons 1 & 2**
[[!Summary lessons 1&2](Review_lessons1and2.png)]

### _Chapter 1: Update your database as the structure changes_

One of the main reasons of splitting data into different tables or as in this case, splitting the university professor's table into several different 
entities is to reduce redundancy.

E.g, when you count the observations in the university_professor tables;

```sql
SELECT COUNT(*)
FROM university_professors;
```
the output is 1377 but when we cout DISTINCT professors;

```sql
SELECT COOUNT(DISTINCT organization)
FROM university_professors;
```
the output is 1287

To copy data from one table to a new one, we can use the 

```SQL
INSERT INTO name_of_table
SELECT DISTINCT var1, var2
FROM nam_of_source_table;
```
in our case,

```sql
INSERT INTO employers
SELECT DISTINCT organizations, organization_sector
FROM university_professors;
```

The normal use case for `INSERT INTO` is,

```sql
INSERT INTO table_name(column_a, column_b)
Values("value_a","value_b")
```

In case there are mistakes in the column name; e.g. the Affiliations table was created as such with the following code
```sql
CREATE TABLE affiliations (
  firstname text,
  lastname text,
  university_shortname text,
  function text,
  organisation text
);
```

We can see that organization is spelled with s instead of the american styled z. We have to rename the column.

```sql
ALTER TABBLE affiliations
RENAME COLUMN organisation TO organization;
```

The use case for this RENAME is;

```sql
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name
```

In addition the university_shortname may not be needed, so that may be dropped;

```sql
ALTER TABLE table_name
DROP column column_name
```

**Exercise:RENAME and DROP COLUMNs in affiliations**
As mentioned in the video, the still empty affiliations table has some flaws. In this exercise, you'll correct them as outlined in the video.

You'll use the following queries:

- To rename columns:
```sql
ALTER TABLE table_name
RENAME COLUMN old_name TO new_name;
```
- To delete columns:
```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

*Instructions*
1. Rename the organisation column to organization in affiliations. 
```sql
-- Rename the organisation column
ALTER TABLE affiliations
RENAME COLUMN organisation TO organization;
```
2. Delete the university_shortname column in affiliations.
```sql
-- Delete the university_shortname column
ALTER TABLE affiliations
DROP COLUMN university_shortname;
```
**Exercise: Migrate data with INSERT INTO SELECT DISTINCT**
Now it's finally time to migrate the data into the new tables. You'll use the following pattern:
```sql
INSERT INTO ... 
SELECT DISTINCT ... 
FROM ...;
```
It can be broken up into two parts:

First part:
```sql
SELECT DISTINCT column_name1, column_name2, ... 
FROM table_a;
```
This selects all distinct values in table `table_a` – nothing new for you.

Second part:

`INSERT INTO table_b ...;`
Take this part and append it to the first, so it inserts all distinct rows from `table_a` into `table_b`.

One last thing: It is important that you run all of the code at the same time once you have filled out the blanks.

*Instructions*
1. Insert all `DISTINCT` professors from `university_professors` into `professors`	.
Print all the rows in `professors`.

```sql
-- Insert unique professors into the new table
INSERT INTO professors 
SELECT DISTINCT firstname, lastname, university_shortname 
FROM university_professors;

-- Doublecheck the contents of professors
SELECT * 
FROM professors;
```
2. Insert all `DISTINCT` affiliations into `affiliations` from `university_professors`.
```sql
-- Insert unique affiliations into the new table
INSERT INTO affiliations 
SELECT DISTINCT firstname, lastname, function, organization 
FROM university_professors;

-- Doublecheck the contents of affiliations
SELECT * 
FROM affiliations;
```
**Exercise:Delete tables with DROP TABLE**
Obviously, the `university_professors` table is now no longer needed and can safely be deleted.

For table deletion, you can use the simple command:

`DROP TABLE table_name;`
*Instructions*

Delete the university_professors table.
-- Delete the university_professors table
`DROP TABLE university_professors;`

### _Chapter 2; Enforce Data consistency with attribute constraints_

Apart from creating, updating and inserting data into tables, there are several other database features that can be used.
The idea usually is to push data in a particular structure and type while maintaining relationships and other rules. These rules are called integrity constraints.
There are three types of integrity constraints. The first are 

1. **Attribute constraints**: E.g. a certain attribute in a database could allow only for an integer to be stored in a column.
2. **Key Constraints**: Primary keys e.g. uniquely id each record or row.
3. **Referential integrity constraints**: They glue different database tables together.

##### _Why integrity constraints?_
- They give data a consitent form and makes the cleaning process easier.
- In essence, they help to solve a lot of data quality issues.
- Data quality is a business advantage and data science prerequisite.

##### _Data Types as attribute constraints_

Data types are attribute constrains that vcan be specified for each column of the table.

##### _Dealing with data types (casting)_

Data types restrict possible operations on the stored data. e.g, it is impossible to do a calcula,tion between an integer and a text column.
A text column "wind_speed" may store numbers, but postgresql can't use a text column to calculate, hence.
The solution to this is casting, which is using the `CAST` function. We cast the "wind_speed" into an integer before performing the calculation.


**Exercise:  Conforming with data types**
For demonstration purposes, I created a fictional database table that only holds three records. The columns have the data types `date`, `integer`, and `text`, respectively.
```sql
CREATE TABLE transactions (
 transaction_date date, 
 amount integer,
 fee text
);
```
Have a look at the contents of the `transactions` table.

The `transaction_date` accepts `date` values. According to the PostgreSQL documentation, it accepts values in the form of `YYYY-MM-DD`, `DD/MM/YY`, and so forth.

Both columns `amount` and `fee` appear to be numeric, however, the latter is modeled as `text` – which you will account for in the next exercise.

**Instructions**
Execute the given sample code.
As it doesn't work, have a look at the error message and correct the statement accordingly – then execute it again.


FROM
```sql
-- Let's add a record to the table
INSERT INTO transactions (transaction_date, amount, fee) 
VALUES ('2018-24-09', 5454, '30');

-- Doublecheck the contents
SELECT *
FROM transactions;
```

to
```sql
-- Let's add a record to the table
INSERT INTO transactions (transaction_date, amount, fee) 
VALUES ('2018-09-24', 5454, '30');

-- Doublecheck the contents
SELECT *
FROM transactions;
```

**Exercise: Type CASTs**
In the video, you saw that type casts are a possible solution for data type issues. If you know that a certain column stores numbers as text, you can cast the column to a numeric form, i.e. to `integer`.
```sql
SELECT CAST(some_column AS integer)
FROM table;
```
Now, the `some_column` column is temporarily represented as `integer` instead of `text`, meaning that you can perform numeric calculations on the column.

**Instructions**
Execute the given sample code.
As it doesn't work, add an `integer` type cast at the right place and execute it again.


FROM
```sql
-- Calculate the net amount as amount + fee
SELECT transaction_date, amount + fee AS net_amount 
FROM transactions;
```
to
```sql
-- Calculate the net amount as amount + fee
SELECT transaction_date, amount + CAST(fee AS integer) AS net_amount 
FROM transactions;
```

#### _Working with data types_

- Data types are attribute constraints enforced on single columns on a table.
- Data types define the domain of values in a column, i.e what form the values can take.
- They also define the range of operations that can be conducted on a column.
- They enfoce consitent storage of values.

[[!Most common data types](datatypes.png)] etc.

Altering data type after table creation is also a straightforward exercise.

```sql
ALTER TABLE students
ALTER COLUMN name
TYPE VARCHAR(128);
```

Sometimes it may be necessary to truncate columns or transform them in a new way so they fit a new data type. This can be achieved with the function `USING`.

e.g.

```sql
ALTER TABLE students
ALTER COLUMN average_grades
TYPE integer
```
--Turns 5.54 into 6, not 5, before the the type conversion
```sql
USING ROUND(average_grades);
```

**Exercise: Change types with ALTER COLUMN**
The syntax for changing the data type of a column is straightforward. The following code changes the data type of the `column_name` column in `table_name` to `varchar(10)`:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name
TYPE varchar(10)
```
Now it's time to start adding constraints to your database.

**Instructions 1/3**

1. Have a look at the distinct `university_shortname` values in the `professors` table and take note of the length of the strings.
```sql
-- Select the university_shortname column
SELECT DISTINCT(university_shortname) 
FROM professors;
```
2. Now specify a fixed-length character type with the correct length for university_shortname.
```sql
-- Specify the correct fixed-length character type
ALTER TABLE professors
ALTER COLUMN university_shortname
TYPE CHAR(3);
```

3. Change the type of the firstname column to varchar(64).
```sql
-- Change the type of firstname
ALTER TABLE professors
ALTER COLUMN firstname
TYPE VARCHAR(64);
```

**Exercise: Convert types USING a function**
If you don't want to reserve too much space for a certain `varchar` column, you can truncate the values before converting its type.

For this, you can use the following syntax:
```sql
ALTER TABLE table_name
ALTER COLUMN column_name
TYPE varchar(x)
USING SUBSTRING(column_name FROM 1 FOR x)
```
You should read it like this: Because you want to reserve only `x` characters for `column_name`, you have to retain a `SUBSTRING` of every value, i.e. the first `x` characters of it, 
and throw away the rest. This way, the values will fit the `varchar(x)` requirement.

**Instructions**

Run the sample code as is and take note of the error.
Now use `SUBSTRING()` to reduce `firstname` to 16 characters so its type can be altered to `varchar(16)`.


##### _The not-null and Unique Constraints_

- The not-null diallows anu null values on a given column
- This must hold true for the entire database.

What does null mean.

- Unknown or
- Does not exist
- Does not apply to the column.


[[!Not Null](settinganddroppingnotnull.png)]

The UNIQUE constrains on a column makes sure there are no duplicates in a column.
- It diallows for duplicate values.
- Must hold true for current state.
- Must hold true for any future state.

**Adding Unique constraints**

```sql
CREATE TABLE table_name (
columns_name UNIQUE
);
```

we can also alter a table and add a constraint.
```sql
ALTER TABLE table_name
ADD CONSTRAINT some_name UNIQUE (column_name)
;
```

**Exercise: Disallow NULL values with SET NOT NULL**
The `professors` table is almost ready now. However, it still allows for `NULLs` to be entered. Although some information might be missing about some professors, there's certainly columns that always need to be specified.

**Instructions**

1. Add a not-null constraint for the `firstname` column.

```sql
-- Disallow NULL values in firstname
ALTER TABLE professors 
ALTER COLUMN firstname SET NOT NULL;
```
2. Add a not-null constraint for the lastname column.

```sql
ALTER TABLE professors
ALTER COLUMN lastname SET NOT NULL;
```
**Exercise: Make your columns UNIQUE with ADD CONSTRAINT**
As seen in the video, you add the `UNIQUE` keyword after the `column_name` that should be unique. This, of course, only works for new tables:
```sql
CREATE TABLE table_name (
 column_name UNIQUE
);
```
If you want to add a unique constraint to an existing table, you do it like that:
```sql
ALTER TABLE table_name
ADD CONSTRAINT some_name UNIQUE(column_name);
```
Note that this is different from the `ALTER COLUMN` syntax for the not-null constraint. Also, you have to give the constraint a name `some_name`.

**Instructions 1/2**
1.  Add a unique constraint to the university_shortname column in universities. Give it the name university_shortname_unq.

```sql
-- Make universities.university_shortname unique
ALTER TABLE universities
ADD CONSTRAINT university_shortname_unq UNIQUE(university_shortname);
```
2. Add a unique constraint to the organization column in organizations. Give it the name organization_unq.
```sql
-- Make organizations.organization unique
ALTER TABLE organizations
ADD CONSTRAINT organization_unq UNIQUE(organization);
```

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
- They enfoce consistent storage of values.

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

The UNIQUE constraints on a column makes sure there are no duplicates in a column.
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

[[!Integrity constraints summary](Integrity constraints summary.png)]

### _Chapter 3: Uniquely identify records with key constraints_

#### _Keys and Super keys_

**What is a key**

Typically, a database table has an attribute or a combination of attributes whose values are unique across the entire table.
These attributes identify a record uniquely. Usually, a table only contains unique records meaning that the combination of all 
attributes is a key itself. This is however not called a key but a --Super Key-- because attributes from that record can be removed.
If all attributes have been remoived and the record is still uniquely identifiable, we call that a --Minimal Super Key-- This is the key we are talkking about.
- A key is always minimal.

What then is a key?

- Attribute(s) that Id records uniquely.
- As long as attributes from the combination can be removed, this is called a **SUper Key**
- If all atributes have been removed and the record is still uniquely identifiable, i.e, no more attributes can be removes, we call that *Minimal Super Key*

In essence, keys are minimal if no attributes can be removed without loosing the Uniqueness property.


**Exercise:Get to know SELECT COUNT DISTINCT**
Your database doesn't have any defined keys so far, and you don't know which columns or combinations of columns are suited as keys.

There's a simple way of finding out whether a certain column (or a combination) contains only unique values – and thus identifies the records in the table.

You already know the `SELECT DISTINCT` query from the first chapter. Now you just have to wrap everything within the `COUNT()` function and PostgreSQL will return the number of unique rows for the given columns:
```sql
SELECT COUNT(DISTINCT(column_a, column_b, ...))
FROM table;
```
**Instructions 1/2**

1. First, find out the number of rows in universities.
```sql
-- Count the number of rows in universities
SELECT COUNT(*) 
FROM universities;
```
2. Then, find out how many unique values there are in the university_city column.
```sql
-- Count the number of distinct values in the university_city column
SELECT COUNT(DISTINCT(university_city)) 
FROM universities;
```

**Exercise: Identify keys with SELECT COUNT DISTINCT**
There's a very basic way of finding out what qualifies for a key in an existing, populated table:

1. Count the distinct records for all possible combinations of columns. If the resulting number `x` equals the number of all rows in the table for a combination, you have discovered a superkey.

2. Then remove one column after another until you can no longer remove columns without seeing the number `x` decrease. If that is the case, you have discovered a (candidate) key.

The table `professors` has 551 rows. It has only one possible candidate key, which is a combination of two attributes. You might want to try different combinations using the "Run code" button. Once you have found the solution, you can submit your answer.

**Instructions**

Using the above steps, identify the candidate key by trying out different combination of columns.

```sql
-- Try out different combinations
SELECT COUNT(DISTINCT(firstname, lastname)) 
FROM professors;
```

[[!Keys and superkeys summary](keys and superkeys summary.png)]

#### _Primary keys_

Primary keys are one of the most important concepts in database design and almost every database table should have a primary key.

- One primary key per database table chosen from the candidate keys.
- They are unique idetifiers. Primary keys ought to be the final column that don't accept duplicate or null values
i.e, they are unique and jot null contraint.
- They are time invariant.

##### _Specifying primary keys_

These two tables are the same except for the fact that the latter has a primary key specified.

```sql
CREATE TABLE products (
    product_no integer UNIQUE NOT NULL,
    name text,
    price numeric
);

CREATE TABLE products (
    product_no integer PRIMARY KEY,
    name text,
    price numeric
);
```

Primary keys can also be specified as such:

```sql
CREATE TABLE example (
    a integer,
    b integer,
    c integer,
    PRIMARY KEY (a, c)
);
```

This can be used if you want to use a combination of two columns to form a primary key. Nevertheless, this is still one single primary key though formed by the combination of two columns.

Adding constraints to primary keys is still same as adding UNIQUE constraints to an existing table.

```sql
ALTER TABLE table_name
ADD CONSTRAINT some_name PRIMARY KEY (column_name);
```

**Exercise: ADD key CONSTRAINTs to the tables**

Two of the tables in your database already have well-suited candidate keys consisting of one column each: `organizations` and `universities` with the `organization` and `university_shortname` columns, respectively.

In this exercise, you'll rename these columns to `id` using the `RENAME COLUMN` command and then specify primary key constraints for them. This is as straightforward as adding unique constraints (see the last exercise of Chapter 2):

```sql
ALTER TABLE table_name
ADD CONSTRAINT some_name PRIMARY KEY (column_name)
```
Note that you can also specify more than one column in the brackets.

Instructions 1/2

1. Rename the `organization` column to `id` in `organizations`.
Make `id` a primary key and name it `organization_pk`.

```slq
-- Rename the organization column to id
ALTER TABLE organizations
RENAME COLUMN organization TO id;

-- Make id a primary key
ALTER TABLE organizations
ADD CONSTRAINT organization_pk PRIMARY KEY (id);
```
2. Rename the `university_shortname` column to `id` in `universities`.
Make `id` a primary key and name it `university_pk`.

```sql
-- Rename the university_shortname column to id
ALTER TABLE universities 
RENAME COLUMN university_shortname TO id;

-- Make id a primary key
ALTER TABLE universities
ADD CONSTRAINT university_pq PRIMARY KEY (id);
```
#### _Surrogate keys_

Surrogate keys are an artifical form of primary key.
They are not based on a column in the data but on a column that exist for the sake of creating a primary key.

- Primary keys should be built from as few columns as possible.
- They are time invariant.

In the table below, the `licence_no` is the prikmary key because it is obvious it can't change over time. However, the the following table where we have just the `make`, `model` and `color`
in which there is no outright primary key, a primary key has to be created with a combination of the `make` and `model`.

[[!Tables Extract](tables_extract.png)]


##### _Adding a surrogate key with serial data type_

You could add an id, the serial type and specify it as a primary key. Once this serial type is added, all the rows in the table will be numbered. So much so that whenever a recored is added to the 
table, it automatically gets a number that does not already exist on the table. (See code below) there are similar data types cin other database management systems like MySQL.

```sql
ALTER TABLE cars
ADD COLUMN id SERIAL PRIMARY KEY;
INSERT INTO cars
VALUES ('volkswagen', 'Blitz', 'Black');
```

##### _Another type of surrogate key_

Another strategy for creating a surrogate key is combining two existing columns into one

In this example, we alter the table and add a VARCHAR column. Then we UPDATE the column and set it to be a concatenation of the column a and b.
Then lastly, we turn the new column into a new primary key.
```sql
ALTER TABLE table_Name
ADD COLUMN column_c VARCHAR(256);

UPDATE TABLE table_name
SET column_c = CONCAT(column_a, Column_b);

ALTER TABLE table_name
ADD CONSTRAINT pk PRIMARY KEY (column_c);
```

**Exercise: Add a SERIAL surrogate key**
Since there's no single column candidate key in `professors` (only a composite key candidate consisting of `firstname`, `lastname`), you'll add a new column `id` to that table.

This column has a special data type `serial`, which turns the column into an auto-incrementing number. This means that, whenever you add a new professor to the table, it will automatically get an `id` that does not exist yet in the table: a perfect primary key!

**Instructions**

1. Add a new column `id` with data type `serial` to the `professors` table.

```sql
-- Add the new column to the table
ALTER TABLE professors 
ADD COLUMN id SERIAL;
```
2. Make `id` a primary key and name it `professors_pkey`.

```sql
-- Add the new column to the table
ALTER TABLE professors 
ADD COLUMN id serial;

-- Make id a primary key
ALTER TABLE professors 
ADD CONSTRAINT professors_pkey PRIMARY KEY (id);
```
3. Write a query that returns all the columns and 10 rows from professors.

```sql
-- Add the new column to the table
ALTER TABLE professors 
ADD COLUMN id serial;

-- Make id a primary key
ALTER TABLE professors 
ADD CONSTRAINT professors_pkey PRIMARY KEY (id);

-- Have a look at the first 10 rows of professors
SELECT *
FROM professors
LIMIT 10;
```


**Exercise: CONCATenate columns to a surrogate key**
Another strategy to add a surrogate key to an existing table is to concatenate existing columns with the `CONCAT()` function.

Let's think of the following example table:
```sql
CREATE TABLE cars (
 make varchar(64) NOT NULL,
 model varchar(64) NOT NULL,
 mpg integer NOT NULL
)
```
The table is populated with 10 rows of completely fictional data.

Unfortunately, the table doesn't have a primary key yet. None of the columns consists of only unique values, so some columns can be combined to form a key.

In the course of the following exercises, you will combine `make` and `model` into such a surrogate key.

**Instructions**
1. Count the number of distinct rows with a combination of the `make` and `model` columns.

```sql
-- Count the number of distinct rows with columns make, model
SELECT COUNT (DISTINCT(make, model))
FROM cars;
```
2. Add a new column `id` with the data type `varchar(128)`.
```sql
-- Count the number of distinct rows with columns make, model
SELECT COUNT(DISTINCT(make, model)) 
FROM cars;

-- Add the id column
ALTER TABLE cars
ADD COLUMN id varchar(128);
```
3. Concatenate `make` and `model` into `id` using an `UPDATE table_name SET column_name = ...` query and the `CONCAT()` function.
```sql
-- Count the number of distinct rows with columns make, model
SELECT COUNT(DISTINCT(make, model)) 
FROM cars;

-- Add the id column
ALTER TABLE cars
ADD COLUMN id varchar(128);

-- Update id with make + model
UPDATE cars
SET id = CONCAT(make, model);
```
4. Make 1id1 a primary key and name it `id_pk`

```sql
-- Count the number of distinct rows with columns make, model
SELECT COUNT(DISTINCT(make, model)) 
FROM cars;

-- Add the id column
ALTER TABLE cars
ADD COLUMN id varchar(128);

-- Update id with make + model
UPDATE cars
SET id = CONCAT(make, model);

-- Make id a primary key
ALTER TABLE cars
ADD CONSTRAINT id_pk PRIMARY KEY(id);

-- Have a look at the table
SELECT * FROM cars;
```

**Exercise: Test your knowledge before advancing**
Before you move on to the next chapter, let's quickly review what you've learned so far about attributes and key constraints. If you're unsure about the answer, please quickly review chapters 2 and 3, respectively.

Let's think of an entity type "student". A student has:

a *last name* consisting of up to 128 characters (required),
a unique *social security number*, consisting only of integers, that should serve as a key,
a *phone number* of fixed length 12, consisting of numbers and characters (but some students don't have one).

**Instructions**

- Given the above description of a student entity, create a table `students` with the correct column types.
- Add a `PRIMARY KEY` for the social security number `ssn`.
Note that there is no formal length requirement for the integer column. The application would have to make sure it's a correct SSN!

```sql
-- Create the table
CREATE TABLE students (
  last_name varchar(128) NOT NULL,
  ssn integer PRIMARY KEY,
  phone_no CHAR(12)
);
```
[[!Summary Primary keys](summary primary keys.png)]


### _Chapter 4: Glue together tables with foreign keys_

#### _Model1:N relationships with foreign keys_

 
[[!Entity relationship diag](ER Diagram.png)]

In the diagram above, the small numbers represent the cardinality of the relationship.

- Professors work for one university while universities can have any number of professors working for it. Even zero (0).

##### _Implementing Relationships with foreign keys_

Such relationships as those described above between professors and universities are implemented with foreign keys.
- Foreing keys (FK) point to the primary keys (PK) of another table.The are desifnated columns that poin t to a primary key of another table.
Some restrictions however exist: 

- The domain and datatype must be the same as the one of the PK.
- Secondly, Each value of the FK must exist in the PK table (FK or referntial integrity).
- A FK is not an actually keys. This is because duplicates and null values are allowed.

##### _Specifying FK_

Foreign keys can be specified just as PK can be.

e.g.

We create a car manufacturers' table with a primary key "name", and then a cars' table with a primary key "model"

```sql
CREATE TABLE manufacturers (
  name varchar(255) PRIMARY KEY
);

INSERT INTO manufacturers
VALUES ('Ford'), ('VW'), ('GM');

CREATE TABLE cars (
  model varchar(255) PRIMARY KEY,
  manufacturer_name varchar(255) REFERENCES manufacturers (name)
);

INSERT INTO cars
VALUES ('Ranger', 'Ford'), ('Beetle', 'VW');
```

From the code, we specicfy the FK `manufacturer_name` which `REFERENCES` the `name` in `manufacturers` table.
So from nowq on, no new cars can be entered whose manufacturer is not already stroed in the manufacturers' table.

The syntax for adding FKs to existing tables is the same.

```sql
ALTER TABLE table_name
ADD CONSTRAINT a_fkey FOREIGN KEY ((b_id) REFERENCES b(id);
```

**Exercise: REFERENCE a table with a FOREIGN KEY**
In your database, you want the `professors` table to reference the `universities` table. You can do that by specifying a column in `professors` table that references a column in the `universities table`.

As just shown in the video, the syntax for that looks like this:
```sql
ALTER TABLE a 
ADD CONSTRAINT a_fkey FOREIGN KEY (b_id) REFERENCES b (id);
```
Table `a` should now refer to table `b`, via `b_id`, which points to `id`. `a_fkey` is, as usual, a constraint name you can choose on your own.

Pay attention to the naming convention employed here: Usually, a foreign key referencing another primary key with name `id` is named `x_id`, where `x` is the name of the referencing table in the singular form.

**Instructions**

1. Rename the `university_shortname` column to `university_id` in professors.

```sql
-- Rename the university_shortname column
ALTER TABLE professors
RENAME COLUMN university_shortname TO university_id;
```
2. Add a foreign key on `university_id` column in `professors` that references the `id` column in `universities`.
Name this foreign key `professors_fkey`.

```sql
-- Rename the university_shortname column
ALTER TABLE professors
RENAME COLUMN university_shortname TO university_id;

-- Add a foreign key on professors referencing universities
ALTER TABLE professors 
ADD CONSTRAINT professors_fkey FOREIGN KEY (university_id) REFERENCES universities (id);
```
**Exercise: Explore foreign key constraints**
Foreign key constraints help you to keep order in your database mini-world. In your database, for instance, only professors belonging to Swiss universities should be allowed, as only Swiss universities are part of the `universities` table.

The foreign key on `professors` referencing `universities` you just created thus makes sure that only existing universities can be specified when inserting new data. Let's test this!

**Instructions**
Run the sample code and have a look at the error message.
What's wrong? Correct the university_id so that it actually reflects where Albert Einstein wrote his dissertation and became a professor – at the University of Zurich (UZH)!

```sql
-- Try to insert a new professor
INSERT INTO professors (firstname, lastname, university_id)
VALUES ('Albert', 'Einstein', 'UZH');
```

**Exercise: JOIN tables linked by a foreign key**

Let's join these two tables to analyze the data further!

You might already know how SQL joins work from the Intro to SQL for Data Science course (last exercise) or from Joining Data in PostgreSQL.

Here's a quick recap on how joins generally work:
```sql
SELECT ...
FROM table_a
JOIN table_b
ON ...
WHERE ...
```
While foreign keys and primary keys are not strictly necessary for join queries, they greatly help by telling you what to expect. For instance, you can be sure that records referenced from table A will always be present in table B – so a join from table A will always find something in table B. If not, the foreign key constraint would be violated.


`JOIN` `professors` with `universities` on `professors.university_id` = `universities.id`, i.e., retain all records where the foreign key of professors is equal to the primary key of universities.
Filter for `university_city = 'Zurich'`.

```sql
-- Select all professors working for universities in the city of Zurich
SELECT professors.lastname, universities.id, universities.university_city
FROM professors
LEFT JOIN universities
ON professors.university_id = universities.id
WHERE universities.university_city = 'Zurich';
```

#### _Model more complex relationships_

The first example we impolemented was a 1 to N relationship.
Where, professors are only affiliated to one university, however a university can be affiliated to many different Universities.

WHat about the relationship between professors and organizations.

A professor can be affiliated to many different organizations with different functions. e.g, he can be president in one, and chairman of the other.
Also, an organization can be affiliated to more than one professor.

In this case, this is an N to M (N:M) relationship.

To implement this, for example to create the affiliations table in this lesson (see image) we can create a table as follows.

```sql
CREATE TABLE affiliations (
professor_id integer REFERENCES professors(id),
organization_id VARCHAR(256) REFERENCES organizations(id),
function VARCHAR(256)
);
```
It is woth noting that in this case there is no primary key.

**Exercise: Add foreign keys to the "affiliations" table**
At the moment, the affiliations table has the structure {firstname, lastname, function, organization}, as you can see in the preview at the bottom right. In the next three exercises, you're going to turn this table into the form {professor_id, organization_id, function}, with professor_id and organization_id being foreign keys that point to the respective tables.

You're going to transform the affiliations table in-place, i.e., without creating a temporary table to cache your intermediate results.

**Instructions**

1. Add a professor_id column with integer data type to affiliations, and declare it to be a foreign key that references the id column in professors.
```sql
-- Add a professor_id column
ALTER TABLE affiliations
ADD COLUMN professor_id integer REFERENCES professors (id);
```
2. Rename the organization column in affiliations to organization_id.
```sql
-- Add a professor_id column
ALTER TABLE affiliations
ADD COLUMN professor_id integer REFERENCES professors (id);

-- Rename the organization column to organization_id
ALTER TABLE affiliations
RENAME organization TO organization_id;
```
3. Add a foreign key constraint on organization_id so that it references the id column in organizations.

```sql
-- Add a professor_id column
ALTER TABLE affiliations
ADD COLUMN professor_id integer REFERENCES professors (id);

-- Rename the organization column to organization_id
ALTER TABLE affiliations
RENAME organization TO organization_id;

-- Add a foreign key on organization_id
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id);
```

**Exercise: Populate the "professor_id" column**

Now it's time to also populate professors_id. You'll take the ID directly from professors.

Here's a way to update columns of a table based on values in another table:
```sql
UPDATE table_a
SET column_to_update = table_b.column_to_update_from
FROM table_b
WHERE condition1 AND condition2 AND ...;
```
This query does the following:

1. For each row in table_a, find the corresponding row in table_b where condition1, condition2, etc., are met.
2. Set the value of column_to_update to the value of column_to_update_from (from that corresponding row).
The conditions usually compare other columns of both tables, e.g. table_a.some_column = table_b.some_column. Of course, this query only makes sense if there is only one matching row in table_b.

**Instructions**
1. First, have a look at the current state of affiliations by fetching 10 rows and all columns.

```sql
SELECT *
FROM affiliations
LIMIT 10
;
```
2. Update the professor_id column with the corresponding value of the id column in professors.
"Corresponding" means rows in professors where the firstname and lastname are identical to the ones in affiliations.

```sql
-- Set professor_id to professors.id where firstname, lastname correspond to rows in professors
UPDATE affiliations
SET professor_id = professors.id
FROM professors
WHERE affiliations.firstname = professors.firstname AND affiliations.lastname = professors.lastname;
```

3. Check out the first 10 rows and all columns of affiliations again. Have the professor_ids been correctly matched?

```sql
-- Update professor_id to professors.id where firstname, lastname correspond to rows in professors
UPDATE affiliations
SET professor_id = professors.id
FROM professors
WHERE affiliations.firstname = professors.firstname AND affiliations.lastname = professors.lastname;

-- Have a look at the 10 first rows of affiliations again
SELECT *
FROM affiliations
LIMIT 10;
```

**Exercise: Drop "firstname" and "lastname"**

The firstname and lastname columns of affiliations were used to establish a link to the professors table in the last exercise – so the appropriate professor IDs could be copied over. This only worked because there is exactly one corresponding professor for each row in affiliations. In other words: {firstname, lastname} is a candidate key of professors – a unique combination of columns.

It isn't one in affiliations though, because, as said in the video, professors can have more than one affiliation.

Because professors are referenced by professor_id now, the firstname and lastname columns are no longer needed, so it's time to drop them. After all, one of the goals of a database is to reduce redundancy where possible.

**Instructions**

Drop the firstname and lastname columns from the affiliations table.

```sql
ALTER TABLE affiliations
DROP COLUMN firstname;

ALTER TABLE affiliations
DROP COLUMN lastname;

```

[[!Summary Prev Lessons](prev lessons summary.png)]


#### _Referential Integrity_

THis is a very important concept in database systems.

- A record referencing another table must always refer to an existing record.
- It is constraint that alsways involves two tables.

It can be violated in two ways.

- If there are two tables A & B and a record in B that is referenced from A is deleted, then a reference is violated.
- If you attempt inserting a record in A which is not referenced in B then a reference is violated.
-- Foreign keys therefore prevent these violations.

##### _Dealing with violations_

- Flagging a violation is not the only option. If you throw in a FK, you can immediately tell what should happen if a reference is violated.
The `ON DELETE NO ACTION` is usually automatically appended to a FK. i.e, if you try to delete a record in table `B` which is referenced in table `A` the nsystem will throw an error.
There are more options.

The `CASCADE` option. This option will first delete the record in `B` and then automatically delete all referencing records in `A`, hence the deletion is cascaded.


```sql
CREATE TABLE a (
    id integer PRIMARY KEY,
    column_a varchar(64),
    ...,
    b_id integer REFERENCES b (id) ON DELETE NO ACTION
);

CREATE TABLE a (
    id integer PRIMARY KEY,
    column_a varchar(64),
    ...,
    b_id integer REFERENCES b (id) ON DELETE CASCADE
);
```

There are more options

`ON DELETE`...
- ... NO ACTION: Throw an error
- ... CASCADE: Delete all referencing records
- ... RESTRICT: Throw an error.
- ... SET NULL: set referncing columns to NULL.
- ...SET DEFAULT: Sets the referncing column to its default value. This only works if you have set the default value for the column.

**Exercise: Change the referential integrity behavior of a key**

So far, you implemented three foreign key constraints:

`professors.university_id` to `universities.id`
`affiliations.organization_id` to `organizations.id`
`affiliations.professor_id` to `professors.id`

These foreign keys currently have the behavior `ON DELETE NO ACTION`. Here, you're going to change that behavior for the column referencing `organizations` from `affiliations`. 
If an organization is deleted, all its affiliations (by any professor) should also be deleted.

Altering a key constraint doesn't work with `ALTER COLUMN`. Instead, you have to `DROP` *the key constraint* and then `ADD` *a new one* with a different `ON DELETE` behavior.

For deleting constraints, though, you need to know their name. This information is also stored in `information_schema`.

**Instructions**
1. Have a look at the existing foreign key constraints by querying table_constraints in information_schema.

```sql
-- Identify the correct constraint name
SELECT constraint_name, table_name, constraint_type
FROM information_schema.table_constraints
WHERE constraint_type = 'FOREIGN KEY';
```

2. Delete the `affiliations_organization_id_fkey` foreign key constraint in affiliations.
```sql
-- Drop the right foreign key constraint
ALTER TABLE affiliations
DROP CONSTRAINT affiliations_organization_id_fkey;
```
3. Add a new foreign key to `affiliations` that `CASCADE`s deletion if a referenced record is deleted from `organizations`. Name it `affiliations_organization_id_fkey`.

```sql
-- Add a new foreign key constraint from affiliations to organizations which cascades deletion
ALTER TABLE affiliations
ADD CONSTRAINT affiliations_organization_id_fkey FOREIGN KEY (organization_id) REFERENCES organizations (id) ON DELETE CASCADE;
```
4. Run the `DELETE` and `SELECT` queries to double check that the deletion cascade actually works.

```sql
-- Delete an organization 
DELETE FROM organizations 
WHERE id = 'CUREM';

-- Check that no more affiliations with this organization exist
SELECT * FROM affiliations
WHERE organization_id = 'CUREM';
```

**Exercise: Count affiliations per university**

Now that your data is ready for analysis, let's run some example SQL queries on the database. You'll now use already known concepts such as grouping by columns and joining tables.

In this exercise, you will find out which university has the most affiliations (through its professors). For that, you need both `affiliations` and `professors` tables, as the latter also holds the `university_id`.

As a quick repetition, remember that joins have the following structure:
```sql
SELECT table_a.column1, table_a.column2, table_b.column1, ... 
FROM table_a
JOIN table_b 
ON table_a.column = table_b.column
```
This results in a combination of `table_a` and `table_b`, but only with rows where `table_a.column` is equal to `table_b.column`.

**Instructions**

Count the number of total affiliations by university.
Sort the result by that count, in descending order.

```sql
-- Count the total number of affiliations per university
SELECT COUNT(*), professors.university_id 
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
-- Group by the university ids of professors
GROUP BY professors.university_id 
ORDER BY count DESC;
```
**Exercise: Join all the tables together**

In this last exercise, your task is to find the university city of the professor with the most affiliations in the sector "Media & communication".

For this,

you need to join all the tables,
group by some column,
and then use selection criteria to get only the rows in the correct sector.
Let's do this in three steps!

**Instructions**

1. Join all tables in the database (starting with affiliations, professors, organizations, and universities) and look at the result.

```sql
-- Join all tables
SELECT *
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id;
```

2. 

```sql

-- Group the table by organization sector, professor ID and university city
SELECT COUNT(*), organizations.organization_sector, 
professors.id, universities.university_city
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id
GROUP BY organizations.organization_sector, 
professors.id, universities.university_city;
```
3. Only retain rows with "Media & communication" as organization sector, and sort the table by count, in descending order.

```sql
-- Filter the table and sort it
SELECT COUNT(*), organizations.organization_sector, 
professors.id, universities.university_city
FROM affiliations
JOIN professors
ON affiliations.professor_id = professors.id
JOIN organizations
ON affiliations.organization_id = organizations.id
JOIN universities
ON professors.university_id = universities.id
WHERE organizations.organization_sector = 'Media & communication'
GROUP BY organizations.organization_sector, 
professors.id, universities.university_city
ORDER BY COUNT DESC;
```


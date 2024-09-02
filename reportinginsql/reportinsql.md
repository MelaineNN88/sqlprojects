# Reporting in SQL

## Chapter 1: Exploring the Olympics dataset.

### _Case Study_

Course decription:

This course is aims to strenghten SQL skills for reporting

- Reporting is the act of presenting data in any shape or form. Table, viz, or a number.

*Who uses reports?*
- It is used by a wide array of individuals in biz settings.
- eg, data scientist, a data viz specialist, or a software developer

Regardless of wh, reporting starts same way, gathering data.
Here we use postgresql

_Olympic case study_

Identifying *Base reports*

- A base report is an underlying report that sources a visualization.
- From a SQL point of view, you  frst of all want to create a base report before creating a visualization.

**Exercise: Identify base reports**

!whatisfalsehere identifyingbasereport.png

**Exercise 2: Building base reports**

!Buildingbasereports buildingbasereports.png

```sql
-- Query the sport and distinct number of athletes
SELECT
	sport, 
   COUNT (DISTINCT athlete_id) AS athletes
FROM summer_games
GROUP BY sport
-- Only include the 3 sports with the most athletes
ORDER BY athletes DESC
LIMIT 3;
```
!Athletesvseventsbysport athletesvseventsbysport.png

```sql
-- Query sport, events, and athletes from summer_games
SELECT 
	sport, 
    COUNT(DISTINCT event) AS events, 
    COUNT(DISTINCT athlete_id) AS athletes
FROM summer_games
GROUP BY sport;
```

###_The olympic dataset_

Before digging into a specific database, it is good to undertand the E.R diagram.

**Entity Relationship (E.R) diagram**
- An E.R diagram is a visual representation of a database structure. They show tables and fields and connects tables visually to show relationships. They are a great way quickly understand the structure of a database.
- Outside of giving a high level overview of the datles and fields, E.R diagrams are a good reference document when planning large queries.
- By highlighting relevant fields, it is then easy to plan what types of JOINS, UNIONS, or other logics may be performed.

!exampleERDiagram ERdiagram.png

**Exercise: age of oldest athlete by Region**

!OldestAtheletebyRegion oldestathletebyregion.png

```sql
-- Select the age of the oldest athlete for each region
SELECT 
	region, 
    MAX(age) AS age_of_oldest_athlete
FROM athletes as a
-- First JOIN statement
JOIN summer_games as s
ON a.id = s.athlete_id
-- Second JOIN statement
JOIN countries as c
ON s.country_id = c.id
GROUP BY c.region;
```
!numofeventsineachsport numofeventsineachsport.png

```sql
-- Select sport and events for summer sports
SELECT 
	sport, 
    COUNT(DISTINCT event) AS events
FROM summer_games
GROUP BY sport
UNION
-- Select sport and events for winter sports
SELECT 
	sport, 
    COUNT(DISTINCT event) AS events
FROM winter_games
GROUP BY sport
-- Show the most events at the top of the report
ORDER BY events DESC;
```

!Summaryofprevlearning summaryofprevlearning.png


###_Exploring our data_
In the last lesson we looked at ER diagrams and how they can hlep us with knowledge to understand the structure of the data.The relationships between tables and columns.
That however, is not the only thing that needs to be done. We need to explore our data in order to understand the data.

**Exploring with the Console**
- The simplest way to explore data is to look at the dataCamp console or in the sql client.
- This may answer some basic questions about the data, but it may not provide accurate information about the data as a whole.
- A more robust approach to explore data is to run simple queries.
- We can run aggregated queries using a `GROUP BY` to explore the data.
- Fied-level and table level (`COUNT(*)`) aggragation might also be a good approach.

These data exploration techniques can be used to validate queries.
- Validating a query is crucial to ensure the report is reliable.

**Exercise: Exploring Summer Games**
1. 
!ExploringSummerGames exploringsummergames1.png

```sql
-- Update the query to explore the bronze field
SELECT bronze
FROM summer_games;
```
2. The results do not provide much insight as we mostly see NULL. Add a `DISTINCT` to only show unique bronze values.

```sql
-- Update query to explore the unique bronze field values
SELECT DISTINCT bronze
FROM summer_games;
```
3. `GROUP BY` is an alternative to using `DISTINCT`. Remove the `DISTINCT` and add a `GROUP BY` statement. 

```sql
-- Recreate the query by using GROUP BY 
SELECT DISTINCT bronze
FROM summer_games
GROUP BY bronze;
```
4. Let's see how much of our dataset is NULL. Add a field that shows number of rows to your query.

```sql
-- Add the rows column to your query
SELECT 
	bronze, 
	COUNT(*) AS rows
FROM summer_games
GROUP BY bronze;
```

**Exercise: Validating our query**

####The same techniques we use to explore the data can be used to validate queries. By using the query as a subquery, you can run exploratory techniques to confirm the query results are as expected.

####In this exercise, you will create a query that shows Bronze Medals by Country and then validate it using the subquery technique.

####Feel free to reference the !E:RDiagram ERdiagram.png as needed.
1. Create a query that outputs total bronze medals from the summer_games table.

```sql
-- Pull total_bronze_medals from summer_games below
SELECT SUM(bronze) AS total_bronze_medals
FROM summer_games;
```

2. The value for total_bronze_medals is commented out for reference. Setup a query that shows bronze _medals for summer_games by country.

```sql
/* Pull total_bronze_medals from summer_games below
SELECT SUM(bronze) AS total_bronze_medals
FROM summer_games; 
>> OUTPUT = 141 total_bronze_medals */

-- Setup a query that shows bronze_medal by country
SELECT 
	country, 
    SUM(bronze) AS bronze_medals
FROM summer_games AS s
JOIN countries AS c
ON s.country_id = c.id
GROUP BY country;
```
3. Add parenthesis to your query you just created and alias as subquery.
Select the total number of bronze_medals and compare to the total bronze medals in summer_games.

```sql
/* Pull total_bronze_medals below
SELECT SUM(bronze) AS total_bronze_medals
FROM summer_games; 
>> OUTPUT = 141 total_bronze_medals */

-- Select the total bronze_medals from your query
SELECT country, bronze_medals
FROM 
-- Previous query is shown below.  Alias this AS subquery
  (SELECT 
      country, 
      SUM(bronze) AS bronze_medals
  FROM summer_games AS s
  JOIN countries AS c
  ON s.country_id = c.id
  GROUP BY country) as subquery
;
```

**Exercise: Report 1: Most Decorated Atletes**
[[!MostdecAthelets|Solid](mostdecoratedsummerathletes.png)]

Your job is to create the base report for this element. Base report details:

- Column 1 should be athlete_name.
- Column 2 should be gold_medals.
- The report should only include athletes with at least 3 medals.
- The report should be ordered by gold medals won, with the most medals at the top.

**Instructions**
- Select athlete_name and gold_medals by joining summer_games and athletes.
- Only include athlete_name with at least 3 gold medals.
- Sort the table so that the most gold_medals appears at the top.

```sql
-- Pull athlete_name and gold_medals for summer games
SELECT 
	name AS athlete_name, 
    SUM(gold) AS gold_medals
FROM summer_games AS s
JOIN athletes AS a
ON a.id = s.athlete_id
GROUP BY athlete_name
-- Filter for only athletes with 3 gold medals or more
HAVING SUM(gold) >= 3
-- Sort to show the most gold medals at the top
ORDER BY gold_medals DESC;
```

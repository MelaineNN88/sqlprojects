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

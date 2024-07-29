# Applying funtions for string data cleaning in SQL

**Exercise**
A service to provide parking violation recipients with a hard copy of the violation is being re-designed. For proper formatting of the output of the information on the report, some fields needs to be changed from the database representation. The changes are as follows:

For proper text alignment on the form, violation_location values must be 4 characters in length.
All P-U (pick-up truck) values in the vehicle_body_type column should use a general TRK value.
Only the first letter in each word in the street_name column should be capitalized.
The LPAD(), REPLACE(), and INITCAP() functions will be used to effect these changes.

```SELECT
  -- Add 0s to ensure violation_location is 4 characters in length
  LPAD(violation_location, 4, '0') AS violation_location,
  -- Replace 'P-U' with 'TRK' in vehicle_body_type column
  REPLACE(vehicle_body_type, 'P-U', 'TRK') AS vehicle_body_type,
  -- Ensure only first letter capitalized in street_name
  INITCAP(street_name) AS street_name
FROM
  parking_violation;
```

**Pattern Matching**

**Exercise**

# Classifying parking violations by time of day
There have been some concerns raised that parking violations are not being issued uniformly throughout the day. You have been tasked with associating parking violations with the time of day of issuance. You determine that the simplest approach to completing this task is to create a new column named morning. This field will be populated with (the integer) 1 if the violation was issued in the morning (between 12:00 AM and 11:59 AM), and, (the integer) 0, otherwise. The time of issuance is recorded in the violation_time column of the parking_violation table. This column consists of 4 digits followed by an A (for AM) or P (for PM).

In this exercise, you will populate the morning column by matching patterns for violation_times occurring in the morning.


- Use the regular expression pattern '\d\d\d\dA' in the sub-query to match violation_time values consisting of 4 consecutive digits (\d) followed by an uppercase A.
- Edit the CASE clause to populate the morning column with 1 (integer without quotes) when the regular expression is matched.
- Edit the CASE clause to populate the morning column with 0 (integer without quotes) when the regular expression is not matched.

```SELECT 
	summons_number, 
    CASE WHEN 
    	summons_number IN (
          SELECT 
  		summons_number 
  		 FROM 
  		parking_violation 
  		  WHERE 
            -- Match violation_time for morning values
  			violation_time SIMILAR TO '\d\d\d\dA'
    	)
        -- Value when pattern matched
        THEN 1 
        -- Value when pattern not matched
        ELSE 0 
    END AS morning 
FROM 
	parking_violation;
```
**Exercise**
# Masking identifying information with regular expressions
Regular expressions can also be used to replace patterns in strings using REGEXP_REPLACE(). The function is similar to the REPLACE() function. Its signature is REGEXP_REPLACE(source, pattern, replace, flags).

pattern is the string pattern to match in the source string.
replace is the replacement string to use in place of the pattern.
flags is an optional string used to control matching.
For example, REGEXP_REPLACE(xyz, '\d', '_', 'g') would replace any digit character (\d) in the column xyz with an underscore (_). The g ("global") flag ensures every match is replaced.

To protect parking violation recipients' privacy in a new web report, all letters in the plate_id column must be replaced with a dash (-) to mask the true license plate number.

Use REGEXP_REPLACE() to replace all uppercase letters (A to Z) in the plate_id column with a dash character (-) so that masked license plate numbers can be used in the report.

```SELECT 
	summons_number,
	-- Replace uppercase letters in plate_id with dash
	REGEXP_REPLACE(plate_id, '[A-Z]', '-', 'g') 
FROM 
	parking_violation;
```


# Matching similar strings

Matching inconsistent color names
From the sample of records in the parking_violation table, it is clear that the vehicle_color values are not consistent. For example, 'GRY', 'GRAY', and 'GREY' are all used to describe a gray vehicle. In order to consistently represent this color, it is beneficial to use a single value. Fortunately, the DIFFERENCE() function can be used to accomplish this goal.

In this exercise, you will use the DIFFERENCE() function to return records that contain a vehicle_color value that closely matches the string 'GRAY'. The fuzzystrmatch module has already been enabled for you.

**Exercise**
Use the DIFFERENCE() function to find parking_violation records having a vehicle_color with a Soundex code that matches the Soundex code for 'GRAY'. Recall that the DIFFERENCE() function accepts string values (not Soundex codes) as parameter arguments.

```SELECT
  summons_number,
  vehicle_color
FROM
  parking_violation
WHERE
  -- Match SOUNDEX codes of vehicle_color and 'GRAY'
  DIFFERENCE(vehicle_color, 'GRAY') = 4;
```

# Standardizing color names
In the previous exercise, the DIFFERENCE() function was used to identify colors that closely matched our desired representation of the color GRAY. However, this approach retained a number of records where the vehicle_color value may or may not be gray. Specifically, the string GR (green) has the same Soundex code as the string GRAY. Fortunately, records with these vehicle_color values can be excluded from the set of records that should be changed.

In this exercise, you will assign a consistent gray vehicle_color value by identifying similar strings that represent the same color. Again, the fuzzystrmatch module has already been installed for you.

Complete the SET clause to assign 'GRAY' as the vehicle_color for records with a vehicle_color value having a matching Soundex code to the Soundex code for 'GRAY'.
Update the WHERE clause of the subquery so that the summons_number values returned exclude summons_number values from records with 'GR' as the vehicle_color value.

```UPDATE 
	parking_violation
SET 
	-- Update vehicle_color to `GRAY`
	vehicle_color = 'GRAY'
WHERE 
	summons_number IN (
      SELECT
        summons_number
      FROM
        parking_violation
      WHERE
        DIFFERENCE(vehicle_color, 'GRAY') = 4 AND
        -- Filter out records that have GR as vehicle_color
        vehicle_color != 'GR'
);
```

# Standardizing multiple colors
After the success of standardizing the naming of GRAY-colored vehicles, you decide to extend this approach to additional colors. The primary colors RED, BLUE, and YELLOW will be used for extending the color name standardization approach. In this exercise, you will:

- Find vehicle_color values that are similar to RED, BLUE, or YELLOW.
- Handle both the ambiguous vehicle_color value BL and the incorrectly identified vehicle_color value BLA using pattern matching.
- Update the vehicle_color values with strong similarity to RED, BLUE, or YELLOW to the standard string values.

```UPDATE 
	parking_violation pv
SET 
	vehicle_color = CASE
      -- Complete conditions and results
      WHEN red = 4 THEN 'RED'
      WHEN blue = 4 THEN 'BLUE'
      WHEN yellow = 4 THEN 'YELLOW'
	END
FROM 
	red_blue_yellow rby
WHERE 
	rby.summons_number = pv.summons_number;

SELECT * FROM parking_violation LIMIT 10;
```

# Formatting text for colleagues
A website to monitor filming activity in New York City is being constructed based on film permit applications stored in film_permit. This website will include information such as an event_id, parking restrictions required for the filming (parking_held), and the purpose of the filming.

Your task is to deliver data to the web development team that will not require the team to perform further cleaning. event_id values will need to be padded with 0s in order to have a uniform length, capitalization for parking will need to be modified to only capitalize the initial letter of a word, and extra spaces from parking descriptions will need to be removed. The REGEXP_REPLACE() function (introduced in one of the previous exercises) will be used to clean the extra spaces.

Use the LPAD() function to complete the query so that each event_id is always 10 digits in length with preceding 0s added for any event_id less than 10 digits.

```SELECT 
	-- Add 0s to ensure each event_id is 10 digits in length
	LPAD(event_id, 10, '0') as event_id, 
    parking_held 
FROM 
    film_permit;
```
Complete the query to fix capitalization for parking_held column values so that only the first letter of each word is capitalized.

```SELECT 
	LPAD(event_id, 10, '0') as event_id, 
    -- Fix capitalization in parking_held column
    INITCAP(parking_held) as parking_held
FROM 
    film_permit;
```
Complete the query using the regular expression pattern ' +' so that one or more consecutive space characters in the parking_held column are replaced with a single space (' ').

```SELECT 
	LPAD(event_id, 10, '0') as event_id, 
    -- Replace consecutive spaces with a single space
    REGEXP_REPLACE(INITCAP(parking_held), ' +', ' ', 'g')  as parking_held
FROM 
    film_permit;
```

# Chapter 1 Summary

**Your recent learnings**
--When you left 2 days ago, you worked on Missing, Duplicate, and Invalid Data, chapter 2 of the course Cleaning Data in PostgreSQL Databases. Here is what you covered in your last lesson:
--You learned about the importance of data cleaning in PostgreSQL, focusing on string similarity and pattern matching techniques to identify and clean messy data. Here are the key points you covered:
--Soundex Algorithm: This algorithm encodes the sound of words into a 4-character code, allowing you to compare strings that sound similar but are spelled differently. For example, different spellings of "Fontainebleau" all produce the Soundex code F535.
--fuzzystrmatch Module: This PostgreSQL extension provides functions like SOUNDEX() and DIFFERENCE() for fuzzy string matching. You enabled it using the CREATE EXTENSION command.
--DIFFERENCE() Function: This function compares the Soundex codes of two strings and returns a value between 0 and 4, indicating how similar the strings sound. For example, DIFFERENCE('pair', 'pear') returns 4 because they sound identical.

--Practical Application: You applied these concepts to clean inconsistent data, such as standardizing vehicle color names in a parking_violation table.

```SELECT
  summons_number,
  vehicle_color
FROM
  parking_violation
WHERE
  DIFFERENCE(vehicle_color, 'GRAY') = 4;
```
--UPDATE Statement: You learned to unify disparate values using an UPDATE statement, ensuring consistency in your dataset.
By mastering these techniques, you can efficiently clean and standardize data in PostgreSQL, making it more reliable for analysis.

The goal of the next lesson is to learn how to use PostgreSQL functions to clean and format string data for better analysis.

!chapter1summary ch1summary.png



# Chapter 2

** Missing Data**
-- Missing data can have different representations.
-- It is common to use a special Null value (NULL) general
-- String columns may represent null values but also empty strings may represent null values.

**Causes of missing data**
- Human error
- Systematic issues
Missing data can be calssified in different types.

-- MCAR - Missing completely at random: No systematic relationship exist between missing data and other values
-- MAR - Missing at Random: There is a systematic relationship between missing data and other observed values.
-- MNAR - Missing Not at Random: Systematic relationship between missing data and unobserved values.

**Identifying Missing data**
-- SELECT statements can be used to ID missing values in a column of interest.
-- COUNT function can be used to quickly compute the value
e.g
```SELECT 
	COUNT(*)
    FROM 
	restaurant_inspection
    WHERE
	score IS NULL;
```

--- We can investigate relationships between missing values of one column and other variables by ounting columns of the one we want to establish the missing count and groupig aby the other.
e.g
```SELECT
	inspection_type,
	COUNT(*) as count
    FROM 
	restaurant_inspection
    WHERE 
	score IS NULL
    GROUP BY 
	inspection_type
    ORDER BY
	count DESC;
```
**Rectifying missing data**
---Locatate and add missinng values. Ths may however not be wothwhile, and may not be feasible.
---Provide a value (average, mean, etc)
---Exclude values.

**Replacing Missing values**
---Postgresql provides a function for replacing missing values
-- The COALESCE function.

```COALESCE (arg1, [arg2,...])
```
---In case we want to display  -1 instead of a null score for restaurant inspection results.
```SELECT
        name,
        COALESCE(score, -1),
        inspection_type
   FROM 
        restaurant_inspection;
```
**Exercise**

Quantifying completeness
The records for parking violations stored in the parking_violation table contain missing values for the vehicle_body_type column. Assume this data is missing completely at random (MCAR) due to human error. In an effort to make the data more complete, you have been tasked with filling in these values. You decide to quantify how many records are missing and perform an analysis for an appropriate fill-in value to replace the missing values.

How many parking_violation records have a NULL value for vehicle_body_type? Write and execute a SELECT query that computes this number.

```SELECT 
       COUNT(*)
FROM
       parking_violation
WHERE 
        vehicle_body_type IS NULL;
```

## Using a fill-in value
The sedan body type is the most frequently occurring vehicle_body_type in the sample parking violations. For this reason, you propose changing all NULL-valued vehicle_body_type records in the parking_violations table to SDN. Discussions with your team result in a decision to use a value other than SDN as a fill-in value. The body type can be determined by looking up the vehicle using its license plate number. A license plate number is present in most parking_violation records. Rather than using the most frequent value to replace NULL vehicle_body_type values, a placeholder value of Unknown will be used. The actual body type will be updated as license plate lookup data is gathered.

--- In this exercise, you will replace NULL vehicle_body_type values with the string Unknown.

--- Use COALESCE() to replace any vehicle_body_type that is NULL with the string value Unknown in the parking_violation table.
```UPDATE
  parking_violation
SET
  -- Replace NULL vehicle_body_type values with `Unknown`
  vehicle_body_type = COALESCE(vehicle_body_type, 'Unknown');

SELECT COUNT(*) FROM parking_violation WHERE vehicle_body_type = 'Unknown';
```

## Analyzing incomplete records
In an effort to reduce the number of missing vehicle_body_type values going forward, your team has decided to embark on a campaign to educate issuing agencies on the need for complete data. However, each campaign will be customized for individual agencies.
In this exercise, your goal is to use the current missing data values to prioritize these campaigns. You will write a query which outputs the issuing agencies along with the number of records attributable to that agency with a NULL vehicle_body_type. These records will be listed in descending order to determine the order in which education campaigns should be developed.

--- Specify two columns for the query results: issuing_agency and num_missing (the number of missing vehicle body types for the issuing agency).
--- Restrict the results such that only NULL values for vehicle_body_type are counted.
--- Group the results by issuing_agency.
--- Order the results by num_missing in descending order.

```SELECT
  -- Define the SELECT list: issuing_agency and num_missing
  issuing_agency,
  COUNT(*) AS num_missing
FROM
  parking_violation
WHERE
  -- Restrict the results to NULL vehicle_body_type values
  vehicle_body_type IS NULL
  -- Group results by issuing_agency
  GROUP BY issuing_agency
  -- Order results by num_missing in descending order
  ORDER BY
   num_missing DESC;
```

**Handling duplicated data**
-- Databases should not store duplicated values.
-- Duplicated values waste storage and potentially distorts analysis.
## Detecting duplicated data is therefore crucial.

---We can use ROW_NUMBER() function to id duplicates
```ROW_NUMBER() OVER (PARTITION BY VAR1, VAR2, VAR3) -1 
AS duplicates
FROM 
	table_name
```
--- In this case, the duplicate values above 0 can be removed from the dataset.

--- Another form of duplicates which may be encountered is **IMPARTIAL DUPLICATES**
--- This arises when record values are dupliated for some columns values, while introducing ambiguity for column values which they differ.
--- Simply deleting duplicate values in this case doesn't is not appropriate due to the ambiguity.

**Exercise**
## Duplicate parking violations
There have been a number of complaints indicating that some New York residents have been receiving multiple parking tickets for a single violation. This is resulting in the affected residents having to incur additional legal fees for a single incident. There is justifiable anger about this situation. You have been tasked with identifying records that reflect this duplication of violations.

In this exercise, using ROW_NUMBER(), you will find parking_violation records that contain the same plate_id, issue_date, violation_time, house_number, and street_name, indicating that multiple tickets were issued for the same violation.

--- Use ROW_NUMBER() with columns plate_id, issue_date, violation_time, house_number, and street_name to define the duplicate window.
```SELECT
  	summons_number,
    -- Use ROW_NUMBER() to define duplicate window
  	ROW_NUMBER() OVER(
        PARTITION BY 
            plate_id, 
          	issue_date, 
          	violation_time, 
          	house_number, 
          	street_name
    -- Modify ROW_NUMBER() value to define duplicate column
      ) -1 AS duplicate, 
    plate_id, 
    issue_date, 
    violation_time, 
    house_number, 
    street_name
FROM 
	parking_violation;
```

## Duplicate parking violations
There have been a number of complaints indicating that some New York residents have been receiving multiple parking tickets for a single violation. This is resulting in the affected residents having to incur additional legal fees for a single incident. There is justifiable anger about this situation. You have been tasked with identifying records that reflect this duplication of violations.

In this exercise, using ROW_NUMBER(), you will find parking_violation records that contain the same plate_id, issue_date, violation_time, house_number, and street_name, indicating that multiple tickets were issued for the same violation.

---Using the previous query's results, SELECT all records that have a duplicate value that is 1 or greater.

```SELECT 
	-- Include all columns 
	*
FROM (
	SELECT
  		summons_number,
  		ROW_NUMBER() OVER(
        	PARTITION BY 
            	plate_id, 
          		issue_date, 
          		violation_time, 
          		house_number, 
          		street_name
      	) - 1 AS duplicate, 
      	plate_id, 
      	issue_date, 
      	violation_time, 
      	house_number, 
      	street_name 
	FROM 
		parking_violation
) sub
WHERE
	-- Only return records where duplicate is 1 or more
	duplicate > 0;
```

## Resolving impartial duplicates
The parking_violation dataset has been modified to include a fee column indicating the fee for the violation. This column would be useful for keeping track of New York City parking ticket revenue. However, due to duplicated violation records, revenue calculations based on the dataset would not be accurate. These duplicate records only differ based on the value in the fee column. All other column values are shared in the duplicated records. A decision has been made to use the minimum fee to resolve the ambiguity created by these duplicates.

Identify the 3 duplicated parking_violation records and use the MIN() function to determine the fee that will be used after removing the duplicate records.

--- Return the summons_number and the minimum fee for duplicated records.
--- Group the results by summons_number.
--- Restrict the results to records having a summons_number count that is greater than 1.

```SELECT 
	-- Include SELECT list columns
	summons_number, 
    MIN(fee) AS fee
FROM 
	parking_violation 
GROUP BY
	-- Define column for GROUP BY
	summons_number 
HAVING 
	-- Restrict to summons numbers with count greater than 1
	COUNT(summons_number) > 1;
```

## Deleting Invalid values
**Handling invalid values with patter match**
--- To ID scores for example with non digit values, we can use patter match
```...WHERE
	score NOT SIMILAR to '\d+';
```
---This returns values where score has non-digit characters. However, the value for score is between 0 and 100, hence scores with scores of more than 100 will not be flagged.
--- A better query for this will be
```...WHERE
	score NOT SIMILAR to '\d{1}' AND
	score NOT SIMILAR to '\d{2}' AND
	score NOT SIMILAR to '\d{3}';
```
--- This will flag entries with scores of more than 3 digits. However, a score of 175 will not be flagged.
--- In this case, the strategy has to be restricting scores to integer values, so that non-integer will be disallowed.
--- To do this we change the score column from text to small integer using ALTER.
```ALTER TABLE table_name
    ALTER COLUMN score TYPE SMALLINT USING score::smallint;
```
--- This however still allows for invalid characters.
--- With the SMALLINT transformation, we could then use other conditional statements. e.g,  such as
```score <0 OR
    score >=101;
```
---We can also use BETWEEN
```WHERE score NOT BETWEEN 0 AND 100;
```

**Exercise**
Detecting invalid values with regular expressions
In the video exercise, we saw that there are a number of ways to detect invalid values in our data. In this exercise, we will use regular expressions to identify records with invalid values in the parking_violation table.

A couple of regular expression patterns that will be useful in this exercise are c{n} and c+. c{n} matches strings which contain the character c repeated n times. For example, x{4} would match the pattern xxxx. c+ matches strings which contain the character c repeated one or more times. This pattern would match strings including xxxx as well as x and xx.


---1.  Write a query returning records with a registration_state that does not match two consecutive uppercase letters.
```SELECT
  summons_number,
  plate_id,
  registration_state
FROM
  parking_violation
WHERE
  -- Define the pattern to use for matching
  registration_state NOT SIMILAR TO '[A-Z][A-Z]';
```

---2. Write a query that returns records containing a plate_type that does not match three consecutive uppercase letters.
```SELECT
  summons_number,
  plate_id,
  plate_type
FROM
  parking_violation
WHERE
  -- Define the pattern to use for matching
  plate_type NOT SIMILAR TO '[A-Z][A-Z][A-Z]';
```

---2. 

---3.Write a query returning records with a vehicle_make not including an uppercase letter, a forward slash (/), or a space (\s).
```SELECT
  summons_number,
  plate_id,
  vehicle_make
FROM
  parking_violation
WHERE
  -- Define the pattern to use for matching
  vehicle_make NOT SIMILAR TO '%[A-Z]/%\s]%';
```

## Identifying out-of-range vehicle model years
Type constraints are useful for restricting the type of data that can be stored in a table column. However, there are limitations to how thoroughly these constraints can prevent invalid data from entering the column. Range constraints are useful when the goal is to identify column values that are included in a range of values or excluded from a range of values. Using type constraints when defining a table followed by checking column values with range constraints are a powerful approach to ensuring the integrity of data.

In this exercise, you will use a BETWEEN clause to build a range constraint to identify invalid vehicle model years in the parking_violation table. Valid vehicle model years for this dataset are considered to be between 1970 and 2021.

**Write a query that returns the summons_number, plate_id, and vehicle_year for records in the parking_violation table containing a vehicle_year outside of the range 1970-2021.**
```SELECT
  -- Define the columns to return from the query
  summons_number,
  plate_id,
  vehicle_year
FROM
  parking_violation
WHERE
  -- Define the range constraint for invalid vehicle years
  vehicle_year NOT BETWEEN 1970 AND 2021;
```

## Detecting Inconsistent Data

**Exercise**
##Identifying invalid parking violations
The parking_violation table has three columns populated by related time values. The from_hours_in_effect column indicates the start time when parking restrictions are enforced at the location where the violation occurred. The to_hours_in_effect column indicates the ending time for enforcement of parking restrictions. The violation_time indicates the time at which the violation was recorded. In order to ensure the validity of parking tickets, an audit is being performed to identify tickets given outside of the restricted parking hours.
In this exercise, you will use the parking restriction time range defined by from_hours_in_effect and to_hours_in_effect to identify parking tickets with an invalid violation_time.

--- Complete the SELECT query to return the summons_number, violation_time, from_hours_in_effect, and to_hours_in_effect for violation_time values, in that order, outside of the restricted range.
```SELECT 
  -- Specify return columns
  summons_number, 
  violation_time, 
  from_hours_in_effect, 
  to_hours_in_effect
FROM 
  parking_violation 
WHERE 
  -- Condition on values outside of the restricted range
  violation_time NOT BETWEEN from_hours_in_effect AND to_hours_in_effect;
```

--- Add a condition such that the returned records are those with from_hours_in_effect less than to_hours_in_effect ensuring only records without overnight restrictions are included.
```SELECT 
  summons_number, 
  violation_time, 
  from_hours_in_effect, 
  to_hours_in_effect 
FROM 
  parking_violation 
WHERE 
  -- Exclude results with overnight restrictions 
  from_hours_in_effect < to_hours_in_effect AND 
  violation_time NOT BETWEEN from_hours_in_effect AND to_hours_in_effect;
```

--- Invalid violations with overnight parking restrictions
In the previous exercise, you identified parking_violation records with violation_time values that were outside of the restricted parking times. The query for identifying these records was restricted to violations that occurred at locations without overnight restrictions. A modified query can be constructed to capture invalid violation times that include overnight parking restrictions. The parking violations in the dataset satisfying this criteria will be identified in this exercise.

For example, this query will identify that a record with a from_hours_in_effect value of 10:00 PM, a to_hours_in_effect value of 10:00 AM, and a violation_time of 4:00 PM is an invalid record.

--- Add a condition to the SELECT query that ensures the returned records contain a from_hours_in_effect value that is greater than the to_hours_in_effect value.
--- Add a condition that ensures the violation_time is less than the from_hours_in_effect.
--- Add a condition that ensures the violation_time is greater than the to_hours_in_effect.

```SELECT
  summons_number,
  violation_time,
  from_hours_in_effect,
  to_hours_in_effect
FROM
  parking_violation
WHERE
  -- Ensure from hours greater than to hours
  from_hours_in_effect > to_hours_in_effect AND
  -- Ensure violation_time less than from hours
  violation_time < from_hours_in_effect AND
  -- Ensure violation_time greater than to hours
  violation_time > to_hours_in_effect;
```

## Recovering deleted data
While maintenance of the film permit data was taking place, a mishap occurred where the column storing the New York City borough was deleted. While the data was backed up the previous day, additional permit applications were processed between the time the backup was made and when the borough column was removed. In an attempt to recover the borough values while preserving the new data, you decide to use some data cleaning skills that you have learned to rectify the situation.

Fortunately, a table mapping zip codes and boroughs is available (nyc_zip_codes). You will use the zip codes from the film_permit table to re-populate the borough column values. This will be done utilizing five sub-queries to specify which of the five boroughs to use in the new borough column.

## Question
In the lesson on handling missing data, you learned how to categorize missing data. Before writing any code, can you identify what type of missing data this example represents?
**Missing Completely at Random**

--- Define 1 subquery (of the 5) that will be used to select zip_codes from the nyc_zip_codes table that are in the borough of Manhattan.
```-- Select all zip codes from the borough of Manhattan
SELECT * FROM nyc_zip_codes WHERE borough = 'Manhattan';
```
Complete the CASE statement sub-queries so that the borough column is populated by the correct borough name when the zip_code is matched.
Use NULL to indicate that the zip_code value is not associated to any borough for later investigation.

```SELECT 
	event_id,
	CASE 
      WHEN zip_code IN (SELECT zip_code FROM nyc_zip_codes WHERE borough = 'Manhattan') THEN 'Manhattan' 
      -- Match Brooklyn zip codes
      WHEN zip_code IN (SELECT zip_code FROM nyc_zip_codes WHERE borough = 'Brooklyn') THEN 'Brooklyn'
      -- Match Bronx zip codes
      WHEN zip_code IN (SELECT zip_code FROM nyc_zip_codes WHERE borough = 'Bronx') THEN 'Bronx'
      -- Match Queens zip codes
      WHEN zip_code IN (SELECT zip_code FROM nyc_zip_codes WHERE borough = 'Queens') THEN 'Queens'
      -- Match Staten Island zip codes
      WHEN zip_code IN (SELECT zip_code FROM nyc_zip_codes WHERE borough = 'Staten Island') THEN 'Staten Island'
      -- Use default for non-matching zip_code
      ELSE NUll 
    END as borough
FROM
	film_permit;
```


### Chapter 3:
** Data Type Conversions**
**Determining Column Type**
```SELECT 
	Col_name,
	data_type
FROM 
                information_schema.columns
WHERE 
	table_name = 'table_name'; OR

--- to get the column name of a single column, we use

WHERE 
	table_name = 'table_name' AND
	column_name = 'column name';
```

---CAST function can be used.
--e.g, if we intend to take the difference between MAX and MIN of score, but score is not stored as an integer, we can use CAST
```SELECT MAX(CAST (score AS int)) - MIN(CAST(score AS int))
```
OR we can use :: which is a short hand for CAST
``` MAX(score::int) - MIN(score::int)
```
**Exercise**
Type conversion with a CASE clause
One of the parking_violation attributes included for each record is the vehicle's location with respect to the street address of the violation. An 'F' value in the violation_in_front_of_or_opposite column indicates the vehicle was in front of the recorded address. A 'O' value indicates the vehicle was on the opposite side of the street. The column uses the TEXT type to represent the column values. The same information could be captured using a BOOLEAN (true/false) value which uses less memory.

In this exercise, you will convert violation_in_front_of_or_opposite to a BOOLEAN column named is_violation_in_front using a CASE clause. This column is true for records that occur in front of the recorded address and false for records that occur opposite of the recorded address.

```SELECT
  CASE WHEN
          -- Use true when column value is 'F'
          violation_in_front_of_or_opposite = 'F' THEN TRUE
       WHEN
          -- Use false when column value is 'O'
          violation_in_front_of_or_opposite = 'O' THEN FALSE
       ELSE
          NULL
  END AS is_violation_in_front
FROM
  parking_violation;
```

---Applying aggregate functions to converted values
As demonstrated in the video exercise, converting a column's value from TEXT to a number allows for calculations to be performed using aggregation functions. The summons_number is of type TEXT in the parking_violation dataset. The maximum (using MAX(summons_number)) and minimum (using MIN(summons_number)) of the TEXT representation summons_number can be calculated. If you, however, want to know the size of the range (max - min) of summon_number values , this calculation is not possible because the operation of subtraction on TEXT types is not defined. First, converting summons_number to a BIGINT will resolve this problem.

In this exercise, you will calculate the size of the range of summons_number values as the difference between the maximum and minimum summons_number.

---Define the range_size for summons_number as the difference between the maximum summons_number and the minimum of the summons_number using the summons_number column after converting to the BIGINT type.

```SELECT
  -- Define the range_size from the max and min summons number
  MAX(CAST (summons_number AS BIGINT)) - MIN(CAST (summons_number AS BIGINT)) AS range_size
FROM
  parking_violation;
```
** Data PARSING**

**Exercise**
Cleaning invalid dates
The date_first_observed column in the parking_violation dataset represents the date when the parking violation was first observed by the individual recording the violation. Unfortunately, not all date_first_observed values were recorded properly. Some records contain a '0' value for this column. A '0' value cannot be interpreted as a DATE automatically as its meaning in this context is ambiguous. The column values require cleaning to enable conversion to a proper DATE column.

In this exercise, you will convert the date_first_observed value of records with a '0' date_first_observed value into NULL values using the NULLIF() function, so that the field can be represented as a proper date.

---Replace '0' values in the date_first_observed using the NULLIF() function.

```SELECT
  -- Replace '0' with NULL
  NULLIF(date_first_observed, '0') AS date_first_observed
FROM
  parking_violation;
```
---Convert the TEXT values in the date_first_observed column (with NULL in place of '0') into DATE values.
```
SELECT
  -- Convert date_first_observed into DATE
  DATE(NULLIF(date_first_observed, '0')) AS date_first_observed
FROM
  parking_violation;
```

##Converting and displaying dates
The parking_violation dataset with which we have been working has two date columns where dates are represented in different formats: issue_date and date_first_observed. This is the case because these columns were imported into the database table as TEXT types. Using the DATE formatting approaches covered in the video exercise, it is possible to convert the dates from TEXT values into proper DATE columns and then output the dates in a consistent format.

In this exercise, you will use DATE() to convert vehicle_expiration_date and issue_date into DATE types and TO_CHAR() to display each value in the YYYYMMDD format.

---Convert the TEXT columns issue_date and date_first_observed to DATE types.

```SELECT
  summons_number,
  -- Convert issue_date to a DATE
  DATE(issue_date) AS issue_date,
  -- Convert date_first_observed to a DATE
  DATE(date_first_observed) AS date_first_observed
FROM
  parking_violation;
```

---Use the TO_CHAR() function to display the issue_date and date_first_observed DATE columns in the YYYYMMDD format.
```SELECT
  summons_number,
  -- Display issue_date using the YYYYMMDD format
  TO_CHAR(issue_date, 'YYYYMMDD') AS issue_date,
  -- Display date_first_observed using the YYYYMMDD format
  TO_CHAR(date_first_observed, 'YYYYMMDD') AS date_first_observed
FROM (
  SELECT
    summons_number,
    DATE(issue_date) AS issue_date,
    DATE(date_first_observed) AS date_first_observed
  FROM
    parking_violation
) sub
```

## Timestamp Parsing and formatting

**Exercises**
Extracting hours from a time value
Your team has been tasked with generating a summary report to better understand the hour of the day when most parking violations are occurring. The violation_time field has been imported into the database using strings consisting of the hour (in 12-hour format), the minutes, and AM/PM designation for each violation. An example time stored in this field is '1225AM'. Note the lack of a colon and space in this format.

Use the TO_TIMESTAMP() function and the proper format string to convert the violation_time into a TIMESTAMP, extract the hour from the TIME component of this TIMESTAMP, and provide a count of all parking violations by hour issued. The given conversion to a TIME value is performed because violation_time values do not include date information.

--- Convert violation_time to a TIMESTAMP using the TO_TIMESTAMP() function and a format string including 12-hour format (HH12), minutes (MI), and meridian indicator (AM or PM). ::TIME converts the resulting timestamp value to a TIME.
Exclude records with a NULL-valued violation_time.

```SELECT
  -- Convert violation_time to a TIMESTAMP
  TO_TIMESTAMP(violation_time, 'HH12MIPM')::TIME AS violation_time
FROM
  parking_violation
WHERE
  -- Exclude NULL violation_time
  violation_time IS NOT NULL;
```

---Use the EXTRACT() function to complete the query such that the first column of the resulting records is populated by the hour of the violation_time.

```SELECT
  -- Populate column with violation_time hours
  EXTRACT(hour FROM violation_time) AS hour,
  COUNT(*)
FROM (
    SELECT
      TO_TIMESTAMP(violation_time, 'HH12MIPM')::TIME as violation_time
    FROM
      parking_violation
    WHERE
      violation_time IS NOT NULL
) sub
GROUP BY
  hour
ORDER BY
  hour
```

## A parking violation report by day of the month
Hearing anecdotal evidence that parking tickets are more likely to be given out at the end of the month compared to during the month, you have been tasked with preparing data to get a sense of the distribution of tickets by day of the month. While the date on which the violation occurred is included in the parking_violation dataset, it is currently represented as a string date. While this presents an obstacle for producing the data required, you feel confident in your ability to get the data in the format that you need.

In this exercise, you will convert the strings representing the issue_date into proper PostgreSQL DATE values. From this representation of the data, you will extract the day of the month required to produce the distribution of violations by month day.

---Use one of the techniques introduced in this chapter to convert a string representing a date into a PostgreSQL DATE to convert issue_date into a DATE value.
```SELECT
  -- Convert issue_date to a DATE value
  DATE(issue_date) AS issue_date
FROM
  parking_violation;
```

```SELECT
  -- Create issue_day from the day value of issue_date
  EXTRACT(day FROM issue_date) AS issue_day,
  -- Include the count of violations for each day
  COUNT(*)
FROM (
  SELECT
    -- Convert issue_date to a `DATE` value
    DATE(issue_date) AS issue_date
  FROM
    parking_violation
) sub
GROUP BY
  issue_day
ORDER BY
  issue_day;
```

##Risky parking behavior
The parking_violation table contains many parking violation details. However, it's unclear what causes an individual to violate parking restrictions. One hypothesis is that violators attempt to park in restricted areas just before the parking restrictions end. You have been asked to investigate this phenomenon. You first need to contend with the fact that times in the parking_violation table are represented as strings.

In this exercise, you will convert violation_time, and to_hours_in_effect to TIMESTAMP values for violations that took place in locations with partial day restrictions, calculate the interval between the violation_time and to_hours_in_effect for these records, and identify the records where the violation_time is less than 1 hour before to_hours_in_effect.

---Convert violation_time and to_hours_in_effect to TIMESTAMP values using TO_TIMESTAMP() and the appropriate format string. ::TIME converts the value to a TIME.
Exclude locations having both a from_hours_in_effect value of 1200AM and a to_hours_in_effect value of 1159PM.

```
SELECT
  summons_number,
  -- Convert violation_time to a TIMESTAMP
  TO_TIMESTAMP(violation_time, 'HH12MIPM')::TIME as violation_time,
  -- Convert to_hours_in_effect to a TIMESTAMP
  TO_TIMESTAMP(to_hours_in_effect, 'HH12MIPM')::TIME as to_hours_in_effect
FROM
  parking_violation
WHERE
  -- Exclude all day parking restrictions
  NOT (from_hours_in_effect = '1200AM' AND to_hours_in_effect = '1159PM');
```
---Use the EXTRACT() function to create two columns representing the number of hours and minutes, respectively, between violation_time and to_hours_in_effect.
```SELECT
  summons_number,
  -- Create column for hours between to_hours_in_effect and violation_time
  EXTRACT(hours FROM to_hours_in_effect - violation_time) AS hours,
  -- Create column for minutes between to_hours_in_effect and violation_time
  EXTRACT(minutes FROM to_hours_in_effect - violation_time) AS minutes
FROM (
  SELECT
    summons_number,
    TO_TIMESTAMP(violation_time, 'HH12MIPM')::time as violation_time,
    TO_TIMESTAMP(to_hours_in_effect, 'HH12MIPM')::time as to_hours_in_effect
  FROM
    parking_violation
  WHERE
    NOT (from_hours_in_effect = '1200AM' AND to_hours_in_effect = '1159PM')
) sub
```

---The previous query results are stored in a table named time_differences which contains the columns hours and minutes. Count the number of violation_time values that are within 0 hours and 59 minutes of the record's to_hours_in_effect value.

```SELECT
  -- Return the count of records
  COUNT(*)
FROM
  time_differences
WHERE
  -- Include records with a hours value of 0
  hours = 0 AND
  -- Include records with a minutes value of at most 59
  minutes <= 59;
```

**Summary of Chapter 3**

!ch3summary ch1summary.png


## Chapter 4

**Combining columns**
--CONCATENATION: This is joining individual values end-to-end to get a single value.

```SELECT
	CONCAT(
--The E backslash n indicates that the remaining argument should be displayed on the next line
	       name, E'\n',
	       building, ' ', street, E'\n',
	       boro, ', NY ', zip_code
	      ) AS mailing_address
 FROM
 	restaurant_inspection
```
--Using CONCAT with NULL values results in the NULLs being ignored, hence you may have, in this case, incomplete addresse.
--Ideally, we want the NULL values to be included. In this case we can achieve it with the double pile function.
--The double pipe operator combines the values that surround it. So in essence, the double pipe operator returns the null values where there is null.
-- Using our example above


```SELECT
	       name || E'\n' ||
	       building || ' ' || street || E'\n' ||
	     ||  boro || ', NY ' || zip_code
	       AS mailing_address
 FROM
 	restaurant_inspection
```
**Exercise**

##Tallying corner parking violations
The parking_violation table has two columns (street_name and intersecting_street) with New York City streets. When the values for both columns are not NULL, this indicates that the violation occurred on a corner where two streets intersect. In an effort to identify street corners that tend to be the location of frequent parking violations, you have been tasked with identifying which violations occurred on a street corner and the total number of violations at each corner.

--In this exercise, you will concatenate the street_name and intersecting_street columns to create a new corner column. Then all parking violations occurring at a corner will be tallied by a SQL query.

--1. Combine street_name, ' & ' (an ampersand surrounded by two spaces), and intersecting_street to create a column named corner. Write the query such that records without an intersecting_street value have NULL column entries.

```SELECT
  -- Combine street_name, ' & ', and intersecting_street
  street_name || ' & ' || intersecting_street AS corner
FROM
  parking_violation;
```

--2. Use the corner query that you just completed to generate a column with the corner value and a second column with the total number of violations occurring at each corner.
Exclude corner values that are NULL.

```SELECT
  -- include the corner in results
  corner,
  -- include the total number of violations occurring at corner
  COUNT(*)
FROM (
  SELECT
    -- concatenate street_name, ' & ', and intersecting_street
    street_name || ' & ' || intersecting_street AS corner
  FROM
    parking_violation
) sub
WHERE
  -- exclude corner values that are NULL
  corner IS NOT NULL
GROUP BY
  corner
ORDER BY
  count DESC
```

##Creating a TIMESTAMP with concatenation
In a previous exercise, the violation_time column in the parking_violation table was used to check that the recorded violation_time is within the violation location's restricted times. This presented a challenge in cases where restricted parking took place overnight because, for these records, the from_hours_in_effect time is later than the to_hours_in_effect time. This issue could be eliminated by including a date in addition to the time of a violation.

In this exercise, you will begin the process of simplifying the identification of overnight violations through the creation of the violation_datetime column populated with TIMESTAMP values. This will be accomplished by concatenating issue_date and violation_time and converting the resulting strings to TIMESTAMP values.

--1. Concatenate the issue_date column, a space character (' '), and the violation_time column to create a violation_datetime column in the query results.
```SELECT
  -- Concatenate issue_date and violation_time columns
  CONCAT(issue_date, ' ', violation_time) AS violation_datetime
FROM
  parking_violation;
```

--2.Complete the query so that the violation_datetime strings returned by the subquery are converted into proper TIMESTAMP values using the format string MM/DD/YYYY HH12MIAM.
```SELECT
  -- Convert violation_time to TIMESTAMP
  TO_TIMESTAMP(violation_datetime, 'MM/DD/YYYY HH12MIAM') AS violation_datetime
FROM (
  SELECT
    CONCAT(issue_date, ' ', violation_time) AS violation_datetime
  FROM
    parking_violation
) sub;
```

## SPlitting data: The reverse of CONCATENATION
--Finding substring starting position with STRPOS() function
--STRPOS() returns 0 when the source_string does not contain a search_string

e.g.
```STRPOS ('09B Thawing procedures', ' ');
```
-- In this case STRPOS() returns 4.
--However, if ```('09B Thawing procedures', '?')```, STRPOS() will return 0, given that there is no '?' in the string value.

##Extrating substring using SUBSTRING
``` SELECT
	SUBSTRING(
	'09B Thawing procedures'
	FROM 1 
	FOR STRPOS('09B Thawing procedures', ' ') - 1
             );
```
**Exercise**
Extracting time units with SUBSTRING()
In a previous exercise, you separated the interval between the violation_time and to_hours_in_effect columns into their constituent hour and minute time units. Some pre-cleaning of these values was done behind the scenes to make the values more amenable for conversion because of inconsistencies in the recording of these values. The functions explored in this lesson provide an approach to extract values from strings.

In this exercise, you will use SUBSTRING() to extract the hour and minute units from time strings. This is an alternative approach to extracting time units removing the need to convert the string to a TIMESTAMP value to extract the time unit as was done previously.
 
--1. Define the hour column as the substring starting at the 1st position in violation_time and extending 2 characters in length.
```SELECT
  -- Define hour column
  SUBSTRING(violation_time FROM 1 FOR 2) AS hour
FROM
  parking_violation;
```

--2. Add a definition for the minute column in the results as the substring starting at the 3rd position in violation_time and extending 2 characters in length.
```SELECT
  SUBSTRING(violation_time FROM 1 FOR 2) AS hour,
  -- Define minute column
  SUBSTRING(violation_time FROM 3 FOR 2) AS minute
FROM
  parking_violation;
```


Extracting house numbers from a string
Addresses for the Queens borough of New York City are unique in that they often include dashes in the house number component of the street address. For example, for the address 86-16 60 Ave, the house number is 16, and 86 refers to the closest cross street. Therefore, if we want the house_number to strictly represent the house number where a parking violation occurred, we need to extract the digits after the dash (-) to represent this value.

In this exercise, you will use STRPOS(), SUBSTRING(), and LENGTH() to extract the specific house number from Queens street addresses.

--1. Write a query that returns the position in the house_number column where the first dash character (-) location is found or 0 if the house_number does not contain a dash (-).

```SELECT
  -- Find the position of first '-'
  STRPOS(house_number, '-') AS dash_position
FROM
  parking_violation;
```

--2. 
```SELECT
  house_number,
  -- Extract the substring after '-'
  SUBSTRING(
    -- Specify the column of the original house number
    house_number
    -- Calculate the position that is 1 beyond '-'
    FROM STRPOS(house_number, '-') + 1
    -- Calculate number characters from dash to end of string
    FOR LENGTH(house_number) - STRPOS(house_number, '-')
  ) AS new_house_number
FROM
  parking_violation;
```
## Splitting data with DELIMITERS

--SPLIT_PART requires 3 Arguments.
```SPLIT_PART(stringvar, 'delimiter', 1)```
-1 determins what part of the split should be returned

e.g.
```SPLIT_PART('Cycle Inspection / Re-Inspection', '/', 1)``` returns 'Cycle Inspection'. 2 at thelast argument returns Re-Inspection

**Splitting data into Rows REGEXP_SPLIT_TO_TABLE**

**Exercise**

Splitting house numbers with a delimiter
In the previous exercise, you used STRPOS(), LENGTH(), and SUBSTRING() to separate the actual house number for Queens addresses from the value representing a cross street. In the video exercise, you learned how strings can be split into parts based on a delimiter string value.

In this exercise, you will extract the house number for Queens addresses using the SPLIT_PART() function.

--1. Write a query that returns the part of the house_number value after the dash character ('-') (if a dash character is present in the column value) as the column new_house_number.
```SELECT
  -- Split house_number using '-' as the delimiter
  SPLIT_PART(house_number, '-', 2) AS new_house_number
FROM
  parking_violation
WHERE
  violation_county = 'Q';
```

--1. Use REGEXP_SPLIT_TO_TABLE() with the empty-string ('') as a delimiter to split days_parking_in_effect into a single availability symbol (B or Y).
Include street_address and violation_county as columns so that each row contains these associated values.

```SELECT
  -- Specify SELECT list columns
  street_address,
  violation_county,
  REGEXP_SPLIT_TO_TABLE(days_parking_in_effect, '') AS daily_parking_restriction
FROM
  parking_restriction;
```
--2. Use the ROW_NUMBER() function to enumerate each combination of street_address and violation_county values with a number from 1 (Monday) to 7 (Sunday) corresponding to the daily_parking_restriction values.

```SELECT
  -- Label daily parking restrictions for locations by day
  ROW_NUMBER() OVER(
    PARTITION BY
        street_address, violation_county
    ORDER BY
        street_address, violation_county
  ) AS day_number,
  *
FROM (
  SELECT
    street_address,
    violation_county,
    REGEXP_SPLIT_TO_TABLE(days_parking_in_effect, '') AS daily_parking_restriction
  FROM
    parking_restriction
) sub;
```

##Creating PIVOT Tables

- One way data could be PIVOTED is to use a FILTER clause.
- FILTER clause applies an aggregation over a subset of records.
- The subset of records is determined by accompanying WHERE clause
- FILTER clause defines the columns is the SELECT list of queries.
- FILTER clauses help us re-orient our data to create a pivot table.


**Exercises**
Selecting data for a pivot table
In an effort to get a better understanding of which agencies are responsible for different types of parking violations, you have been tasked with creating a report providing these details. This report will focus on four issuing agencies: Police Department (P), Department of Sanitation (S), Parks Department (K), and Department of Transportation (V). All of the records required to create such a report are present in the parking_violations table. An INTEGER violation_code and CHAR issuing_agency is recorded for every parking_violation.

In this exercise, you will write a SELECT query that provides the underlying data for your report: the parking violation code, the issuing agency code, and the total number of records with each pair of values.

-- Include violation_code and issuing_agency in the SELECT list for the query.
-- For each violation_code and issuing_agency pair, include the number of records containing the pair in the SELECT list.
-- Restrict the results to the agencies of interest based on their single-character code (P, S, K, V).

```SELECT 
	-- Include the violation code in results
	violation_code, 
    -- Include the issuing agency in results
    issuing_agency, 
    -- Number of records with violation code/issuing agency
    COUNT(*) 
FROM 
	parking_violation 
WHERE 
	-- Restrict the results to the agencies of interest
	issuing_agency IN ('P', 'S', 'K', 'V') 
GROUP BY 
	-- Define GROUP BY columns to ensure correct pair count
	violation_code, issuing_agency
ORDER BY 
	violation_code, issuing_agency;
```

##Using FILTER to create a pivot table
In the previous exercise, you wrote a query that provided information on the number of parking violations (by their numerical code) issued by each of four agencies. The results contained all of the desired information but were presented in a format that included a duplicate display of each violation_code up to four times (for every issuing_agency selected) in the results. A more compact representation of the same data can be achieved through the creation of a pivot table.

In this exercise, you will write a query using the FILTER clause to produce results in a pivot table format. This improved presentation of the data can more easily be used in the report for parking violations issued by each of the four agencies of interest.

-- Define the Police column as the number of records for each violation_code with an issuing_agency value of P.
-- Define the Sanitation column as the number of records for each violation_code with an issuing_agency value of S.
-- Define the Parks column as the number of records for each violation_code with an issuing_agency value of K.
-- Define the Transportation column as the number of records for each violation_code with an issuing_agency value of V.

```SELECT 
	violation_code, 
    -- Define the "Police" column
	COUNT(issuing_agency) FILTER (WHERE issuing_agency = 'P') AS "Police",
    -- Define the "Sanitation" column
	COUNT(issuing_agency) FILTER (WHERE issuing_agency = 'S') AS "Sanitation",
    -- Define the "Parks" column
	COUNT(issuing_agency) FILTER (WHERE issuing_agency = 'K') AS "Parks",
    -- Define the "Transportation" column
	COUNT(issuing_agency) FILTER (WHERE issuing_agency = 'V') AS "Transportation"
FROM 
	parking_violation 
GROUP BY 
	violation_code
ORDER BY 
	violation_code
```

##Aggregating film categories
For the final exercise in this course, let's return to the film_permit table. It contains a community_board TEXT column composed of a comma-separated list of integers. There is interest in doing an analysis of the types of film permits that are being provided for each community board. However, the representation of community boards (INTEGERs in a TEXT column) makes this difficult. By using techniques learned in this chapter, the data can be transformed to allow for such an analysis.

In this exercise, you will first create a (temporary) VIEW that represents the community_board values individually for two permit categories. A VIEW is a named query that can be used like a TABLE once created. You will use this VIEW in a subquery for aggregating the results in a pivot table.


--1. Use REGEXP_SPLIT_TO_TABLE() to split community_board into multiple rows using a comma (',') followed by a space character (' ') as the 2-character delimiter.
Restrict the category values to 'Film', 'Television', and 'Documentary'.
```CREATE OR REPLACE TEMP VIEW cb_categories AS 
SELECT
	-- Split community board values
	REGEXP_SPLIT_TO_TABLE(community_board, ', ') AS community_board,
    category
FROM
	film_permit
WHERE 
    -- Restrict the categories in results
    category IN ('Film', 'Television', 'Documentary');

-- View cb_categories
SELECT * FROM cb_categories;
```

--2. Convert community_board values to INTEGER so that community_board values are listed in ascending order.
Define the Film, Television, and Documentary pivot table columns as the number of permits of each type for each community board.
```SELECT
	-- Convert community_board data type
	CAST(community_board AS INT) AS community_board,
    -- Define pivot table columns
	COUNT(category) FILTER(where category = 'Film') AS "Film",
    COUNT(category) FILTER(where category = 'Television') AS "Television",
	COUNT(category) FILTER(where category = 'Documentary') AS "Documentary"
FROM
	cb_categories
GROUP BY 
	community_board
ORDER BY 
	community_board;
```
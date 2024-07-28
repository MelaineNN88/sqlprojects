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


# SQL Cheatsheet

A quick reminder of all relevant SQL queries and examples on how to use them. 

For proper SQL style and formating, follow www.sqlstyle.guide

# Table of Contents 
1.  [ Finding Data Queries. ](#find)
2.  [ Data Modification Queries. ](#modify)
3.  [ Aggregation Functions. ](#aggregation)
4.  [ Join Queries. ](#joins)
5.  [ View Queries. ](#view)
6.  [ Altering Table Queries.](#alter)
7.  [ Creating Table Query.](#create)
8.  [ Common Table Expressions.](#cte)
9.  [ Windows Functions ](#windows)
10. [ Cleaning Functions ](#cleaning)
11. [ Stored Procedures ](#procedures)

<a name="find"></a>
# 1. Finding Data Queries

### **DISTINCT**: filters away duplicate values and returns rows of specified column
* `SELECT DISTINCT` column_name;

### **WHERE**: used to filter records/rows
``` sql
SELECT column1, column2 FROM table_name WHERE condition;
SELECT * FROM table_name WHERE condition1 AND condition2;
SELECT * FROM table_name WHERE condition1 OR condition2;
SELECT * FROM table_name WHERE NOT condition;
SELECT * FROM table_name WHERE condition1 AND (condition2 OR condition3);
SELECT * FROM table_name WHERE EXISTS` (SELECT column_name FROM table_name WHERE condition);
```

### **ORDER BY**: used to sort the result-set in ascending or descending order
``` sql
`SELECT` * FROM table_name ORDER BY column1`ASC, column2 DESC
```

### **SELECT TOP**: used to specify the number of records to return from top of table
``` sql
SELECT TOP number columns_names FROM table_name WHERE condition
SELECT TOP percent columns_names FROM table_name WHERE condition

-- Not all database systems support `SELECT TOP`. The MySQL equivalent is the `LIMIT` clause
SELECT column_name FROM table_name LIMIT offset, count

-- The PSQL equivalent is the `LIMIT` clause and OFFSET
SELECT column_name FROM table_name LIMIT number OFFSET number
```

### **LIKE**: operator used in a WHERE clause to search for a specific pattern in a column
* % (percent sign) is a wildcard character that represents zero, one, or multiple characters
* _ (underscore) is a wildcard character that represents a single character
* `SELECT` column_names `FROM` table_name `WHERE` column_name `LIKE` pattern;
* `LIKE` ‘a%’ (find any values that start with “a”)
* `LIKE` ‘%a’ (find any values that end with “a”)
* `LIKE` ‘%or%’ (find any values that have “or” in any position)
* `LIKE` ‘_r%’ (find any values that have “r” in the second position)
* `LIKE` ‘a_%_%’ (find any values that start with “a” and are at least 3 characters in length)
* `LIKE` ‘[a-c]%’ (find any values starting with “a”, “b”, or “c”

### **IN**: operator that allows you to specify multiple values in a WHERE clause
* essentially the IN operator is shorthand for multiple OR conditions
* `SELECT` column_names `FROM` table_name `WHERE` column_name `IN` (value1, value2, …);
* `SELECT` column_names `FROM` table_name `WHERE` column_name `IN` (`SELECT STATEMENT`);

### **BETWEEN**: operator selects values within a given range inclusive
* `SELECT` column_names `FROM` table_name `WHERE` column_name `BETWEEN` value1 `AND` value2;
* `SELECT` * `FROM` Products `WHERE` (column_name `BETWEEN` value1 `AND` value2) `AND NOT` column_name2 `IN` (value3, value4);
* `SELECT` * `FROM` Products `WHERE` column_name `BETWEEN` #01/07/1999# AND #03/12/1999#;

### **NULL**: values in a field with no value
* `SELECT` * `FROM` table_name `WHERE` column_name `IS NULL`;
* `SELECT` * `FROM` table_name `WHERE` column_name `IS NOT NULL`;

### **AS**: aliases are used to assign a temporary name to a table or column
* `SELECT` column_name `AS` alias_name `FROM` table_name;
* `SELECT` column_name `FROM` table_name `AS` alias_name;
* `SELECT` column_name `AS` alias_name1, column_name2 `AS` alias_name2;
* `SELECT` column_name1, column_name2 + ‘, ‘ + column_name3 `AS` alias_name;

### **UNION**: set operator used to combine the result-set of two or more SELECT statements
* Each SELECT statement within UNION must have the same number of columns
* The columns must have similar data types
* The columns in each SELECT statement must also be in the same order
* `SELECT` columns_names `FROM` table1 `UNION SELECT` column_name `FROM` table2;
* `UNION` operator only selects distinct values, `UNION ALL` will allow duplicates

### **INTERSECT**: set operator which is used to return the records that two SELECT statements have in common
* Generally used the same way as **UNION** above
* `SELECT` columns_names `FROM` table1 `INTERSECT SELECT` column_name `FROM` table2;

### **EXCEPT**: set operator used to return all the records in the first SELECT statement that are not found in the second SELECT statement
* Generally used the same way as **UNION** above
* `SELECT` columns_names `FROM` table1 `EXCEPT SELECT` column_name `FROM` table2;

### **ANY|ALL**: operator used to check subquery conditions used within a WHERE or HAVING clauses
* The `ANY` operator returns true if any subquery values meet the condition
* The `ALL` operator returns true if all subquery values meet the condition
* `SELECT` columns_names `FROM` table1 `WHERE` column_name operator (`ANY`|`ALL`) (`SELECT` column_name `FROM` table_name `WHERE` condition);

### **GROUP BY**: statement often used with aggregate functions (COUNT, MAX, MIN, SUM, AVG) to group the result-set by one or more columns
* `SELECT` column_name1, COUNT(column_name2) `FROM` table_name `WHERE` condition `GROUP BY` column_name1 `ORDER BY` COUNT(column_name2) DESC;

### **HAVING**: this clause was added to SQL because the WHERE keyword could not be used with aggregate functions
* `SELECT` `COUNT`(column_name1), column_name2 `FROM` table `GROUP BY` column_name2 `HAVING` `COUNT(`column_name1`)` > 5;

### **CASE WHEN**: IF but on SQL
``` sql
SELECT column_name,
       column_name,
       CASE WHEN column_name > condition THEN 'string'
            WHEN column_name < condition THEN NULL
            ELSE 1 END AS alias
  FROM table_name;
```

### **Percentages with CASE and AVG**: Complex CASE WHEN
``` sql
  SELECT column_name,
         ROUND(AVG(CASE WHEN column_name = condition THEN 1
                        WHEN column_name = condition THEN 0
                         END)
               ,2) AS alias,
         ROUND(AVG(CASE WHEN column_name = condition THEN 1
                        WHEN column_name = condition THEN 0
                         END)
               ,2) AS alias
    FROM table_name
GROUP BY column_name;
```

<a name="modify"></a>
# 2. Data Modification Queries

### **INSERT INTO**: used to insert new records/rows in a table
* `INSERT INTO` table_name (column1, column2) `VALUES` (value1, value2);
* `INSERT INTO` table_name `VALUES` (value1, value2 …);

### **UPDATE**: used to modify the existing records in a table
* `UPDATE` table_name `SET` column1 = value1, column2 = value2 `WHERE` condition;
* `UPDATE` table_name `SET` column_name = value;

### **DELETE**: used to delete existing records/rows in a table
* `DELETE FROM` table_name `WHERE` condition;
* `DELETE` * `FROM` table_name;

<a name="aggregation"></a>
# 3. Aggregation Functions

### **COUNT**: returns the # of occurrences
* `SELECT COUNT (DISTINCT` column_name`)`;

### **COUNT_BIG**: returns the # of occurrences
* `SELECT COUNT_BIG(DISTINCT` column_name`)`;

### **MIN() and MAX()**: returns the smallest/largest value of the selected column
* `SELECT MIN (`column_names`) FROM` table_name `WHERE` condition;
* `SELECT MAX (`column_names`) FROM` table_name `WHERE` condition;

### **AVG()**: returns the average value of a numeric column
* `SELECT AVG (`column_name`) FROM` table_name `WHERE` condition;

### **SUM()**: returns the total sum of a numeric column
* `SELECT SUM (`column_name`) FROM` table_name `WHERE` condition;

## Relative

### **LAG()**: returns column's value at the row n rows before the current row
* LAG(column_name, n) 

### **LEAD()**: returns column's value at the row n rows aer the current row
* LEAD(column_name, n) 

## Absolute

### **FIRST_VALUE()**: returns the rst value in the table or partition
* FIRST_VALUE(column_name) 

### **FIRST_VALUE()**: returns the last value in the table or partition
LAST_VALUE(column_name) 

<a name="joins"></a>
# 4. Join Queries

###  **INNER JOIN**: returns records that have matching value in both tables
* `SELECT` column_names `FROM` table1 `INNER JOIN` table2 `ON` table1.column_name=table2.column_name;
* `SELECT` table1.column_name1, table2.column_name2, table3.column_name3 `FROM` ((table1 `INNER JOIN` table2 `ON` relationship) `INNER JOIN` table3 `ON` relationship);

### **LEFT (OUTER) JOIN**: returns all records from the left table (table1), and the matched records from the right table (table2)
* `SELECT` column_names `FROM` table1 `LEFT JOIN` table2 `ON` table1.column_name=table2.column_name;

### **RIGHT (OUTER) JOIN**: returns all records from the right table (table2), and the matched records from the left table (table1)
* `SELECT` column_names `FROM` table1 `RIGHT JOIN` table2 `ON` table1.column_name=table2.column_name;

### **FULL (OUTER) JOIN**: returns all records when there is a match in either left or right table
* `SELECT` column_names `FROM` table1 ``FULL OUTER JOIN`` table2 `ON` table1.column_name=table2.column_name;

### **Self JOIN**: a regular join, but the table is joined with itself
* `SELECT` column_names `FROM` table1 T1, table1 T2 `WHERE` condition;

<a name="view"></a>
# 5. View Queries

### **CREATE**: create a view
``` sql
CREATE VIEW view_name AS 
  SELECT column1, column2 FROM table_name WHERE condition;
```

### **SELECT**: retrieve a view
``` sql
SELECT * FROM view_name
```

### **DROP**: drop a view
``` sql
DROP VIEW view_name
```

<a name="alter"></a>
# 6. Altering Table Queries

### **ADD**: add a column
``` sql
ALTER TABLE` table_name ADD column_name column_definition
```

### **MODIFY**: change data type of column
``` sql
ALTER TABLE table_name MODIFY column_name column_type
```

### **DROP**: delete a column
``` sql
ALTER TABLE table_name DROP COLUMN column_name
```

<a name="create"></a>
# 7. Creating Table Query

### **CREATE**: create a table
``` sql
CREATE TABLE table_name(
             column1 datatype,
             column2 datatype,
             column3 datatype
             )
```
   
<a name="cte"></a>
# 8. Common Table Expressions or CTE's
``` sql
WITH cte_name AS (
      SELECT columns FROM table_name WHERE condition
     )
     cte_name_2 AS (
      SELECT columns FROM table_name WHERE condition
     )
   
SELECT columns FROM cte_name, cte_name_2 WHERE condition
```

### **Get Mode**: 
``` sql
WITH Mode (partition_column, alias)
as
(
	SELECT partition_column, 
	ROW_NUMBER() OVER(PARTITION BY partition_column ORDER BY other_column) AS alias
	FROM table_name 
)

SELECT partition_column
FROM Mode
WHERE alias IN (SELECT MAX(alias) FROM Mode)
```


<a name="windows"></a>
# 9. Windows Functions

### **OVER**: General Window Function
``` sql
SELECT column_name,
       SUM(column_name) OVER(ORDER BY column_name) AS alias
  FROM table_name 
 WHERE condition
```

### **PARTITION BY**: It is like a GROUP BY but on that column
``` sql
SELECT column_name,
       SUM(column_name) OVER(PARTITION BY column_name ORDER BY column_name) AS alias
  FROM table_name 
 WHERE condition
```

### **ROWS BETWEEN**: To take efect on certain rows: UNBOUNDED PRECEDING, 2 PRECEDING, CURRENT ROW, 2 FOLLOWING or UNBOUNDED FOLLOWING
``` sql
SELECT column_name,
       SUM(column_name) OVER(PARTITION BY column_name ORDER BY column_name ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS alias
  FROM table_name 
 WHERE condition
```

### **ROW_NUMBER, RANK, DENSE_RANK**: Generates a secuential number

* ROW_NUMBER() always assigns unique numbers, even if two rows' values are the same
* RANK() assigns the same number to rows with identical values, skipping over the next numbers in such cases
* DENSE_RANK() also assigns the same number to rows with identical values, but doesn't skip over the next numbers

``` sql
SELECT column_name,
       DENSE_RANK() OVER(ORDER BY column_name) AS alias
  FROM table_name 
 WHERE condition
``` 

### **NTILE()**: Spliting data into (approximately) equal chunks
* NTILE(n) splits the data into n approximately equal pages

``` sql
SELECT column_name,
       NTILE(3) OVER (ORDER BY column_name DESC) AS alias
  FROM table_name 
 WHERE condition
``` 

<a name="cleaning"></a>
# 10. Cleaning

### **CAST**: cast diferent data types
``` sql
SELECT CAST(column_name AS DATATYPE) FROM table_name WHERE condition
``` 

### **REPLACE**: replace from string to another string
``` sql
SELECT REPLACE(column_name, "string_from", "string_to") FROM table_name WHERE condition
```

### **TRIM**: trim a string
``` sql
SELECT TRIM(column_name) FROM table_name WHERE condition
```

### **COALESCE**: replace null values for certain string or integer
``` sql
SELECT COALESCE(column_name, "string_or_integer") FROM table_name WHERE condition
```

### **Cleaning Strings**: 
``` sql
SELECT 
    LEFT(REPLACE(UPPER(TRIM(c.column_name)), '.', ''), 3) AS alias,
FROM table_name
```

### **Safe functions**:

* CAST()	* TRY_CAST()	-- General SQL
* CONVERT()	* TRY_CONVERT()	-- SQL Server Exclusive, best performance
* PARSE()	* TRY_PARSE()	-- SQL Server Exclusive, worst alternative, don't

* ISDATE()
* CHARINDEX()
* PATINDEX()
* CONCAT()
* CONCAT_WS()
* STRING_AGG()
* STRING_SPLIT() -- on FROM clause
* CEILING()
* FLOOR()
* POWER()

<a name="procedures"></a>
# 11. Stored Procedures

### **PROCEDURE**: like functions, with parameters and please use transactions
``` sql
-- =============================================
-- Author:
-- Create date:
-- Description:
-- =============================================

CREATE PROCEDURE [dbo].[verb] @CRUD VARCHAR(8),
                              @variable_name INT = NULL,
                              @variable_name INT
AS
BEGIN
    BEGIN TRY
        BEGIN TRANSACTION
            IF @CRUD = 'select'
                BEGIN

                END
            ELSE
                IF @CRUD = 'insert'
                    BEGIN

                    END
                ELSE
                    IF @CRUD = 'update'
                        BEGIN

                        END
                    ELSE
                        IF @CRUD = 'delete'
                            BEGIN

                            END
        COMMIT
    END TRY
    BEGIN CATCH
        ROLLBACK TRANSACTION
        DECLARE @ErrorMessage VARCHAR(MAX) = ERROR_MESSAGE();
        DECLARE @ErrorSeverity VARCHAR(MAX) = ERROR_SEVERITY();
        DECLARE @ErrorState VARCHAR(MAX) = ERROR_STATE();

        RAISERROR (@ErrorMessage,@ErrorSeverity,@ErrorState)
    END CATCH
END
GO
```

``` sql
-- =============================================
-- Author:
-- Create date:
-- Description:
-- =============================================

CREATE OR ALTER PROCEDURE dbo.cuspRideSummaryDelete
	@DateParm date,
	@Error nvarchar(max) = NULL OUTPUT
AS
SET NOCOUNT ON
BEGIN
  BEGIN TRY
      DELETE FROM RideSummary
      WHERE Date = @DateParm
  END TRY
  BEGIN CATCH
		SET @Error = 
		'Error_Number: '+ CAST(ERROR_NUMBER() AS VARCHAR) +
		'Error_Severity: '+ CAST(ERROR_SEVERITY() AS VARCHAR) +
		'Error_State: ' + CAST(ERROR_STATE() AS VARCHAR) + 
		'Error_Message: ' + ERROR_MESSAGE() + 
		'Error_Line: ' + CAST(ERROR_LINE() AS VARCHAR)
  END CATCH
END;
```

``` sql
CREATE FUNCTION dbo.GetTripDistanceHotDeck()
RETURNS numeric(18,4)
AS 
BEGIN
RETURN
	(SELECT TOP 1 TripDistance
	FROM YellowTripData
    -- Sample 1000 records
	TABLESAMPLE(1000 rows)
	WHERE TripDistance > 0)
END;
```

``` sql
CREATE FUNCTION dbo.GetShiftNumber (@Hour integer)
RETURNS int
AS
BEGIN
RETURN
	-- 12am (0) to 9am (9) shift
	(CASE WHEN @Hour >= 0 AND @Hour < 9 THEN 1
     	  -- 9am (9) to 5pm (17) shift
		 WHEN @Hour >= 9 AND @Hour < 17 THEN 2
          -- 5pm (17) to 12am (24) shift
	     WHEN @Hour >= 17 AND @Hour < 24 THEN 3 END)
END;
```


<a name="sqlserver"></a>
# 12. SQL Server things

### **TOP**: LIMIT equivalent
``` sql
SELECT TOP(1) PERCENT column_name FROM table_name WHERE condition
``` 

### **LEN**: 
``` sql
SELECT LEN(column_name) as alias_name FROM table_name WHERE condition
``` 

### **CHARINDEX**: 
``` sql
SELECT CHARINDEX('string', column_name) FROM table_name WHERE condition
```

### **SUBSTRING**: 
``` sql
SELECT SUBSTRING(column_name, from, lenght) FROM table_name WHERE condition
``` 

### **DECLARE**: 
``` sql
DECLARE @variable INT = 0

SET @variable = 1
``` 

### **ISNULL**: COALESCE equivalent, but just 1 column
``` sql
SELECT ISNULL(column_name, '') FROM table_name WHERE condition
``` 

### **COALESCE**: COALESCE equivalent, multiple columns
``` sql
SELECT COALESCE(column_name, column_name, column_name, '') FROM table_name WHERE condition
``` 

### **Temporal Tables**: 
``` sql
SELECT  column_name
INTO #temporal_table
FROM table_name

SELECT column_name
FROM #temporal_table
``` 

### **DATEADD**: To add or subtract a value to get a new date
``` sql
SELECT DATEADD(DATEPART, number, 'dd-mm-yyyy') FROM table_name WHERE condition
```

### **dbo.Calendar**: Calendar table
``` sql

```

### **DATEDIFF**: Get difference
``` sql
SELECT DATEADD(DATEPART, 'dd-mm-yyyy', 'dd-mm-yyyy') FROM table_name WHERE condition
```

### **ROUND**: Get difference
``` sql
SELECT ROUND(number, lenght, lenght_truncate) FROM table_name WHERE condition
```

### **ABS**: absolute
``` sql
SELECT ABS(column_name) FROM table_name WHERE condition
```

### **SQRT**: 
``` sql
SELECT SQRT(column_name) FROM table_name WHERE condition
```

### **SQUARE**: 
``` sql
SELECT SQUARE(column_name) FROM table_name WHERE condition
```

### **LOG**
``` sql
SELECT LOG(column_name) FROM table_name WHERE condition
```

### **stdev**
``` sql
SELECT stdev(column_name) OVER(ORDER BY column_name) FROM table_name WHERE condition
```

### **CTE** 
``` sql
WITH cte_name (new_column) 
  AS (SELECT column as new_column FROM table_name WHERE condition)
   
SELECT new_column FROM cte_name WHERE condition
```

## Grouping

### **ROLLUP or CUBE**:
``` sql
  SELECT column_name,
         column_name_2,
         SUM(column_name_sum)
    FROM table_name
GROUP BY
	 column_name,
	 column_name_2
WITH ROLLUP
WHERE condition;
```

### **GROUPING SETS**:
``` sql
  SELECT column_name,
         column_name_2,
         SUM(column_name_sum)
    FROM table_name
GROUP BY GROUPING SETS
(
  	-- Group in hierarchical order
	(column_name, column_name_2),
  	-- Group by 
	(column_name),
	-- Is weekend or not
	(c.IsWeekend),
        -- This remains blank; it gives us the grand total
	()
)
WHERE condition;
```

### **High and Low precision dates**: 
``` sql
SELECT
	-- High Precision
	CONVERT(date, SYSDATETIME()) AS [SYSDATETIME],
	CONVERT(date, SYSDATETIMEOFFSET()) AS [SYSDATETIMEOFFSET],
	CONVERT(date, SYSUTCDATETIME()) AS [SYSUTCDATETIME],
	-- Low Precision
	CONVERT(date, CURRENT_TIMESTAMP) AS [CURRENT_TIMESTAMP],
	CONVERT(date, GETDATE()) AS [GETDATE],
	CONVERT(date, GETUTCDATE()) AS [GETUTCDATE];
```


### **Other functions**:
* NULLIF(column_name, string)
* PERCENTILE_COUNT(0.5)	WITHIN GROUP (ORDER BY someval) OVER()	--median, not performance friendly

### **APPLY** 


### **UDF**: User Defined Functions
``` sql
CREATE FUNCTION GetYesterday()
RETURNS DATE
AS
BEGIN

RETURN(SELECT DATEADD(day, -1, GETDATE()))
END 
```


### **ITVF**: Inline statement table value function, best performance
``` sql
CREATE FUNCTION SumStationStats(@StartDate AS datetime)
RETURNS TABLE
AS
RETURN
SELECT column_name
	FROM table_name
	WHERE condition

```
### **MTVF**: Multi statement table value function
``` sql
CREATE FUNCTION CountTripAvgDuration (@Month CHAR(2), @Year CHAR(4))
RETURNS @DailyTripStats TABLE(
	TripDate	date,
	TripCount	int,
	AvgDuration	numeric)
AS
BEGIN

	INSERT INTO @DailyTripStats
	SELECT column_name
	FROM table_name
	WHERE condition

RETURN 
END
```

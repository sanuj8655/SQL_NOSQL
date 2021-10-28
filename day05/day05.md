# MySQL - RDBMS

## Agenda
* SQL Functions
	* Numeric Functions
	* Control Functions
	* List Functions
	* NULL related Functions
	* Group Functions
* GROUP BY clause
* HAVING clause

## SQL Functions

### Numeric Functions

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

HELP SIGN;

SELECT SIGN(-82.9), SIGN(0), SIGN(72.20);

SELECT ABS(34.5), ABS(-723.45);

SELECT POWER(2, 10), POWER(2, 0.5);

-- floor is immediately lower integer for given number.
SELECT FLOOR(3.67), FLOOR(-3.67);
-- 3, -4

-- ceil is immediately higher integer for given number.
SELECT CEIL(3.67), CEIL(-3.67);
-- 4, -3

-- round() fn rounds given number upto give decimal places.
-- 	if last digit >= 5, then round up; otherwise round down.
SELECT ROUND(3.1415, 3), ROUND(3.1415, 2);
-- 	3.1415 upto 3 places of decimal --> 3.142
-- 	3.1415 upto 2 places of decimal --> 3.14

SELECT ROUND(12345.67, -2), ROUND(123456.78, -2);
-- 12345.67 -- -2 means to nearest multiple of 100 --> 12300
-- 123456.78 -- -2 means to nearest multiple of 100 --> 123500
```

### Control Functions
* IF(condition, expression if true, expression if false)

```SQL
-- print empno, ename, sal and category. If sal > 2500, then RICH; otherwise POOR.
SELECT empno, ename, sal, IF(sal > 2500, 'RICH', 'POOR') AS category FROM emp;

-- print empno, ename, sal and category. If sal < 1500, then POOR, if sal between 1500 AND 2500 then MIDDLE, otherwise RICH.
SELECT empno, ename, sal,
IF(sal < 1500, 'POOR', IF(sal <= 2500, 'MIDDLE', 'RICH')) category
FROM emp;
```

### List Functions
* Input multiple (variable) arguments and return a single result.
* The multiple values are from the same row.

```SQL
SELECT CONCAT('Nilesh', ' @ ', 'Sunbeam', ' Infotech');

SELECT CONCAT(ename, ' is a ', job, ' with sal ', sal) FROM emp;

SELECT LEAST(82, 23, 582, 272, 53);
-- returns min value from multiple args given.

SELECT GREATEST(82, 23, 582, 272, 53);
-- returns max value from multiple args given.

SELECT ename, job, LEAST(ename, job) FROM emp;

SELECT CONCAT(23, NULL, 'HELLO'), LEAST(823, 73, NULL), GREATEST(NULL, 76, 838);
-- NULL, NULL, NULL -- if any arg is NULL, result will be NULL.

-- COALESCE is a list function (variable args) that returns first NON-NULL value.
SELECT COALESCE(NULL, NULL, 12), COALESCE(NULL, 'Sunbeam', 3.142), COALESCE('2021-07-26', NULL, 7229);
-- 12, Sunbeam, 2021-07-26

SELECT ename, comm, sal, COALESCE(comm, sal) FROM emp;
-- display ename, comm, sal and comm if non-null, otherwise sal in last column.
```

### NULL related functions
* NULL is special value represent missing or non-applicable values.
* Few operators and functions are designed to work with NULL values.
* Operators: IS NULL, IS NOT NULL, <=>
* functions: COALESCE, ISNULL, IFNULL, NULLIF.

```SQL
-- ISNULL() returns 1 if null value, otherwise 0 -- similar to IS NULL operator.
SELECT ename, comm, ISNULL(comm) FROM emp;

-- IFNULL() returns the arg2 value if arg1 is NULL; otherwise arg1.
SELECT ename, comm, IFNULL(comm, 0.0) FROM emp;

SELECT ename, sal, comm, sal + comm AS income FROM emp;

SELECT ename, sal, comm, sal + IFNULL(comm, 0.0) AS income FROM emp;

-- NULLIF() if two values are equal, return NULL; otherwise return value1.
SELECT NULLIF(100, 100), NULLIF(100, 200);
```

### GROUP Functions
* Also called multi-row functions or aggregate functions.
* Used for aggregating/summerizing values from multiple rows.
* Most of these functions take single argument i.e. name of column to be aggregated
* Example:
	* SUM()
	* AVG()
	* COUNT()
	* MIN()
	* MAX()

```SQL
SELECT sal FROM emp;

SELECT SUM(sal), AVG(sal), COUNT(sal), MIN(sal), MAX(sal) FROM emp;

SELECT SUM(comm), AVG(comm), COUNT(comm), MIN(comm), MAX(comm) FROM emp;
-- null values are ignored by group functions
```

#### Change sql_mode in MySQL

* @@sql_mode is a MySQL system variable.

```SQL
SELECT @@sql_mode;
```

* step 1. Open Notepad with admin rights (Run as Adminstrator).
* step 2. Open MySQL config file in notepad. C:\ProgramData\MySQL\MySQL Server 8.0\my.ini
* step 3. In the file replace "sql-mode" full line by
	* sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ONLY_FULL_GROUP_BY"
* step 4. Save the file and close notepad.
* step 5. Run --> services.msc
* step 6. Restart MySQL service.
* step 7. Exit the MySQL CLI and start it again.
* step 8. SELECT @@sql_mode;

#### Limitations of GROUP functions

```SQL
-- cannot use GROUP function with a column.
SELECT ename, SUM(sal) FROM emp;

-- cannot use GROUP function with single row function.
SELECT LOWER(ename), SUM(sal) FROM emp;

-- cannot use GROUP function in WHERE clause.
SELECT * FROM emp WHERE sal = MAX(sal);

-- cannot use a group function in another group function.
SELECT MAX(SUM(sal)) FROM emp;
```

## GROUP BY clause

```SQL
SELECT job, SUM(sal) FROM emp;
-- error

-- display total sal for each job.
SELECT job, SUM(sal) FROM emp GROUP BY job;

-- display avg sal for each dept.
SELECT deptno, AVG(sal) FROM emp GROUP BY deptno;

-- display total, avg, min, max and count for each dept.
SELECT deptno, SUM(sal), AVG(sal), MIN(sal), MAX(sal), COUNT(sal) FROM emp GROUP BY deptno;

-- display number of employees for each job profile.
SELECT job, COUNT(empno) FROM emp GROUP BY job;

-- display number of employees for each dept.
SELECT deptno, COUNT(empno) FROM emp GROUP BY deptno;

-- display number of employees for each job in each dept.
SELECT deptno, job, COUNT(empno) FROM emp GROUP BY deptno, job;

SELECT deptno, job, COUNT(empno) FROM emp GROUP BY deptno, job ORDER BY deptno, job;
```

```SQL
SELECT job, COUNT(empno) FROM emp GROUP BY job;

SELECT COUNT(empno) FROM emp GROUP BY job;
-- allowed syntax, but not much useful.

SELECT job, COUNT(empno) FROM emp GROUP BY deptno;
-- not allowed

```

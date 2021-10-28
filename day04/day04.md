# MySQL - RDBMS

## Agenda
* UPDATE
* DELETE
* TRUNCATE
* DROP
* HELP
* DUAL table
* SQL Functions
	* String Functions
	* Date and Time Functions
	* Information Functions

## UPDATE

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

SELECT * FROM books;

UPDATE books SET price = 230.546 WHERE id = 1001;

SELECT * FROM books;

-- increase price of all C Programming books by 5%.
UPDATE books SET price = price + price * 0.05 WHERE subject = 'C Programming';

SELECT * FROM books;

-- increase price of all books by 10 %.
UPDATE books SET price = price + price * 0.10;

SELECT * FROM books;
```

## DELETE, TRUNCATE and DROP

```SQL
SELECT * FROM books;

-- delete all books whose price > 1000.0
DELETE FROM books WHERE price > 1000.0;

SELECT * FROM books;

-- delete all C Programming books.
DELETE FROM books WHERE subject = 'C Programming';

SELECT * FROM books;

SHOW TABLES;

SELECT * FROM students1;

-- delete all rows from students1 (DML operation -- slower)
DELETE FROM students1; 

SELECT * FROM students1;

SELECT * FROM students2;

-- delete all rows from students2 (DDL operation -- faster)
TRUNCATE TABLE students2;

SELECT * FROM students2;

SHOW TABLES;

DESCRIBE students1;
DESCRIBE students2;

DESCRIBE temp;

DROP TABLE temp;

SHOW TABLES;

DESCRIBE temp;
-- error
```

## HELP

```SQL
HELP SELECT;

HELP CREATE;

HELP CREATE TABLE;

HELP Functions;

HELP Numeric Functions;

HELP String Functions;

HELP Date and Time Functions;

HELP UPPER;
```

## DUAL table

```SQL
-- SELECT expr, ... FROM table;

DESCRIBE DUAL;
-- error
SELECT * FROM DUAL;
-- error

-- DUAL table should be used for arbitrary calculations or SQL functions testing.
SELECT 2 + 3 * 4 FROM DUAL;
SELECT NOW() FROM DUAL;
SELECT USER(), DATABASE() FROM DUAL;

-- DUAL is accepted syntax in ANSI SQL, but not mandetory.
SELECT 2 + 3 * 4;
SELECT NOW();
SELECT USER(), DATABASE();
```

## SQL Functions

### String Functions

```SQL
SELECT UPPER('SunBeam'), LOWER('SunBeam');

SELECT name FROM books;

SELECT name, UPPER(name), LOWER(name) FROM books;

SELECT name, LENGTH(name) FROM books;

SELECT CONCAT('James', 'Bond', 7);

SELECT CONCAT(ename, ' - ', job) FROM emp;

SELECT LTRIM('   Sunbeam   ') AS l, RTRIM('   Sunbeam   ') AS r, TRIM('   Sunbeam   ') AS lr;

SELECT LPAD('SunBeam', 10, '*'), RPAD('SunBeam', 10, '*');

SELECT RPAD( LPAD('SunBeam', 10, '*'), 13, '*' );

SELECT SUBSTRING('SunBeamInfotech', 8, 4);
-- get next 4 chars from 8th position (from left)

SELECT SUBSTRING('SunBeamInfotech', -12, 4);
-- get next 4 chars from 12th position (from right)

SELECT SUBSTRING('SunBeamInfotech', 4, -2);
-- In MySQL, cannot give length 0 or -ve. If given, result is blank.

-- get first and last char of a string.
SELECT SUBSTRING('SUNBEAM', 1, 1), SUBSTRING('SUNBEAM', -1, 1);
SELECT LEFT('SUNBEAM', 1), RIGHT('SUNBEAM', 1);

-- find all emps whose name start from 'J' to 'S'
SELECT * FROM emp WHERE LEFT(ename, 1) BETWEEN 'J' AND 'S';

-- find all emps whose name first and last letter is same.
SELECT * FROM emp WHERE LEFT(ename, 1) = RIGHT(ename, 1);

-- delete all emps whose name is of single letter.
DELETE FROM emp WHERE LENGTH(ename) = 1;

-- convert all book names and subject names in upper case.
UPDATE books SET name=UPPER(name), subject=UPPER(subject);
SELECT * FROM books;

SELECT ASCII('A'), CHAR(65 USING ASCII);
SELECT ASCII('ABCD'); -- show ASCII value of first char only

SELECT ('SunBeam' = 'SUNBEAM') AS cmp1, (BINARY('SunBeam') = BINARY('SUNBEAM')) AS cmp2;
-- cmp1 --> case insensitive cmp, cmp2 --> case sensitive.

SELECT REPLACE('SunBeam', 'Be', 'Cre');
```

### Date and Time Functions
* DATE (3 bytes) --> 01-01-1000 to 31-12-9999 ('YYYY-MM-DD')
* TIME (3 bytes) --> '-838:59:59' to '838:59:59' ('hhh:mm:ss')
* DATETIME (5 bytes) --> 01-01-1000 00:00:00 to 31-12-9999 23:59:59 ('YYYY-MM-DD hh:mm:ss')
* TIMESTAMP (4 bytes) --> '1970-01-01 00:00:01' UTC to '2038-01-19 03:14:07' (milliseconds since 1-Jan-1970 midnight)
* YEAR (1 byte) --> 1901 to 2155

```SQL
HELP Date and Time Functions;

SELECT NOW(), SYSDATE();

SELECT DAY(NOW()), MONTH(NOW()), YEAR(NOW()), HOUR(NOW()), MINUTE(NOW()), SECOND(NOW());
SELECT DATE(NOW()), TIME(NOW());

-- find all emps hired in year 1981
SELECT * FROM emp WHERE YEAR(hire) = 1981;

-- display ename, hire date and hire date in format dd-MMM-yyyy.
SELECT ename, hire, DATE_FORMAT(hire, '%d-%b-%Y') FROM emp;

-- display your birth date in format Weekday, day-Month-year.
SELECT DATE_FORMAT('1983-09-28', '%W, %d-%M-%Y');

-- display your age (in years).
SELECT TIMESTAMPDIFF(YEAR, '1983-09-28', NOW());

-- find experience of all emps in years & months.
SELECT ename, hire, TIMESTAMPDIFF(YEAR, hire, NOW()) y, TIMESTAMPDIFF(MONTH, hire, NOW()) m FROM emp;

SELECT ename, hire, TIMESTAMPDIFF(YEAR, hire, NOW()) y, TIMESTAMPDIFF(MONTH, hire, NOW()) % 12 m FROM emp;
```

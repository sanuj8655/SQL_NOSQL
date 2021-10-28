# Database Technologies

## Agenda
* MySQL internals (Physical layout)
* MySQL data types
* CREATE TABLE
* DESCRIBE TABLE
* INSERT
* SELECT

## Assign 1

* Assuming that sales db is created and permissions are assigned to the dbda user.

* On terminal
	* mysql -u username -ppassword

	```sh
	mysql -u dbda -pdbda
	```
* On mysql> prompt

	```SQL
	SHOW DATABASES;

	USE sales;

	SHOW TABLES;

	SELECT * FROM customers;
	SELECT * FROM salespeople;
	SELECT * FROM orders;
	```

## Table structure

* terminal> mysql -u dbda -pdbda

	```SQL
	USE classwork;

	SELECT USER(), DATABASE();

	SHOW TABLES;

	DESCRIBE emp;
	-- display table structure/metadata
	-- columns, data-types, extra info (nullable, keys, default value, extra)

	DESCRIBE dept;
	
	SELECT * FROM dept;
	```

## CREATE TABLE

```
CREATE TABLE tablename (col1 datatype1, col2 datatype2, col1 datatype2, ...);
```

```SQL
SELECT USER(), DATABASE();
-- dbda -- classwork

CREATE TABLE students(roll INT, name CHAR(30), marks DOUBLE);
-- create a table "students" with three columns roll, name and marks.
-- SQL is not case sensitive (except table names are case sensitive on linux).
-- My (Nilesh) convention: keywords/functions in capital and table/column names in small case.

DESCRIBE students;

SELECT * FROM students;
```

## Data types

```SQL
CREATE TABLE temp(c1 CHAR(4), c2 VARCHAR(4), c3 TEXT(4));

DESCRIBE temp;

INSERT INTO temp VALUES ('abcd', 'abcd', 'abcdef');
-- okay
INSERT INTO temp VALUES ('a', 'b', 'ab');
-- okay
INSERT INTO temp VALUES ('abcde', 'abc', 'abc');
-- error: Data too long for column 'c1'
INSERT INTO temp VALUES ('abc', 'abcde', 'abc');
-- error: Data too long for column 'c2'

SELECT * FROM temp;

INSERT INTO temp VALUES ('A*B', 'A$B#', 'A%^()BCD');
SELECT * FROM temp;
```

## INSERT
* In SQL use string (chars) and date-time in single quotes. Other data types no quotes to be given.

```
INSERT INTO tablename VALUES (col1_value, col2_value, col3_value, ...);

INSERT INTO tablename VALUES (col1_value, col2_value, col3_value, ...), (col1_value, col2_value, col3_value, ...), (col1_value, col2_value, col3_value, ...), ...;
```

```SQL
DESCRIBE students;

INSERT INTO students VALUES (1, 'Steve', 98.40);
INSERT INTO students VALUES (2, 'Bill', 89.30);
-- insert a row -- give all column values sequentially (as in order while creation of table).

INSERT INTO students VALUES (3, 'Mark', 86.10), (4, 'Linus', 97.80), (5, 'Dennis', 99.10);
-- insert 3 rows -- column values to be given sequentially in each row.

INSERT INTO students VALUES ('Richard', 88.50, 6);
-- error: 'Richard' not compatible with INT type.

INSERT INTO students (name, marks, roll) VALUES ('Richard', 88.50, 6);
-- insert a row -- column values to be given in sequence mentioned after table name.

INSERT INTO students VALUES (7, 'Ken');
-- error: number columns in the row are less.

INSERT INTO students (roll, name) VALUES (7, 'Ken');
-- insert a row -- less columns -- follow sequence mentioned after table name.
-- the remaining columns will have NULL value.

SELECT * FROM students;
```

```SQL
CREATE TABLE students1 (roll INT, name CHAR(30), marks DOUBLE);

SELECT * FROM students1;

INSERT INTO students1 SELECT * FROM students;
-- copy all rows from students table into students1 table
-- expected that structure (col seq & type) is same for both the tables.

SELECT * FROM students1;
```

```SQL
CREATE TABLE students2 AS SELECT * FROM students;
-- create a new table students2 with same structure as of students table.
-- copy all rows from students table into students2 table.

DESCRIBE students2;

SELECT * FROM students2;
```

## SELECT 

```
SELECT columns FROM tablename;
```

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

DESCRIBE emp;

SELECT * FROM emp;
-- * means all columns -- fixed order -- as of table creation

SELECT empno, ename, job, sal FROM emp;
-- fetch given columns -- order mentioned in SELECT.

SELECT empno, ename, sal, sal * 0.5, sal * 0.2 FROM emp;
-- assuming da = 50% of sal, ta = 20% of sal.

SELECT empno, ename AS empname, sal, sal * 0.5 AS da, sal * 0.2 AS ta FROM emp;
-- column aliases can be given using AS keyword after column name.

SELECT empno, ename `emp name`, sal, sal * 0.5 da, sal * 0.2 ta FROM emp;
-- AS keyword is optional while giving column aliases.

```


# MySQL - RDBMS

## Agenda
* DCL - GRANT, REVOKE
* Indexes
* Query performance
* Constraints

## SQL (Pending)
* DQL
* DML
* DDL - ALTER
* DCL - GRANT, REVOKE
* TCL - COMMIT, ROLLBACK, SAVEPOINT

## Data Control Language 

```SQL
-- terminal1 - login with root user.
SELECT USER(), DATABASE();
-- root, NULL

-- list all users (from system database - mysql.user)
DESCRIBE mysql.user;
SELECT user, host FROM mysql.user;

-- create database users.
CREATE USER user1@localhost IDENTIFIED BY 'user1';
CREATE USER user2@localhost IDENTIFIED BY 'user2';
CREATE USER user3@'%' IDENTIFIED BY 'user3';
CREATE USER user4@localhost IDENTIFIED BY 'user4';

SELECT user, host FROM mysql.user;

-- change user password
ALTER USER user4@localhost IDENTIFIED BY 'newuser4';
FLUSH PRIVILEGES;

-- drop the user
DROP USER user4@localhost;

SELECT user, host FROM mysql.user;
```

```SQL
-- terminal1 - 
SELECT USER(), DATABASE();
-- root, NULL

GRANT ALL PRIVILEGES ON classwork.* TO user3@'%' WITH GRANT OPTION;
-- user3 is given full permissions on classwork database.
-- also user3 is given permission to give permissions to the other users -- WITH GRANT OPTION.

FLUSH PRIVILEGES;
```

```SQL
-- terminal2 -- login with user3
SELECT USER(), DATABASE();
-- user3, NULL

SHOW DATABASES;

USE classwork;

SHOW TABLES;

CREATE TABLE test(id INT, val VARCHAR(40));

INSERT INTO test VALUES (1, 'A'), (2, 'B'), (3, 'C');

SELECT * FROM test;

-- assign CRUD permissions to user1 with grant OPTION.
GRANT SELECT, UPDATE, INSERT ON classwork.* TO user1@localhost WITH GRANT OPTION;

```

```SQL
-- terminal3 -- login with user1
SELECT USER(), DATABASE();
-- user1, NULL

SHOW DATABASES;

USE classwork;

SHOW TABLES;

SELECT * FROM dept;

SELECT * FROM test;

INSERT INTO test VALUES (4, 'D'), (5, 'E');

DELETE FROM test;
-- error: DELETE command denied to user 'user1'@'localhost'.

CREATE TABLE temp (id INT);
-- error: CREATE command denied to user 'user1'@'localhost'

SHOW GRANTS;

GRANT SELECT, UPDATE, DELETE TO user2@localhost ON classwork.*;
-- error: Illegal authorization identifier.

GRANT SELECT, UPDATE ON classwork.test TO user2@localhost ;
GRANT SELECT, UPDATE, INSERT ON classwork.dept TO user2@localhost ;
GRANT SELECT, INSERT ON classwork.v_empincome TO user2@localhost ;

```


```SQL
-- terminal 4 -- user2 login
SELECT USER(), DATABASE();
-- user2, NULL

SHOW DATABASES;

USE classwork;

SHOW FULL TABLES;

SELECT * FROM v_empincome;

DESCRIBE v_empincome;

SHOW CREATE VIEW v_empincome;
-- SHOW VIEW command denied

SHOW GRANTS;

GRANT SELECT ON classwork.v_empincome TO dev@localhost;
-- (assuming dev user is created)
-- error: GRANT command denied 
```

```SQL
-- terminal 3 -- user1 login
SELECT USER(), DATABASE();

REVOKE SELECT, INSERT ON classwork.v_empincome FROM user2@localhost;
```

```SQL
-- terminal4 -- user2 login
SHOW TABLES;
```


```SQL
-- terminal1 -- root user
SELECT USER(), DATABASE();

-- give full permissions to dbda user -- *.* = system level
GRANT ALL ON *.* TO dbda@localhost WITH GRANT OPTION;
-- now dbda user is having almost all admin privileges. this is not recommneded.
```

```SQL
-- terminal5 -- dbda login
SELECT USER(), DATABASE();

SHOW DATABASES;

SELECT user, host FROM mysql.user;
```

### FLUSH PRIVILEGES
* If root/authorized user use "DML" queries on system tables (e.g. mysql.tables_priv, mysql.columns_priv, mysql.user, ...) to change security settings like password, privileges then FLUSH PRIVILEGES is mandetory.
* This command reloads security settings into server memory and synchronize with clients.
* If user security related commands (DCL) like GRANT, REVOKE, ALTER USER then FLUSH PRIVILEGES is not mandetory. These commands does it internally. 

## Query performance
* RDBMS EXPLAIN statement show query performance.
* cost="some-number" -- represent query execution speed (NOT the exact time milli-sec).
	* Higher the number, less is the performance.
	* Lesser the number, higher is the performance.
* EXPLAIN
	* FORMAT=JSON
	* FORMAT=TREE
	* FORMAT=TRADITIONAL
* EXPLAIN statement also shows the query execution plan (in technical words -- optimization settings, internal data structures, etc.)
* EXPLAIN output is very much RDBMS specific.

## Index
* Indexes are used to speed up execution of the search, sort and group queries.

### Simple index
* Index of single column.

```SQL
-- dbda login -- classwork database
SELECT USER(), DATABASE();

SELECT * FROM books WHERE subject = 'OPERATING SYSTEMS';

EXPLAIN FORMAT=JSON
SELECT * FROM books WHERE subject = 'OPERATING SYSTEMS';
-- 1.05 --> "access_type": "ALL",  "attached_condition": "(`classwork`.`books`.`subject` = 'OPERATING SYSTEMS')"

CREATE INDEX idx_book_subject ON books(subject);

SELECT * FROM books WHERE subject = 'OPERATING SYSTEMS';

EXPLAIN FORMAT=JSON
SELECT * FROM books WHERE subject = 'OPERATING SYSTEMS';
-- 0.80 -->  "access_type": "ref",  "key": "idx_book_subject", "used_key_parts": [ "subject" ]
```

```SQL
SELECT * FROM books;

EXPLAIN FORMAT=JSON
SELECT * FROM books WHERE author = 'Herbert Schildt';
-- 1.05 -- ALL

CREATE INDEX idx_book_author ON books(author DESC);

EXPLAIN FORMAT=JSON
SELECT * FROM books WHERE author = 'Herbert Schildt';
-- 0.70 -- keys: "idx_book_author"
```

### Composite index
* Index on multiple columns.

```SQL
EXPLAIN FORMAT=JSON
SELECT * FROM books
WHERE subject = 'JAVA PROGRAMMING' AND author = 'Herbert Schildt';
-- 0.70

CREATE INDEX idx_book_sub_au ON books(subject ASC, author DESC);

EXPLAIN FORMAT=JSON
SELECT * FROM books
WHERE subject = 'JAVA PROGRAMMING' AND author = 'Herbert Schildt';
-- 0.35

EXPLAIN FORMAT=JSON
SELECT * FROM books
WHERE author = 'Herbert Schildt' AND subject = 'JAVA PROGRAMMING';
-- changing sequence of search may change cost of the query -- however modern RDBMS does optimization.
```

```SQL
EXPLAIN FORMAT=JSON
SELECT * FROM books
WHERE subject = 'JAVA PROGRAMMING' AND author = 'Herbert Schildt' AND price > 400;
-- 0.35
```

### Unique index
* Unique index ensure that value is not duplicated in indexed column.

```SQL
SELECT * FROM emp;

CREATE UNIQUE INDEX idx_emp_ename ON emp(ename);

EXPLAIN FORMAT=JSON
SELECT * FROM emp WHERE ename = 'KING';
-- 1.00

INSERT INTO emp(empno, ename) VALUES (1009, 'KING');
-- error: Duplicate entry 'KING' for key 'emp.idx_emp_ename'

INSERT INTO emp(empno, ename) VALUES (1009, NULL);
-- adding (multiple) NULL value in the column is allowed.

CREATE UNIQUE INDEX idx_emp_job ON emp(job);
-- error: Duplicate entry 'SALESMAN' for key 'emp.idx_emp_job'
```

```SQL
CREATE TABLE stud(std INT, roll INT, sname VARCHAR(30), marks DOUBLE);

INSERT INTO stud VALUES
(1, 1, 'Soham', 98.30),
(1, 2, 'Sakshi', 97.40),
(1, 3, 'Prisha', 99.40),
(2, 1, 'Madhura', 99.20);

CREATE UNIQUE INDEX idx_stud ON stud(std, roll);

INSERT INTO stud VALUES (2, 2, 'Paris', 96.39);

INSERT INTO stud VALUES (1, 2, 'Om', 97.39);
-- error: Duplicate entry '1-2' for key 'stud.idx_stud'

DROP TABLE stud;
```

### Clustered index
* In MySQL, internally each row of the table is identified by a unique id/index called as "clustered index".
* This unique id is mapped to row address on the disk. This id is used to refer rows in other places.
* The clustered index is based on primary key (unique & non-null) of the table i.e. value of PK in each row.
* If PK is not present in the table, a hidden column is added (synthesized) in the table that act as PK and clustered index is created based on it.

```SQL
DESCRIBE books;

DESCRIBE emp;
```

### Display index & Drop index

```SQL
SHOW INDEXES FROM emp;

DROP INDEX idx_emp_ename ON emp;
```

### Advantages
* Faster searching, sorting queries.
* Unique records (UNIQUE index).

```SQL
EXPLAIN FORMAT = JSON
SELECT e.ename, d.dname FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno;
-- 7.70

CREATE UNIQUE INDEX idx_dept ON dept(deptno);
CREATE INDEX idx_emp ON emp(deptno);

EXPLAIN FORMAT = JSON
SELECT e.ename, d.dname FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno;
-- 4.01
```

### Disadvantages
* Indexes occupy space on server disk (BTREE or HASH).
* Index creation is time consuming. (Usually index creation is done as maintence task).
* Indexes are updated on each DML. DML will be slower.

## Constraints

```
CREATE TABLE tblname(
	col1 TYPE1 column_level_constraint,
	col2 TYPE2 column_level_constraint,
	col3 TYPE3,
	table_level_constraint1,
	CONSTRAINT constraint_name table_level_constraint2
);
```

```SQL
CREATE TABLE contacts(
	cname VARCHAR(30) NOT NULL,
	email CHAR(40) UNIQUE NOT NULL,
	phone CHAR(12) UNIQUE,
	addr VARCHAR(40),
	age INT CHECK (age > 18)
);

DROP TABLE contacts;
```

```SQL
CREATE TABLE contacts(
	cname VARCHAR(30) NOT NULL,
	email CHAR(40) NOT NULL,
	phone CHAR(12),
	addr VARCHAR(40),
	age INT,
	UNIQUE(email),
	UNIQUE(phone),
	CHECK (age > 18)
);

DESCRIBE contacts;

SHOW INDEXES FROM contacts;

INSERT INTO contacts (cname, email) VALUES ('James', 'james@bond.com');

INSERT INTO contacts (cname, phone) VALUES ('Abhishek', '9822012345');
-- error: email cannot be NULL.

INSERT INTO contacts (cname, email) VALUES ('Bond', 'james@bond.com');
-- error: email cannot be duplicated

INSERT INTO contacts (cname, email, age) VALUES ('bheem', 'chhota@bheem.com', 15);
-- error: age must be > 18
```

```SQL
CREATE TABLE stud(
	std INT,
	roll INT,
	sname VARCHAR(30),
	marks DOUBLE,
	CONSTRAINT uni_std_roll UNIQUE(std,roll)
);
-- internally creates UNIQUE Composite index i.e. combination cannot be duplicated.
-- constraint name is optional.

SHOW INDEXES FROM stud;

INSERT INTO stud VALUES
(1, 1, 'Soham', 98.30),
(1, 2, 'Sakshi', 97.40),
(1, 3, 'Prisha', 99.40),
(2, 1, 'Madhura', 99.20);
```



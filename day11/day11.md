# MySQL - RDBMS

## Agenda
* Constraints
	* Primary Key
	* Foreign Key
* Normalization

## Constraints

### Primary Key
* Unique identity for each row in RDBMS.

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

DROP TABLE IF EXISTS contacts;

CREATE TABLE contacts(
	cname VARCHAR(30),
	email CHAR(30) PRIMARY KEY,
	phone CHAR(12) UNIQUE,
	addr VARCHAR(40),
	age INT
);

DESCRIBE contacts;

DROP TABLE contacts;
```

```SQL
CREATE TABLE contacts(
	cname VARCHAR(30),
	email CHAR(30),
	phone CHAR(12) UNIQUE,
	addr VARCHAR(40),
	age INT,
	PRIMARY KEY (email)
);

DESCRIBE contacts;

DROP TABLE contacts;
```

```SQL
CREATE TABLE contacts(
	cname VARCHAR(30),
	email CHAR(30),
	phone CHAR(12) UNIQUE,
	addr VARCHAR(40),
	age INT
);

DESCRIBE contacts;

ALTER TABLE contacts ADD PRIMARY KEY (email);
-- ALTER TABLE contacts ADD CONSTRAINT pk_contact PRIMARY KEY (email);

DESCRIBE contacts;

INSERT INTO contacts(cname, email, phone) VALUES ('Nilesh', 'nilesh@sunbeaminfo.com', '9527331338');
INSERT INTO contacts(cname, email, phone) VALUES ('Nitin', 'nitin@sunbeaminfo.com', '9881208115');
INSERT INTO contacts(cname, email, phone) VALUES ('Prashant', 'pranshat.lad@sunbeaminfo.com', '9881208114');
INSERT INTO contacts(cname, email, phone) VALUES ('Prakash', 'pranshat.lad@sunbeaminfo.com', '9881208113');
-- error: PK cannot be duplicated
INSERT INTO contacts(cname, email, phone) VALUES ('Prakash', NULL, '9881208113');
-- error: PK cannot be NULL

SHOW INDEXES FROM contacts;

ALTER TABLE contacts DROP PRIMARY KEY;

SHOW INDEXES FROM contacts;

DESCRIBE contacts;
```

```SQL
DROP TABLE IF EXISTS stud;

CREATE TABLE stud(
	std INT PRIMARY KEY,
	roll INT PRIMARY KEY,
	sname VARCHAR(30),
	marks DOUBLE,
);
-- error: Composite PK cannot be given on column level.

CREATE TABLE stud(
	std INT,
	roll INT,
	sname VARCHAR(30),
	marks DOUBLE,
	PRIMARY KEY(std,roll)
);

DESCRIBE stud;

SHOW INDEXES FROM stud;

INSERT INTO stud VALUES
(1, 1, 'Soham', 98.30),
(1, 2, 'Sakshi', 97.40),
(1, 3, 'Prisha', 99.40),
(2, 1, 'Madhura', 99.20);

INSERT INTO stud VALUES (1, 1, 'Shloka', 99.90);
```

```SQL
CREATE TABLE menu(
	id INT PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(40),
	category VARCHAR(30),
	price DOUBLE,
	description VARCHAR(100)
);

DESCRIBE menu;

INSERT INTO menu (name, category, price) VALUES ('Roti', 'Veg - Main course', 30.0);
INSERT INTO menu (name, category, price) VALUES ('Nan', 'Veg - Main course', 35.0);
INSERT INTO menu (name, category, price) VALUES ('Paneer Tikka', 'Veg - Main course', 280.0);

SELECT * FROM menu;

ALTER TABLE menu AUTO_INCREMENT = 100;

INSERT INTO menu (name, category, price) VALUES ('Palak Paneer', 'Veg - Main course', 250.0);
INSERT INTO menu (name, category, price) VALUES ('Mutter Paneer', 'Veg - Main course', 260.0);
SELECT * FROM menu;
```

### Foreign Key
* Table relations --> 1:1, 1:M, M:1, M:M
	* dept (1) <---> (M) emp
		* dept (PK-deptno) <---> (FK-deptno) emp
* One To Many relations
	* Parent Table (1 row) ---> Child Table (M rows)
		* dept (1 dept) --> emp (M emps)
		* PK (deptno) --> FK (deptno)
* Tables can be related to each other logically (without specifying PK & FK in RDBMS).
* FK constraint will ensure that records can be added in child table only if corresponding PK is available in parent table.
* Syntax:
	* FOREIGN KEY parent_table (parent_table_pri_key)

```SQL
SELECT * FROM emps;

SELECT * FROM depts;

INSERT INTO emps VALUES (6, 'Vishal', 70, 4);
-- allowed: because no PK and FK is created in respective tables -- no constraint.
```

```SQL
DROP TABLE depts;

DROP TABLE emps;

CREATE TABLE depts (deptno INT PRIMARY KEY, dname VARCHAR(20));

INSERT INTO depts VALUES (10, 'DEV');
INSERT INTO depts VALUES (20, 'QA');
INSERT INTO depts VALUES (30, 'OPS');
INSERT INTO depts VALUES (40, 'ACC');

CREATE TABLE emps (
	empno INT PRIMARY KEY, 
	ename VARCHAR(20), 
	deptno INT,
	mgr INT,
	FOREIGN KEY (deptno) REFERENCES dept (deptno)
);
INSERT INTO emps VALUES (1, 'Amit', 10, 4);
INSERT INTO emps VALUES (2, 'Rahul', 10, 3);
-- okay: FK can be duplicated.
INSERT INTO emps VALUES (3, 'Nilesh', 20, 4);

INSERT INTO emps VALUES (4, 'Nitin', 50, 5);
-- error: Cannot add or update a child row: a foreign key constraint fails.
INSERT INTO emps VALUES (5, 'Sarang', 50, NULL);
-- error: Cannot add or update a child row: a foreign key constraint fails.

DESCRIBE emps;

SHOW INDEXES FROM emps;

INSERT INTO emps VALUES (6, 'Vishal', NULL, 4);
-- okay: FK can be NULL.

DELETE FROM depts WHERE deptno = 10;
-- error: cannot delete parent row if corresponding child rows are present in child table.

SELECT e.ename, d.dname FROM emps e
INNER JOIN depts d ON e.deptno = d.deptno;

EXPLAIN FORMAT=JSON
SELECT e.ename, d.dname FROM emps e
INNER JOIN depts d ON e.deptno = d.deptno;
-- PK & FK internally create indexes --> joins execute faster.

```



```SQL
SELECT * FROM stud;

DESCRIBE stud;
-- composite primary key

CREATE TABLE progress_card(
	std INT,
	roll INT,
	subject CHAR(20),
	marks DOUBLE,
	FOREIGN KEY (std, roll) REFERENCES stud (std, roll)
);

INSERT INTO progress_card VALUES
(1, 1, 'Eng', 19),
(1, 1, 'Hid', 18),
(1, 1, 'Math', 20),
(1, 1, 'EVS', 19);

INSERT INTO progress_card VALUES (3, 6, 'Eng', 20);
-- error: Cannot add or update a child row: a foreign key constraint fails
```

```SQL
CREATE TABLE depts_copy (deptno INT PRIMARY KEY, dname VARCHAR(20));

INSERT INTO depts_copy SELECT * FROM depts;

CREATE TABLE emps_copy (
	empno INT PRIMARY KEY, 
	ename VARCHAR(20), 
	deptno INT,
	mgr INT,
	FOREIGN KEY (deptno) REFERENCES dept (deptno)
);

SELECT @@foreign_key_checks;

SET @@foreign_key_checks = 0;
-- disable FK checks temporarily (until set again to 1 or EXIT current session).

SELECT @@foreign_key_checks;

INSERT INTO emps_copy SELECT * FROM emps;
-- very fast data transfer -- no FK checks.

INSERT INTO emps_copy VALUES (4, 'Nitin', 50, 5);
-- you should re-enable checks immediately to ensure valid data inserted next.
-- if not re-enabled, invalid data may get inserted.
-- here Nitin row is Orphan row -- corresponding parent row is absent. 

SET @@foreign_key_checks = 1;

SELECT * FROM emps_copy;
-- constraints are checked only while DML operations.
-- if wrong records were inserted earlier, they should be deleted/updated manually.

INSERT INTO emps_copy VALUES (5, 'Sarang', 50, NULL);
-- error: Cannot add or update a child row: a foreign key constraint fails
```

## Removing duplicates

```SQL
CREATE TABLE voters(
	id INT PRIMARY KEY AUTO_INCREMENT,
	vname VARCHAR(30),
	vemail VARCHAR(40)
);

INSERT INTO voters(vname, vemail) VALUES
('Nilesh', 'nilesh@sunbeaminfo.com'),
('Nitin', 'nitin@sunbeaminfo.com'),
('Vishal', 'vishal@sunbeaminfo.com'),
('Nilesh', 'nilesh@sunbeaminfo.com');

SELECT * FROM voters;

SELECT v1.id, v2.id, v1.vname, v2.vname, v1.vemail FROM voters v1
INNER JOIN voters v2 ON v1.vemail = v2.vemail
WHERE v1.id < v2.id;

DELETE v2 FROM voters v1
INNER JOIN voters v2 ON v1.vemail = v2.vemail
WHERE v1.id < v2.id;

SELECT * FROM voters;
```

## Table relations
* If all data is added in single table, the following problems occurs
	* Redaundency
	* Insert anomaly
	* Update anomaly
	* Delete anomaly
* The data is arranged into multiple tables. These tables are related to each other.
* Table/Database design process --> Normalization.
* Software development (SDLC)
	* Requirement analysis
	* Design --> Database design, UI design, OOP design, Architecture.
	* Development
	* Maintenance


# MySQL - RBDMS

## Agenda
* Regex - Phone
* HAVING clause
* WHERE + GROUP BY + ORDER BY
* Joins

## Regex - Phone

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

CREATE TABLE people (id INT, name VARCHAR(20), email VARCHAR(40), phone VARCHAR(14));

INSERT INTO people VALUES
(1, 'A', 'a@b.com', '9881208115'),
(2, 'B', 'b@c.com', '09881208114'),
(3, 'C', 'c@d.com', '+919527331338'),
(4, 'D', 'd@e.com', '98220123456'),
(5, 'E', 'e@f.com', '942201234'),
(6, 'F', 'f@g.com', 'ABCDE12345');

SELECT * FROM people WHERE phone REGEXP '[0-9]{10}';
-- match all rows having 10 digits or even more digits.

SELECT * FROM people WHERE phone REGEXP '^[0-9]{10}$';
-- match exactly 10 digits (not more or less).

SELECT * FROM people WHERE phone REGEXP '^(0|\\+91)[0-9]{10}$';
-- match exactly 10 digits with prefix 0 or +91.

SELECT * FROM people WHERE phone REGEXP '^(0|\\+91)?[0-9]{10}$';
-- match exactly 10 digits with prefix 0 or +91. and prefix may be present or absent.

SELECT * FROM people WHERE phone REGEXP '^(0|\\+91)?[7-9][0-9]{9}$';
-- match exactly 10 digits with prefix 0 or +91. and prefix may be present or absent.
-- mobile number always start from 7 or 8 or 9.
```

## GROUP BY

### HAVING clause
* HAVING clause is used to filter records based on condition on the "aggregate" result (e.g. sum, max, min, ...).
* Group functions cannot be used in WHERE clause.

```SQL
SELECT job, SUM(sal) FROM emp GROUP BY job;

-- find jobs with total sal less than 6000.
SELECT job, SUM(sal) FROM emp GROUP BY job HAVING SUM(sal) < 6000;

-- find dept with sum of sal is more than 10000.
SELECT deptno, SUM(sal) FROM emp GROUP BY deptno;
SELECT deptno, SUM(sal) FROM emp GROUP BY deptno HAVING SUM(sal) > 10000;

-- find job for which max sal is more than or equal to 3000.
SELECT job, MAX(sal) FROM emp GROUP BY job;
SELECT job, MAX(sal) FROM emp GROUP BY job HAVING MAX(sal) >= 3000.0;

-- find total sal for deptno 10 and 20.
SELECT deptno, SUM(sal) FROM emp GROUP BY deptno HAVING deptno IN (10, 20);
-- all rows are loaded in server memory.
-- sorted by deptno
-- grouped by deptno
-- aggregate operation i.e. sum() is performed
-- deptno 10 and 20 are filtered using HAVING.

SELECT deptno, SUM(sal) FROM emp WHERE deptno IN (10, 20) GROUP BY deptno;
-- rows of deptno 10 and 20 are loaded (WHERE clause).
-- sorted by deptno
-- grouped by deptno
-- aggregate operation i.e. sum() is performed
```

## SQL
* SQL is "declarative" query language.
* User/programmer will mention which operations to be done on the data, not how (sequence or implementation) those operations to be performed on the data.
* The implementation may vary from RDBMS vendor to vendor.
* ANSI standardize SQL syntax.
* Syntax: HELP SELECT;
	```
	SELECT expr1, expr2, ...
	FROM table
	WHERE condition_on_row
	GROUP BY column1, column2, ...
	HAVING condition_on_group
	ORDER BY column1, column2, ...
	LIMIT m,n;
	```

### Combination of clauses

```SQL
-- find jobs for which avg sal is more than 2000 for dept 20 and 30 emps.
SELECT job, sal, deptno FROM emp;

SELECT job, sal, deptno FROM emp
WHERE deptno IN (20, 30);

SELECT job, AVG(sal) FROM emp
WHERE deptno IN (20, 30)
GROUP BY job;

SELECT job, AVG(sal) FROM emp
WHERE deptno IN (20, 30)
GROUP BY job
HAVING AVG(sal) > 2000.0;
```

```SQL
-- find the dept that have max number of employees.
SELECT deptno, COUNT(empno) FROM emp
GROUP BY deptno;

SELECT deptno, COUNT(empno) FROM emp
GROUP BY deptno
ORDER BY COUNT(empno) DESC;

SELECT deptno, COUNT(empno) FROM emp
GROUP BY deptno
ORDER BY COUNT(empno) DESC
LIMIT 1;
```

```SQL
-- ORDER BY syntax can use expression, column name, alias or ordinal number (of projection).
SELECT deptno, COUNT(empno) FROM emp
GROUP BY deptno
ORDER BY COUNT(empno) DESC;

SELECT deptno, COUNT(empno) AS cnt FROM emp
GROUP BY deptno
ORDER BY cnt DESC;

SELECT deptno, COUNT(empno) AS cnt FROM emp
GROUP BY deptno
ORDER BY 2 DESC;
```

## Table Relations
* If all data is inserted into single table, data will be redaundent. It will waste lot of disk space.
* It will also suffer from problems.
	* INSERT anomaly -- same data need to be inserted multiple times.
	* UPDATE anomaly -- update operation need to be done on multiple rows (where data is duplicated).
	* DELETE anomaly -- delete operation may delete undersired data.
* Data is separated into multiple tables.
* Tables are related to each other 1:1, 1:M, M:1, M:M.

```SQL
SHOW TABLES;

SELECT * FROM emps;

SELECT * FROM meeting;

SELECT * FROM emp_meeting;
```

## Joins

### Cross Join

```Java
// class Emp and class Dept
empList = new ArrayList<>(); // 5 emps
deptList = new ArrayList<>(); // 4 depts
for(Emp e : empList) {
	for(Dept d : deptList) {
		sysout(e.ename, d.dname);
	}
}
// output -- 20 rows
```

```SQL
-- display each emp can be in which all possible depts. (print possible combinations)
SELECT e.ename, d.dname FROM emps e CROSS JOIN depts d;
```

* To access all possible combinations. No condition is involved.

### Inner Join

```Java
// class Emp and class Dept
empList = new ArrayList<>(); // 5 emps
deptList = new ArrayList<>(); // 4 depts
for(Emp e : empList) {
	for(Dept d : deptList) {
		if(e.deptno == d.deptno)
			sysout(e.ename, d.dname);
	}
}
// output -- 3 rows
```

* Get matching rows from both tables.
* Inner join = Intersection 

```SQL
-- display emp name and dept name in which he is working.
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno = d.deptno;
```

### Equi and Non-equi join
* If join condition (in ON clause) is equality sign then it is also called as Equi-join.
* If join condition (in ON clause) is not equality sign then it is also called as NonEqui-join.

```SQL
-- display emp name and dept name in which he is working.
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno = d.deptno;

-- display emp name and names of depts in which he is not working.
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno != d.deptno;
```

### Outer Join

#### Right outer join
* Right Outer join = Intersection + Extra rows from the right-side table


```Java
// class Emp and class Dept
empList = new ArrayList<>(); // 5 emps
deptList = new ArrayList<>(); // 4 depts
for(Emp e : empList) {
	found = false;
	for(Dept d : deptList) {
		if(e.deptno == d.deptno) {
			found = true;
			sysout(e.ename, d.dname);
		}
	}
	if(!found)
		sysout(e.ename, NULL);
}
// output -- 3 rows
```

```SQL
-- display emp name and dept name in which he is working.
-- if emp's dept is not available, print NULL for dname.
SELECT e.ename, d.dname FROM depts d RIGHT OUTER JOIN emps e ON e.deptno = d.deptno;
```

#### Left outer join
* Left Outer join = Intersection + Extra rows from the left-side table


```SQL
-- display emp name and dept name in which he is working.
-- if dept do not have any emp, then print NULL there.
SELECT e.ename, d.dname FROM depts d LEFT OUTER JOIN emps e ON e.deptno = d.deptno;
```

#### Full outer join
* Full Outer join = Intersection + Extra rows from the left-side table + + Extra rows from the right-side table

```SQL
SELECT e.ename, d.dname FROM depts d FULL OUTER JOIN emps e ON e.deptno = d.deptno;
-- error: In MySQL, FULL OUTER JOIN is not supported.
```

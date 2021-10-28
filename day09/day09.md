# MySQL - RDBMS

## Agenda
* Sub-query
* Derived tables
* Views
* Data Control Language

## Sub-query
* SELECT Query inside another SELECT query.
* Types
	* Single row Sub-query
	* Multi row Sub-query
	* Co-related row Sub-query

```SQL
-- display all depts which have no emps.
SELECT * FROM dept d
WHERE NOT EXISTS (SELECT e.deptno FROM emp e WHERE e.deptno = d.deptno);

SELECT * FROM dept d
WHERE EXISTS (SELECT e.deptno FROM emp e WHERE e.deptno != d.deptno);
-- 10 --> SELECT e.deptno FROM emp e WHERE e.deptno != d.deptno --> 20, 30 --> non-empty set
-- 20 --> SELECT e.deptno FROM emp e WHERE e.deptno != d.deptno --> 10, 30 --> non-empty set
-- 30 --> SELECT e.deptno FROM emp e WHERE e.deptno != d.deptno --> 20, 30 --> non-empty set
-- 40 --> SELECT e.deptno FROM emp e WHERE e.deptno != d.deptno --> 10, 20, 30 --> non-empty set
```

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

-- display job, number of emps for each job and total emps.
-- e.g. ANALYST		2		14
-- e.g. MANAGER		3		14
SELECT job, COUNT(empno) AS jobemp FROM emp
GROUP BY job;

SELECT job, COUNT(empno) AS jobemp, (SELECT COUNT(empno) FROM emp) AS empcnt FROM emp
GROUP BY job;

-- assign: display dept name, number of emps for each deptno and total emps.

```

### SELECT Query inside DML query

```SQL
-- insert new emp with empno=1001, ename=JOHN, sal=2340 in dept OPERATIONS.
INSERT INTO emp (empno,ename,sal,deptno)
VALUES(1001, 'JOHN', 2340.0, (SELECT deptno FROM dept WHERE dname='OPERATIONS'));

SELECT * FROM emp;

-- insert new emp with empno=1002, ename=JACK, sal same as MAX sal in emp table in dept OPERATIONS.
INSERT INTO emp (empno,ename,sal,deptno)
VALUES(1002, 'JACK', (SELECT MAX(sal) FROM emp), (SELECT deptno FROM dept WHERE dname='OPERATIONS'));
-- not allowed in MySQL -- cannot execute sub-query on the table in which inserting data.

SET @maxsal = (SELECT MAX(sal) FROM emp);

INSERT INTO emp (empno,ename,sal,deptno)
VALUES(1002, 'JACK', @maxsal, (SELECT deptno FROM dept WHERE dname='OPERATIONS'));

SELECT * FROM emp;
```

```SQL
-- Increase sal of OPERATIONS dept emps by 100.
UPDATE emp SET sal = sal + 100
WHERE deptno = (SELECT deptno FROM dept WHERE dname='OPERATIONS');

SELECT * FROM emp;

-- Increase sal of emp with max sal by 100.
UPDATE emp SET sal = sal + 100
WHERE sal = (SELECT MAX(sal) FROM emp);
-- not allowed in MySQL -- cannot execute sub-query on the table in which updating data.
-- can solve it using SQL variables.
```

```SQL
-- Delete the emp with max sal.
DELETE FROM emp 
WHERE sal = (SELECT MAX(sal) FROM emp);
-- not allowed in MySQL -- cannot execute sub-query on the table from which deleting data.
-- can solve it using SQL variables.

-- Delete the emps from OPERATIONS dept.
DELETE FROM emp 
WHERE deptno = (SELECT deptno FROM dept WHERE dname = 'OPERATIONS');
```

```SQL
-- delete the dept in which there are no emps.
SELECT * FROM dept
WHERE deptno NOT IN (SELECT deptno FROM emp);

DELETE FROM dept
WHERE deptno NOT IN (SELECT deptno FROM emp);
```

## Views
* Views is projection of the data.

```SQL
CREATE VIEW v_allemp AS
SELECT * FROM emp;

SELECT * FROM v_allemp;

CREATE VIEW v_empincome AS
SELECT empno, ename, sal, comm, sal + IFNULL(comm, 0.0) AS income FROM emp;

SELECT * FROM v_empincome;

CREATE VIEW v_richemps AS
SELECT * FROM emp WHERE sal > 2500;

SELECT * FROM v_richemps;

CREATE VIEW v_empdepts AS
SELECT e.empno, e.ename, e.job, e.sal, e.deptno, d.dname, d.loc FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno;

SELECT * FROM v_empdepts;

CREATE VIEW v_jobsummary AS
SELECT job, COUNT(empno) cnt, SUM(sal) sumsal, AVG(sal) avgsal, MAX(sal) maxsal, MIN(sal) minsal FROM emp e
GROUP BY job;

SELECT * FROM v_jobsummary;
```

```SQL
SELECT * FROM v_jobsummary;

DELETE FROM v_jobsummary WHERE job = 'SUPERVISOR';

DELETE FROM v_jobsummary;
-- error: because v_jobsummary is complex view

INSERT INTO v_richemps (empno, ename, job, sal, deptno)
VALUES (1003, 'DAGNY', 'SUPERVISOR', 2800.0, 40);
-- okay: because v_richemps is simple view

SELECT * FROM v_richemps;

SELECT * FROM emp;
```

```SQL
INSERT INTO v_richemps (empno, ename, job, sal, deptno)
VALUES (1004, 'FRANSISCO', 'SUPERVISOR', 2000.0, 40);

INSERT INTO v_richemps (empno, ename, job, sal, deptno)
VALUES (1005, 'CRIS', 'SUPERVISOR', 1900.0, 40);

SELECT * FROM v_richemps;

SELECT * FROM emp;

CREATE VIEW v_pooremp AS
SELECT * FROM emp WHERE sal < 1000
WITH CHECK OPTION;

SELECT * FROM v_pooremp;

INSERT INTO v_pooremp (empno, ename, job, sal, deptno)
VALUES (1006, 'JANE', 'SUPERVISOR', 900.0, 40);

SELECT * FROM v_pooremp;

INSERT INTO v_pooremp (empno, ename, job, sal, deptno)
VALUES (1007, 'JILL', 'SUPERVISOR', 1200.0, 40);
-- error: CHECK OPTION failed 'classwork.v_pooremp'

ALTER VIEW v_richemps AS
SELECT * FROM emp WHERE sal > 2500
WITH CHECK OPTION;

INSERT INTO v_richemps (empno, ename, job, sal, deptno)
VALUES (1008, 'JILL', 'SUPERVISOR', 1200.0, 40);
-- error: CHECK OPTION failed 'classwork.v_richemps'
```

```SQL
DROP VIEW v_pooremp;

SELECT * FROM emp;
```

```SQL
SELECT * FROM v_empdepts;

CREATE VIEW v_deptsummary AS
SELECT dname, COUNT(empno) cnt, SUM(sal) salsum FROM v_empdepts
GROUP BY dname;

SELECT * FROM v_deptsummary;

DROP VIEW v_empdepts;

DESCRIBE v_deptsummary;
-- error: View 'classwork.v_deptsummary' references invalid table.

SELECT * FROM v_deptsummary;
-- error: View 'classwork.v_deptsummary' references invalid table.

-- possible solutions
--	1. ALTER v_deptsummary with new SELECT query.
-- 	2. Re CREATE v_empdepts with appropriate columns.

DROP VIEW v_deptsummary;
```

### View applications
* Security
	* DCL -- GRANT and REVOKE

#### Hide source code of table

```SQL
SHOW TABLES;

SHOW FULL TABLES;

DESCRIBE emp;

SHOW CREATE TABLE emp;

DESCRIBE v_jobsummary;

SHOW CREATE VIEW v_jobsummary;
```

#### Simplify queries

```SQL
-- display dept name, number of emps for each deptno and total emps -- tables -- joins + sub-query.
SELECT d.dname, COUNT(e.empno) dempcnt, (SELECT COUNT(empno) FROM emp) empcnt FROM emp e
RIGHT JOIN dept d ON e.deptno = d.deptno
GROUP BY d.dname;

SELECT * FROM v_empdepts;

-- query on joins -- sub-query
SELECT dname, COUNT(empno) dempcnt,  (SELECT COUNT(empno) FROM v_empdepts) empcnt FROM v_empdepts
GROUP BY dname;
```

```SQL
CREATE VIEW v_sales AS
SELECT c.cnum, c.cname, c.rating, c.city ccity, s.snum, s.sname, s.city scity, s.comm, o.onum, o.amt, o.odate
FROM orders o
INNER JOIN customers c ON o.cnum = c.cnum
INNER JOIN salespeople s ON o.snum = s.snum;
```

## Derived Tables
* Also called as Inline views.
* SELECT sub-query written in FROM clause is called as "derived tables".

```SQL
-- divide emps in three categories POOR (< 1500), MIDDLE (1500-2500) and RICH (> 2500).
SELECT empno, ename, sal, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal BETWEEN 1500 AND 2500 THEN 'MIDDLE'
ELSE 'RICH'
END category
FROM emp;

-- count emps in each category using view.
CREATE VIEW v_empcategory AS
SELECT empno, ename, sal, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal BETWEEN 1500 AND 2500 THEN 'MIDDLE'
ELSE 'RICH'
END category
FROM emp;

SELECT * FROM v_empcategory;

SELECT category, COUNT(empno) FROM v_empcategory
GROUP BY category;

-- count emps in each category using derived table.
SELECT category, COUNT(empno) FROM
(SELECT empno, ename, sal, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal BETWEEN 1500 AND 2500 THEN 'MIDDLE'
ELSE 'RICH'
END category
FROM emp) AS t_empcategory
GROUP BY category;

-- count emps in each category in each dept -- display dname and category and count.
SELECT empno, ename, sal, dname, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal BETWEEN 1500 AND 2500 THEN 'MIDDLE'
ELSE 'RICH'
END category
FROM emp
INNER JOIN dept WHERE emp.deptno = dept.deptno;

SELECT dname, category, COUNT(empno) FROM
(SELECT empno, ename, sal, dname, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal BETWEEN 1500 AND 2500 THEN 'MIDDLE'
ELSE 'RICH'
END category
FROM emp
INNER JOIN dept WHERE emp.deptno = dept.deptno
) AS t_empcategory
GROUP BY dname, category;
```

```SQL
-- find emps with max sal in each dept.
SELECT deptno, MAX(sal) FROM emp
GROUP BY deptno;

INSERT INTO emp(empno, ename, deptno, sal)
VALUES (1009, 'JOE', 20, 2850.0);

SELECT * FROM emp
WHERE sal IN
(SELECT MAX(sal) FROM emp
GROUP BY deptno);

SELECT e.empno, e.ename, e.sal, e.deptno, md.maxsal
FROM emp e INNER JOIN
(SELECT deptno, MAX(sal) maxsal FROM emp
GROUP BY deptno) md
ON e.deptno = md.deptno;

SELECT e.empno, e.ename, e.sal, e.deptno
FROM emp e INNER JOIN
(SELECT deptno, MAX(sal) maxsal FROM emp
GROUP BY deptno) md
ON e.deptno = md.deptno
WHERE e.sal = md.maxsal;
```
# MySQL - RDBMS

## Agenda
* SQL Joins

## Assignments

```SQL
USE sales;

SELECT MAX(amt) FROM orders;
-- 9891.88

SELECT snum, SUM(amt) FROM orders
GROUP BY snum;

SELECT snum, SUM(amt) FROM orders
GROUP BY snum
HAVING SUM(amt) > 9891.88;
-- | 1001 | 15382.07 |

SELECT snum, SUM(amt) FROM orders
GROUP BY snum
HAVING SUM(amt) > (SELECT MAX(amt) FROM orders);
-- SELECT query in another SELECT query -- SubQuery
```

```SQL
-- process only those orders where order amt > 3000.0
SELECT snum, MAX(amt) FROM orders
WHERE amt > 3000.0
GROUP BY snum;

-- process those orders where MAX(amt) > 3000.0
SELECT snum, MAX(amt) FROM orders
GROUP BY snum
HAVING MAX(amt) > 3000.0;
```

## Set operators
* MySQL set operators
	* UNION
	* UNION ALL
* Set operators are used to combine output of two SELECT queries. The output of both queries should have same number of columns.

```SQL
SELECT ename, job, deptno FROM emp WHERE deptno = 10;

SELECT ename, job, deptno FROM emp WHERE deptno = 20;

(SELECT ename, job, deptno FROM emp WHERE deptno = 10)
UNION
(SELECT ename, job, deptno FROM emp WHERE deptno = 20);
```

```SQL
SELECT ename, job, deptno FROM emp WHERE deptno = 10;

SELECT ename, job, deptno FROM emp WHERE job = 'MANAGER';

(SELECT ename, job, deptno FROM emp WHERE deptno = 10)
UNION
(SELECT ename, job, deptno FROM emp WHERE job = 'MANAGER');
-- duplicate rows will be considered only once.

(SELECT ename, job, deptno FROM emp WHERE deptno = 10)
UNION ALL
(SELECT ename, job, deptno FROM emp WHERE job = 'MANAGER');
-- duplicate rows will be repeated.
```

```SQL
(SELECT empno, ename FROM emp)
UNION
(SELECT id, name FROM books);
-- allowed, but may not usable.

(SELECT ename, hire FROM emp)
UNION
(SELECT deptno, dname FROM dept);
-- allowed, but may not usable.

(SELECT deptno, dname FROM dept)
UNION
(SELECT deptno, dname, loc FROM dept);
-- error: number of columns are different
```

```SQL
-- print jobwise total sal along with total sal of emps.
SELECT job, SUM(sal) FROM emp GROUP BY job;

SELECT SUM(sal) FROM emp;

(SELECT job, SUM(sal) FROM emp GROUP BY job)
UNION
(SELECT 'Total', SUM(sal) FROM emp);
```

## Joins

### Outer Joins

#### Full Outer Join
* FULL OUTER JOIN is not supported in MySQL.
* It can be emulated using SET operators.

```SQL
SELECT e.ename, d.dname FROM depts d
LEFT JOIN emps e ON e.deptno = d.deptno;

SELECT e.ename, d.dname FROM depts d
RIGHT JOIN emps e ON e.deptno = d.deptno;

(SELECT e.ename, d.dname FROM depts d
LEFT JOIN emps e ON e.deptno = d.deptno)
UNION ALL
(SELECT e.ename, d.dname FROM depts d
RIGHT JOIN emps e ON e.deptno = d.deptno);

(SELECT e.ename, d.dname FROM depts d
LEFT JOIN emps e ON e.deptno = d.deptno)
UNION
(SELECT e.ename, d.dname FROM depts d
RIGHT JOIN emps e ON e.deptno = d.deptno);
-- same as full outer join
```

### Self Join

```SQL
SELECT e.ename, m.ename AS mname FROM emps e
INNER JOIN emps m ON e.mgr = m.empno;

SELECT e.ename, m.ename AS mname FROM emps e
LEFT JOIN emps m ON e.mgr = m.empno;
```

### USING keyword
* USING keyword is used for "equi-join" condition between two tables where "column name" on which join is to be done is "same" in both the tables.
* USING keyword is supported in MySQL.

```SQL
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno = d.deptno;

SELECT e.ename, d.dname FROM emps e INNER JOIN depts d USING (deptno);
-- USING (deptno) --> ON e.deptno = d.deptno

SELECT e.ename, d.dname FROM emps e RIGHT JOIN depts d ON e.deptno = d.deptno;

SELECT e.ename, d.dname FROM emps e RIGHT JOIN depts d USING (deptno);
-- USING (deptno) --> ON e.deptno = d.deptno
```

### NATURAL Joins

* NATURAL JOIN keyword is supported in MySQL.

```SQL
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno = d.deptno;

DESCRIBE emps;
DESCRIBE depts;
-- the column name common for both tables is "deptno".
-- we want to right join on the same column.

SELECT e.ename, d.dname FROM emps e NATURAL JOIN depts d;
-- inner equi-join is executed on common column i.e. deptno --> ON e.deptno = d.deptno

SELECT e.ename, d.dname FROM emps e RIGHT JOIN depts d ON e.deptno = d.deptno;

SELECT e.ename, d.dname FROM emps e NATURAL RIGHT JOIN depts d;
-- right outer equi-join is executed on common column i.e. deptno --> ON e.deptno = d.deptno
```

### Legacy Join Syntax
* No JOIN keyword is used. Table names are separated by command and condition is given in WHERE clause.
* Only INNER join can be done using this syntax in MySQL.

```SQL
SELECT e.ename, d.dname FROM emps e INNER JOIN depts d ON e.deptno = d.deptno;

SELECT e.ename, d.dname FROM emps e, depts d WHERE e.deptno = d.deptno;
```

### Advanced Queries

```SQL
-- print emp name and corresponding dept name. print all emps.
SELECT e.ename, d.dname FROM emps e
LEFT JOIN depts d ON e.deptno = d.deptno;

-- print emp name and corresponding dist. 
SELECT e.ename, a.dist FROM emps e
INNER JOIN addr a ON e.empno = a.empno;

-- print emp name, his dist and his dept.
SELECT e.ename, a.dist, d.dname FROM emps e
INNER JOIN addr a ON e.empno = a.empno
LEFT JOIN depts d ON e.deptno = d.deptno;

-- print all emps and their meeting topics.
SELECT e.ename, m.topic FROM emp_meeting em
INNER JOIN emps e ON em.empno = e.empno
INNER JOIN meeting m ON em.meetno = m.meetno;

-- print all meetings of 'Nilesh'.
SELECT e.ename, m.topic FROM emp_meeting em
INNER JOIN emps e ON em.empno = e.empno
INNER JOIN meeting m ON em.meetno = m.meetno
WHERE e.ename = 'Nilesh';

-- emps will be joining their meeting from their home. 
-- print emp name, meeting topic and their address (dist).
SELECT e.ename, m.topic, a.dist FROM emp_meeting em
INNER JOIN emps e ON em.empno = e.empno
INNER JOIN meeting m ON em.meetno = m.meetno
INNER JOIN addr a ON e.empno = a.empno;

-- emps will be joining their meeting from their home. 
-- also emps represent their dept.
-- print emp name, meeting topic, their address (dist) and their dept name.
SELECT e.ename, m.topic, a.dist, d.dname FROM emp_meeting em
INNER JOIN emps e ON em.empno = e.empno
INNER JOIN meeting m ON em.meetno = m.meetno
INNER JOIN addr a ON e.empno = a.empno
LEFT JOIN depts d ON e.deptno = d.deptno;
```

```SQL
SELECT e.empno, e.ename, e.job, e.deptno, d.dname, d.loc FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno;

-- print dname and number of emps in that dept.
SELECT deptno, COUNT(empno) FROM emp
GROUP BY deptno;

SELECT d.dname, COUNT(e.empno) FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno
GROUP BY d.dname;

SELECT d.dname, COUNT(e.empno) FROM emp e
RIGHT JOIN dept d ON e.deptno = d.deptno
GROUP BY d.dname;
```

```SQL
-- print ename, dname, job, sal in asc order of dname (1st level) and desc order of sal (2nd level).
SELECT e.ename, d.dname, e.job, e.sal FROM emp e
INNER JOIN dept d ON e.deptno = d.deptno
ORDER BY d.dname ASC, e.sal DESC;
```

```SQL
-- print dname and total emp sal in desc order of total sal.
SELECT d.dname, SUM(e.sal) FROM emp e
RIGHT JOIN dept d ON e.deptno = d.deptno
GROUP BY d.dname
ORDER BY SUM(e.sal) DESC;
```

```SQL
SELECT e.ename, d.dname FROM emp e INNER JOIN dept d ON e.deptno = d.deptno;

SELECT emp.ename, dept.dname FROM emp INNER JOIN dept ON emp.deptno = dept.deptno;
-- instead of aliases the table name can be used.

SELECT ename, dname FROM emp INNER JOIN dept ON deptno = deptno;
-- error: deptno column name is same for both tables.

SELECT ename, dname FROM emp INNER JOIN dept ON emp.deptno = dept.deptno;
-- ename and dname column names are not duplicated across the tables, so no alias/table-name required.
```
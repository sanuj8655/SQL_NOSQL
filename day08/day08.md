# MySQL - RDBMS

## Agenda
* Sub-queries

## Assignment

```SQL
-- Write a query that produces all pairs of salespeople who are living in the same city.
SELECT * FROM salespeople;

SELECT s1.sname, s1.city, s2.sname, s2.city FROM salespeople s1
INNER JOIN salespeople s2 ON s1.city = s2.city;

-- Exclude combinations of salespeople with themselves.
SELECT s1.sname, s1.city, s2.sname, s2.city FROM salespeople s1
INNER JOIN salespeople s2 ON s1.city = s2.city
WHERE s1.snum != s2.snum;

-- as well as duplicate rows with the order reversed.
SELECT s1.sname, s1.city, s2.sname, s2.city FROM salespeople s1
INNER JOIN salespeople s2 ON s1.city = s2.city
WHERE s1.snum < s2.snum;

SELECT s1.sname, s1.city, s2.sname, s2.city FROM salespeople s1
INNER JOIN salespeople s2 ON s1.city = s2.city
WHERE s1.sname < s2.sname;
-- this will work provided no two salespeople have same name.
```

```SQL
-- What is name of customer and salesman of the maximum amount order?
SELECT o.amt, s.sname, c.cname FROM orders o
INNER JOIN salespeople s ON o.snum = s.snum
INNER JOIN customers c ON o.cnum = c.cnum;

SELECT o.amt, s.sname, c.cname FROM orders o
INNER JOIN salespeople s ON o.snum = s.snum
INNER JOIN customers c ON o.cnum = c.cnum
ORDER BY o.amt DESC
LIMIT 1;
-- o.snum = s.snum --> get the salesman for the order.
-- o.cnum = c.cnum --> get the customer for the order.
```

```SQL
SELECT o.amt, s.sname, c.cname FROM orders o
INNER JOIN salespeople s ON o.snum = s.snum
INNER JOIN customers c ON c.snum = s.snum
ORDER BY o.amt DESC;
--  wrong: c.snum = s.snum ---> this condition is useful to find salesman for the customer.

SELECT o.amt, s.sname, c.cname FROM orders o
INNER JOIN salespeople s ON o.snum = s.snum
INNER JOIN customers c ON o.snum = c.snum
ORDER BY o.amt DESC;
-- wrong: o.snum = c.snum --> get customer's salesman for the current order.
```


```SQL
-- Which salesman are handling more than one customers? Display name of salesman and number of customers.
SELECT snum, COUNT(cnum) FROM customers
GROUP BY snum;

SELECT s.sname, COUNT(c.cnum) FROM customers c
INNER JOIN salespeople s ON c.snum = s.snum
GROUP BY s.sname;

SELECT s.sname, COUNT(c.cnum) FROM customers c
INNER JOIN salespeople s ON c.snum = s.snum
GROUP BY s.sname
HAVING COUNT(c.cnum) > 1;
```

## Problems
* products --- order_details --- orders --- customers --- city_pin
1. Display all orders in the last months.
	* orders table -- WHERE clause on odate
2. Display all customers who have given orders in the last month.
	* orders + customers table -- ON o.cid = c.cid
3. Display top 10 products sold in last month (top w.r.t. "quantity").
	* products + order_details + orders
		* products + order_details -- ON p.pid = od.pid
		* order_details + orders -- ON o.oid = od.oid
		* View / Derived table -- GROUP
		* WHERE for date, ORDER BY SUM, LIMIT 10
4. Display top 10 orders in the last month (top w.r.t. total order amount).
	* products + order_details + orders
		* products + order_details -- ON p.pid = od.pid
		* order_details + orders -- ON o.oid = od.oid
		* View / Derived table -- GROUP
		* WHERE for date, ORDER BY SUM, LIMIT 10
5. Display top 10 customers in the last month (top w.r.t. total order amounts).
	* products + order_details + orders + customers
6. Find the city in which max business is done in the last month.
	* products + order_details + orders + customers + city_pin

## Sub-queries

```SQL
USE classwork;

SELECT USER(), DATABASE();

-- find employee is maximum salary.
SELECT * FROM emp ORDER BY sal DESC LIMIT 1;
-- correct: KING -- the output may be wrong if two emps have same max sal.

-- find employee is 2nd highest salary salary.
SELECT * FROM emp ORDER BY sal DESC LIMIT 1, 1;
-- correct: SCOTT -- however two emps have same sal (SCOTT and FORD), but only one is displayed.

-- find employee is 3rd highest salary salary.
SELECT * FROM emp ORDER BY sal DESC LIMIT 2, 1;
-- wrong: FORD -- third highest sal is 2975.00 -- JONES.
```

```SQL
-- find employee with maximum salary.
SELECT * FROM emp WHERE sal = MAX(sal);
-- error: group fn cannot be used in WHERE clause.
```

### MySQL variables
* The user-defined session variables are represented using @ sign and can hold a value.
* The variables are auto destroyed, when current session is completed (EXIT;).
* The system-defined variables are represented using @@ sign. They are used to hold user or global settings.
	* SELECT @@sql_mode;
	* SELECT @@version;

```SQL
-- find employee with maximum salary.
SELECT MAX(sal) FROM emp;
-- 5000.0
SELECT * FROM emp WHERE sal = 5000.0;
-- correct: but manual sal entry may not be desired.

-- the same output can be achieved using user variable.
SET @maxsal = (SELECT MAX(sal) FROM emp);
SELECT * FROM emp WHERE sal = @maxsal;
```

```SQL
-- find emps with 2nd highest sal.
SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 1, 1;

SET @sal2 = (SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 1, 1);
SELECT * FROM emp WHERE sal = @sal2;
```

```SQL
-- find emps with 3rd highest sal.
SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 2, 1;

SET @sal3 = (SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 2, 1);
SELECT * FROM emp WHERE sal = @sal3;
```

### Single Row Sub-query
* Sub-query returns single row.

```SQL
-- find emp with max sal
SELECT * FROM emp
WHERE sal = (SELECT MAX(sal) FROM emp);

-- find emps with 2nd highest sal.
SELECT * FROM emp 
WHERE sal = (SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 1, 1);

-- find emps with 3rd highest sal.
SELECT * FROM emp
WHERE sal = (SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 2, 1);
```

```SQL
-- display all emps working in dept of KING.
SELECT deptno FROM emp WHERE ename = 'KING';

SELECT * FROM emp
WHERE deptno = (SELECT deptno FROM emp WHERE ename = 'KING');
```

```SQL
-- find emps having sal more than sal of all salesman.
SELECT sal FROM emp WHERE job = 'SALESMAN';

SELECT MAX(sal) FROM emp WHERE job = 'SALESMAN';

SELECT * FROM emp
WHERE sal > (SELECT MAX(sal) FROM emp WHERE job = 'SALESMAN');
```

### Multi-row Sub-query
* Sub-query returns multiple rows.

#### ALL operator
* Used to compare with rows returned from multi-row Sub-query.
* The condition should be satisfied for ALL the rows.
* Similar to logical AND.

```SQL
-- find emps having sal more than sal of ALL salesman.

SELECT * FROM emp
WHERE sal > (SELECT sal FROM emp WHERE job = 'SALESMAN');
-- error: left side is one sal > right side is 4 sals.

SELECT * FROM emp
WHERE sal > ALL(SELECT sal FROM emp WHERE job = 'SALESMAN');
-- sal > 1600.00 AND sal > 1250.00 AND sal > 1250.00 AND sal > 1500.00.
```

#### ANY operator
* Used to compare with rows returned from multi-row Sub-query.
* The condition should be satisfied for ANT one row.
* Similar to logical OR.

```SQL
-- find emps having sal less than sal of ANY salesman.
SELECT * FROM emp
WHERE sal < (SELECT sal FROM emp WHERE job = 'SALESMAN');
-- error: left side is one sal < right side is 4 sals.

SELECT * FROM emp
WHERE sal < ANY(SELECT sal FROM emp WHERE job = 'SALESMAN');
-- sal < 1600.00 OR sal < 1250.00 OR sal < 1250.00 OR sal < 1500.00.
```

#### IN operator

```SQL
-- display all depts which have emps (at least one emp).
SELECT deptno FROM emp;

SELECT * FROM dept
WHERE deptno = ANY(SELECT deptno FROM emp);
-- deptno = 10 OR deptno = 20 OR deptno = 30

SELECT * FROM dept
WHERE deptno IN (SELECT deptno FROM emp);
-- deptno = 10 OR deptno = 20 OR deptno = 30
```

#### ALL vs ANY vs IN
* ALL operator is comparing similar to logical AND -- all comparisons/conditions should be true.
* ANY/IN operator is comparing similar to logical OR -- any one comparisons/conditions should be true.
* ALL and ANY can be used only with sub-queries (in MySQL).
* IN operator can be used for sub-queries as well as simple WHERE clauses.
* ALL and ANY can be used for any comparison i.e. <, >, <=, >=, =, !=.
* IN operator can be used only for equality checks i.e. =.

### sub-query performance
* Typically sub-queries (inner queries) are executed for each row of the outer query.
* SELECT * FROM dept WHERE deptno IN (SELECT deptno FROM emp);
	* 10 --> SELECT deptno FROM emp -- True
	* 20 --> SELECT deptno FROM emp -- True
	* 30 --> SELECT deptno FROM emp -- True
	* 40 --> SELECT deptno FROM emp -- False
* If RDBMS is not doing any optimization, sub-queries will execute slower.
* However most of modern RDBMS does lot of optimization like materialization (caching the results of sub-query), first_match (ANY operator will return with first matched, no need to compare all of them), etc. which significantly improves performance of sub-queries.
* These optimization are RDBMS specific and depends on internal implementations (time complexity). These optimization may vary as per versions of the RDBMS.
* Refer RDBMS documentation for all available optimizations. https://dev.mysql.com/doc/refman/8.0/en/switchable-optimizations.html
	```SQL
	SELECT @@optimizer_switch;
	```

### Co-related sub-query
* Typically sub-query performance can be improved if inner query is processing/returning less number of rows.
* The number of rows from inner query can be reduced by using WHERE clause based on current row in outer query.
* Such sub-queries (inner query depends on current row of the outer query) are called as "Co-related sub-queries".

#### EXISTS operator
* This operator check if inner query returns non-empty set (number of rows > 0).

```SQL
-- display all depts which have emps (at least one emp).
SELECT * FROM dept WHERE deptno IN (SELECT deptno FROM emp);
	-- 10 --> SELECT deptno FROM emp -- 14
	-- 20 --> SELECT deptno FROM emp -- 14
	-- 30 --> SELECT deptno FROM emp -- 14
	-- 40 --> SELECT deptno FROM emp -- 14

SELECT * FROM dept d WHERE d.deptno IN (SELECT e.deptno FROM emp e WHERE e.deptno = d.deptno);
	-- 10 --> SELECT e.deptno FROM emp e WHERE e.deptno = 10 -- 3 --> 10, 10, 10
	-- 20 --> SELECT e.deptno FROM emp e WHERE e.deptno = 20 -- 5 --> 20, 20, 20, 20, 20
	-- 30 --> SELECT e.deptno FROM emp e WHERE e.deptno = 30 -- 6 --> 30, 30, 30, 30, 30, 30
	-- 40 --> SELECT e.deptno FROM emp e WHERE e.deptno = 40 -- 0 -->

SELECT * FROM dept d WHERE EXISTS (SELECT e.deptno FROM emp e WHERE e.deptno = d.deptno);
```

```SQL
-- display all depts which have no emps.
SELECT * FROM dept WHERE deptno NOT IN (SELECT deptno FROM emp);

SELECT * FROM dept d WHERE NOT EXISTS (SELECT e.deptno FROM emp e WHERE e.deptno = d.deptno);
```

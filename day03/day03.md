# MySQL - RDBMS

## Agenda
* SELECT (DQL)
	* Computed Columns (CASE-WHEN)
	* DISTINCT
	* LIMIT
	* ORDER BY
	* WHERE 
		* Relational operators
		* Logical operators
		* Special operators - IN, BETWEEN, LIKE, RLIKE/REGEXP

## SELECT - DQL

### CASE-WHEN

```SQL
SELECT USER(), DATABASE();
-- dbda, classwork

SELECT empno, ename, deptno FROM emp;

-- also display dname: 10=ACCOUNTING, 20=RESEARCH, 30=SALES, 40=OPERATIONS.
SELECT empno, ename, deptno, CASE deptno
WHEN 10 THEN 'ACCOUNTING'
WHEN 20 THEN 'RESEARCH'
WHEN 30 THEN 'SALES'
ELSE 'OPERATIONS'
END AS dname
FROM emp;

SELECT empno, ename, deptno, CASE
WHEN deptno=10 THEN 'ACCOUNTING'
WHEN deptno=20 THEN 'RESEARCH'
WHEN deptno=30 THEN 'SALES'
ELSE 'OPERATIONS'
END AS dname
FROM emp;

-- display empno, ename, sal, class of all emps.
-- sal < 1500 then POOR
-- sal >= 1500 AND sal <= 2500 then MIDDLE
-- sal > 2500 then RICH
SELECT empno, ename, sal, CASE
WHEN sal < 1500 THEN 'POOR'
WHEN sal >= 1500 AND sal <= 2500 THEN 'MIDDLE'
ELSE 'RICH'
END AS class
FROM emp;
```

### DISTINCT

```SQL
SELECT empno, ename, job, deptno FROM emp;

-- display all unique deptno from emp
SELECT DISTINCT deptno FROM emp;

-- display all unique job from emp
SELECT DISTINCT job FROM emp;

-- display all unique "combinations" of deptno & jobs.
SELECT DISTINCT deptno, job FROM emp;
```

### LIMIT clause

```SQL
SELECT * FROM emp;

SELECT * FROM emp LIMIT 5;
-- fetch first 5 records.

SELECT * FROM emp LIMIT 5, 3;
-- fetch 3 records after skipping first 5 records.
```

### ORDER BY clause

```SQL
SELECT * FROM emp ORDER BY deptno;

SELECT * FROM emp ORDER BY sal ASC;
SELECT * FROM emp ORDER BY sal DESC;

SELECT * FROM emp ORDER BY hire;

SELECT * FROM emp ORDER BY ename DESC;

-- sort emps by deptno (1st level) and job (2nd level)
SELECT * FROM emp ORDER BY deptno, job;

-- sort emps by job (1st level) and deptno (2nd level)
SELECT * FROM emp ORDER BY job, deptno;

-- sort emps by deptno (1st level) and job (2nd level) and sal desc (3rd level)
SELECT * FROM emp ORDER BY deptno ASC, job ASC, sal DESC;
```

### ORDER BY + LIMIT clause

```SQL
-- find 3 emps with highest sal.
SELECT * FROM emp ORDER BY sal DESC;
SELECT * FROM emp ORDER BY sal DESC LIMIT 3;

-- find emp with lowest sal.
SELECT * FROM emp ORDER BY sal ASC;
SELECT * FROM emp ORDER BY sal ASC LIMIT 1;

-- find emp with 3rd lowest sal.
SELECT * FROM emp ORDER BY sal ASC;
SELECT * FROM emp ORDER BY sal ASC LIMIT 2, 1;

-- find emp with 3rd highest sal.
SELECT * FROM emp ORDER BY sal DESC;
SELECT * FROM emp ORDER BY sal DESC LIMIT 2, 1;
-- result is incorrect, because two emps have same sal.
-- we will solve this problem later using sub-queries.

-- find 3rd highest sal.
SELECT DISTINCT sal FROM emp ORDER BY sal DESC LIMIT 2, 1;
```

### Relational operators

```SQL
-- get all emps with sal < 2000.
SELECT * FROM emp WHERE sal < 2000;

-- get all emps in deptno 20.
SELECT * FROM emp WHERE deptno = 20;

-- get all CLERK emps.
SELECT * FROM emp WHERE job = 'CLERK';

-- find emp with id 7900.
SELECT * FROM emp WHERE empno = 7900;

-- find emp hired on 12-Jan-1983.
SELECT * FROM emp WHERE hire = '1983-01-12';
```

### NULL related operators

```SQL
-- NULL values are not compared using relational operators.

-- find all emps who do not get comm (null).
SELECT * FROM emp WHERE comm = NULL;

-- find all emps who get comm (not null).
SELECT * FROM emp WHERE comm != NULL;

-- NULL values are compared using special operators IS NULL, IS NOT NULL, <=> (MySQL).

-- find all emps who do not get comm (null).
SELECT * FROM emp WHERE comm IS NULL;
SELECT * FROM emp WHERE comm <=> NULL;

-- find all emps who get comm (not null).
SELECT * FROM emp WHERE comm IS NOT NULL;
```

### Logical operators, BETWEEN & IN operator

```SQL
-- find all emps hired in year 1981 (hire >= 1981-01-01 AND hire <= 1981-12-31).
SELECT * FROM emp WHERE hire >= '1981-01-01' AND hire <= '1981-12-31';

SELECT * FROM emp WHERE hire BETWEEN '1981-01-01' AND '1981-12-31';

-- find all emps having sal >= 1000 AND sal <= 2000.
SELECT * FROM emp WHERE sal >= 1000.0 AND sal <= 2000.0;

SELECT * FROM emp WHERE sal BETWEEN 1000.0 AND 2000.0;

-- find all emps working in dept 10 and 20.
SELECT * FROM emp WHERE deptno = 10 OR deptno = 20;

SELECT * FROM emp WHERE deptno IN (10, 20);

-- find all PRESIDENT, ANALYST and MANAGER.
SELECT * FROM emp WHERE job = 'PRESIDENT' OR job = 'ANALYST' OR job = 'MANAGER';

SELECT * FROM emp WHERE job IN ('PRESIDENT', 'ANALYST', 'MANAGER');

-- find CLERK in deptno 20.
SELECT * FROM emp WHERE deptno = 20 AND job = 'CLERK';

-- find all rich (sal > 2500) and poor emps (sal < 1500).
SELECT * FROM emp WHERE sal > 2500 OR sal < 1500;
SELECT * FROM emp WHERE sal NOT BETWEEN 1500 AND 2500;

-- find all MANAGER and emps in deptno=10.
SELECT * FROM emp WHERE job = 'MANAGER' OR deptno = 10;

INSERT INTO emp (empno, ename) VALUES (1001, 'J'), (1002, 'S'), (1003, 'T');

-- find all emps whose names are starting from 'J' to 'S'
SELECT * FROM emp WHERE ename BETWEEN 'J' AND 'S';

SELECT * FROM emp WHERE ename BETWEEN 'J' AND 'T' AND ename != 'T';
```

### LIKE operator
* % and _ wild card chars are supported.

```SQL
-- find all emps whose name start from K
SELECT * FROM emp WHERE ename LIKE 'K%';

-- find all emps whose name ends with H
SELECT * FROM emp WHERE ename LIKE '%H';

-- find all emps whose name contains U.
SELECT * FROM emp WHERE ename LIKE '%U%';

-- find all emps whose name contains A multiple times (more than once).
SELECT * FROM emp WHERE ename LIKE '%A%A%';

-- find all emps whose names are of 4 letters.
SELECT * FROM emp WHERE ename LIKE '____';

-- find all emps whose name is 5 letters and end with N.
SELECT * FROM emp WHERE ename LIKE '____N';
```

### RLIKE/REGEXP operator
* column RLIKE 'regex-pattern'
* column REGEXP 'regex-pattern'
* Wild card characters
	* ^ --> Beginning
	* $ --> End
	* [apx], [a-z], [A-Z0-9] --> Any "one" char from given scan set/range
	* . --> Any "one" char
	* * --> 0 or more occurrences of previous char
	* + --> 1 or more occurrences of previous char
	* ? --> 0 or 1 occurrence of previous char
	* {n} --> n occurrences of previous char
	* {m,} --> m or more occurrences of previous char
	* {,n} --> n or lesss occurrences of previous char
	* {m,n} --> min m and max n occurrences of previous char
	* (word1|word2|word3) --> compare with any one word

```SQL
-- find all emps whose name starts from 'J' to 'S'.
SELECT * FROM emp WHERE ename REGEXP '^[J-S].*';

-- find all MANAGER, PRESIDENT and ANALYST
SELECT * FROM emp WHERE job REGEXP '^(MANAGER|ANALYST|PRESIDENT)$';

-- find all emps whose name contains 'T' twice continuously
SELECT * FROM emp WHERE ename REGEXP '.*T{2}.*';
```

```SQL
-- find emp with highest sal from dept 20 and 30.
SELECT * FROM emp;
SELECT * FROM emp WHERE deptno IN (20, 30);
SELECT * FROM emp WHERE deptno IN (20, 30) ORDER BY sal DESC;
SELECT * FROM emp WHERE deptno IN (20, 30) ORDER BY sal DESC LIMIT 1;
```


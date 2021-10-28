# MySQL - RDBMS

## Agenda
* Codd's Rules
* ALTER table
* Transactions

## Codd's Rules
* Foundation Rule
* Rule 1 to 12

## MySQL architecture

### Storage engine

```SQL
SELECT USER(), DATABASE();

SHOW CREATE TABLE dept;

CREATE TABLE items (
  id int,
  name varchar(40),
  description varchar(40)
) ENGINE=CSV;
-- error: The storage engine for the table doesn't support nullable columns

CREATE TABLE items (
  id int NOT NULL,
  name varchar(40) NOT NULL,
  description varchar(40) NOT NULL
) ENGINE=CSV;

INSERT INTO items VALUES
(1, 'item1', 'desc1'),
(2, 'item2', 'desc2'),
(3, 'item3', 'desc3'),
(4, 'item4', 'desc4'),
(5, 'item5', 'desc5');

CREATE TABLE categories (
  id int PRIMARY KEY,
  category varchar(40),
  description varchar(40)
) ENGINE=MYISAM;

INSERT INTO categories VALUES
(1, 'category1', 'desc1'),
(2, 'category2', 'desc2'),
(3, 'category3', 'desc3'),
(4, 'category4', 'desc4'),
(5, 'category5', 'desc5');
```

* MySQL Data Directory: `C:\ProgramData\MySQL\MySQL Server 8.0\Data`

#### Storage Engines
* InnoDb
	* One binary file (.ibd) per table --> Data rows + Table structure
* MYISAM
	* Multiple binary files per table
		* .MYD, .MYI, .sdi --> Data rows, Indexes, Table structure.
* CSV
	* Two binary files per Table
		* .CSM, .CSV --> Data rows, Table structure.
* NDB --> Cluster (Multiple MySQL servers connected together)
* Memory --> In memory data storage and processing.
	* Very fast processing
	* Volatile storage.

#### Components
* Security/Authentication
* SQL interface
* Parser
* Optimizer
* Cache
* Storage engine

## Transaction

```SQL
CREATE TABLE accounts (
	id INT PRIMARY KEY,
	type CHAR(10),
	balance DECIMAL(8,2)
);

INSERT INTO accounts VALUES
(1, 'Saving', 10000),
(2, 'Saving', 3000),
(3, 'Saving', 20000),
(4, 'Current', 50000),
(5, 'Current', 90000);
```

```SQL
-- transfer 1000 from account 1 to account 2.
START TRANSACTION;
UPDATE accounts SET balance = balance - 1000 WHERE id = 1;
UPDATE accounts SET balance = balance + 1000 WHERE id = 2;
COMMIT;

SELECT * FROM accounts;

-- transfer 3000 from account 3 to account 1.

START TRANSACTION;
UPDATE accounts SET balance = balance - 3000 WHERE id = 3;
UPDATE accounts SET balance = balance + 3000 WHERE id = 1;
SELECT * FROM accounts;
ROLLBACK;
SELECT * FROM accounts;
```

```SQL
SHOW TABLES;

START TRANSACTION;
UPDATE accounts SET balance = balance - 3000 WHERE id = 3;
UPDATE accounts SET balance = balance + 3000 WHERE id = 1;
SELECT * FROM accounts;

TRUNCATE TABLE dummy; -- DDL -- changes auto committed

ROLLBACK; -- no changes available for rollback -- does nothing

SELECT * FROM accounts; -- amount is transferred.
```

### Row locking

```SQL
terminal1> SELECT USER(), DATABASE();
-- root, classwork

terminal2> SELECT USER(), DATABASE();
-- dbda, classwork

terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;

terminal1> START TRANSACTION;

terminal1> UPDATE accounts SET balance=balance-100
WHERE id = 1;

terminal1> SELECT * FROM accounts;

terminal1> INSERT INTO accounts VALUES
(6, 'Saving', 7000);

terminal1> SELECT * FROM accounts;

terminal1> DELETE FROM accounts WHERE id = 5;

terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;

terminal1> COMMIT;

terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;
```

#### Optimistic Locking

```SQL
terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;

terminal1> START TRANSACTION;

terminal1> DELETE FROM accounts WHERE id = 6;
-- when DML is done on any record, that row is locked.

terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;

terminal2> UPDATE accounts SET balance = 8000.0
WHERE id = 6;
-- if another user try to perform DML on that record, that transaction is blocked.
-- until user1 transaction is completed or timeout ocurrs.

terminal1> ROLLBACK;
```

#### Pessimistic Locking

```SQL
terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;

terminal1> START TRANSACTION;

terminal1> SELECT * FROM accounts WHERE id = 4 FOR UPDATE;
-- user1 is locking record explicitly (without DML operation) -- Pessimistic

terminal2> DELETE FROM accounts WHERE id = 4;
-- if waiting for long, will unblock with timeout error.

terminal1> UPDATE accounts SET balance = 30000
WHERE id = 4;

terminal2> SELECT * FROM accounts;

terminal1> COMMIT;

terminal1> SELECT * FROM accounts;

terminal2> SELECT * FROM accounts;
```

* Assumption: table1 have primary key (or indexed table).
	* If user1 is updating a record in table1 and user2 is updating another record in table1.
	* What will happen?
	* Both records will be updated individually in different transactions.

* Assumption: table2 have primary key (or indexed table).
	* If user1 is updating a record in table2 and user2 is updating same record in table2.
	* What will happen?
	* The record is locked (Optimistic locking) and hence user2 transaction will be blocked (until user1 transaction is completed or time out).

* Assumption: table3 does not have primary key (or not an indexed table).
	* If user1 is updating a record in table3 and user2 is updating another record in table3.
	* What will happen?
	* User2 transaction is blocked, until user1 transaction is completed or timeout ocurrs.
	* In MySQL, if table is not indexed, then whole table is locked (not only row is locked).























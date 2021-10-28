# Database Technologies

## Agenda
* DBMS vs RDBMS
* RDBMS Server and Client
* Getting Started

## Getting Started
* Database/Schema
	* One MySQL server can hold databases for different projects, called as database/schema.
	* Database/Schema is a container that holds multiple tables, constraints, relations, procedures, etc related to a project.

* Set Path variable to run MySQL client (if not done already).
	* This --> Properties --> Advanced System Settings --> Advanced --> Environment Variables --> Path --> Edit
	* New --> (at the end) "C:\Program Files\MySQL\MySQL Server 8.0\bin"
	* Ok
	* Open new terminal.

* On terminal/command prompt:

	```sh
	mysql -u root -p
	```
	
	* "root" is admin user created while installation. Used for admin work.
	* It will display "mysql> " prompt.
	* This prompt can be changed by setting Environment variable MYSQL_PS1='\u> '

* On MySQL prompt

	```SQL
	\! cls

	SELECT USER();

	SELECT DATABASE();

	SHOW DATABASES;

	SELECT user, host FROM mysql.user;
	-- get user & host columns from user table in mysql database/schema.

	CREATE DATABASE classwork;
	-- (1) create a new database/schema classwork
	
	CREATE USER dbda@localhost IDENTIFIED BY 'dbda';
	-- (2) create a user by name "dbda" and password "dbda", who can login from current machine only "localhost".
	-- '%' (instead of "localhost") will allow to login from remote machine (in the same network).

	SELECT user, host FROM mysql.user;

	SHOW GRANTS FOR dbda@localhost;
	-- display permissions/grants for dbda user.
	-- USAGE means no permissions.

	GRANT ALL PRIVILEGES ON classwork.* TO dbda@localhost;
	-- (3) give full permissions on classwork database/schema to dbda user.

	SHOW GRANTS FOR dbda@localhost;

	FLUSH PRIVILEGES;
	-- good practice for activating permission (not mandetory).

	EXIT;
	```

* On terminal/command prompt:

	```sh
	mysql -u dbda -p
	```

* On MySQL prompt

	```SQL
	SHOW DATABASES;

	SELECT USER(), DATABASE();

	USE classwork;
	-- select databasee/schema for further queries

	SELECT USER(), DATABASE();

	SHOW TABLES;

	SOURCE D:/may21/dbda/dbms/db/classwork-db.sql

	SHOW TABLES;

	SELECT * FROM dept;
	SELECT * FROM emp;
	SELECT * FROM books;

	EXIT;
	```

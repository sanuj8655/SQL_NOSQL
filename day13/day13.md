# NoSQL - Mongo

## Agenda

## Mongo Terminologies
* Mongo database --> RDBMS database/schema
* Mongo collections --> RDBMS tables
* Mongo document --> RDBMS row
* Mongo document fields --> RDBMS columns
* "db" represnt current database.
* Mongo shell --> Mongo client
	* Follows Java Script (JS) syntax -- do not use SQL syntax.

## Mongo Queries 

* terminal> mongo
	* Server: 4.x version

```JS
show databases;

use may21;

show collections;

db

db.people.insert({
	name: "Nitin",
	email: "nitin@sunbeaminfo.com",
	phone: "9881208112"
});

db.people.insert({
	name: "Nilesh",
	email: "nilesh@sunbeaminfo.com",
	phone: "9527331338",
	age: 38
});

db.people.insert({
	name: "Amit",
	email: "amit.kulkarni@sunbeaminfo.com"
});

db.people.insert({
	name: "Sunbeam Infotech",
	site: "www.sunbeaminfo.in"
});

db.people.find();

show collections;

show databases;
```

## Java Script
* Most used in web application development.
	* Front-end -- Runs in client browser.
		* Tecnologies/Frameworks based on JS: React, Angular, jQuery, Bootstrap, plain JS.
	* Back-end -- Runs on server side.
		* Tecnologies/Frameworks based on JS: Node JS, Express.
* JS is also used for databases.
	* Mongo Db.
* JS Syntax
	* JS is case-sensitive language.
	* JS is an object-based language.
		* obj.method(args)
		* outerObj.innerObj.method(args)
	* JS statement ends with semicolon. Semicolon is optional.
	* // single line comment
	* /* multi-line comment */
	* Strings: "string1", 'string2', etc.
	* Boolean: true, false
	* Numbers: 123.484, -23, 0, -3.1415, etc.
	* Arrays: [ v1, v2, v3, ... ]
	* Object: { "key1": value1, "key2": value2, "key3": value3, ... }
		* Keys are always string. "..." or '...'
		* Value can be any type.
	* null

### JSON
* Mongo stores each record (document) as a JSON document (in binary format -- BSON).
* JSON -- Java Script Object Notation
	* Similar to Python dictionary.

```JS
{
	"name": "Bill Gates",
	"age": 62,
	"email": "bill@microsoft.com"
}
```

```JS
{
	"name": {
		"sal": "Mr"
		"fname": "Mark",
		"lname": "Zukerberg"
	},
	"addr": {
		"country": "USA",
		"city": "New York",
		"zip": 723792
	},
	"business": [
		"Facebook", "Whatsapp", "Instagram"
	]
}
```

* In Mongo, JSON fields may NOT be encapsulated in quotes.
	* If using space or special char, quotes are compulsory.

```JS
{
	name: 'Nilesh Ghule',
	age: 38,
	designation: 'Technical Director',
	addr: {
		city: 'Pune',
		country: 'India',
		pin: 411046
	},
	hobbies: [ 'Programming', 'Reading', 'Cooking', 'Music' ],
	academics: [
		{
			std: '10th', marks: 78.66, passing: 1998, board: 'Maharashtra'
		},
		{
			std: '12th', marks: 77.00, passing: 2000, board: 'Maharashtra'
		},
		{
			std: 'B.Sc.', marks: 60.00, passing: 2004, university: 'Pune'
		},
		{
			std: 'M.Sc.', marks: 60.00, passing: 2008, university: 'Pune'
		}
	],
	political: false,
	rating: null
}
```

## Mongo Data Types
* Mongo store all data in binary JSON format.
* Mongo supports all JS data types. Additionally defines "extra" data types.
* Internally each data type is identified by a number.
	* string --> 2
	* boolean --> 8
	* array --> 4
	* null --> 10
	* numeric --> 1 or 16 or 18
		* 3.14, -12, 0
		* NumberInt('123'), NumberLong('123456')
	* date --> 9
		* new Date() -- current date
		* ISODate('yyyy-MM-ddThh:mm:ss')
	* object --> 3
		* { ... }
	* ObjectId

## Mongo Security
* In Mongo, by default Security is disabled.
* By default, mongo admin (root) login is done.
* Mongo security should be enabled separately.

## Mongo queries
* Most of mongo queries are -- calls to JS functions in some JS objects.
* For Most of queries (JS methods), args are JSON objects.

```JS
db.help()

db.createCollection
// only method name without ()

db.people.help()

db.people.insert
// double tab

db.people.insert

db.people.insertOne

db.people.insertMany

db.people.find().help()
```

* Mongo online documentation is too rich.
	* Syntax
	* Concepts
	* Examples

* Emp Dept -- Copy contents of empdept.js and paste on Mongo shell.

```JS
show collections

db.dept.find()

db.emp.find()
```

* find() method returns a cursor object.
	* Cursor object is used to access documents one by one (like Iterator in Java).
	* Cursor methods
		* pretty() -- format output
		* sort() -- ORDER BY clause
		* limit(n) -- LIMIT n
		* skip(m).limit(n) --> LIMIT m,n 
		* forEach() --> custom operation on each record.

```JS
db.emp.find().pretty()

// SELECT * FROM emp ORDER BY sal;
db.emp.find().sort({sal: 1})

// SELECT * FROM emp ORDER BY sal DESC;
db.emp.find().sort({sal: -1})

// SELECT * FROM emp ORDER BY deptno ASC, job DESC, sal DESC;
db.emp.find().sort({deptno: 1, job: -1, sal: -1})

// SELECT * FROM emp ORDER BY sal LIMIT 1;
db.emp.find().sort({sal: 1}).limit(1)

// SELECT * FROM emp ORDER BY sal LIMIT 1, 1;
db.emp.find().sort({sal: 1}).skip(1).limit(1)

//db.emp.find().forEach(JS function)
```

## Mongo Scripts
* Mongo script is JS file containing one/more mongo commands e.g. empdept.js.
* terminal> mongo --help
	* usage: `mongo [options] [db address] [file names (ending in .js)]`
		* db address -- name of database e.g. may21
		* mongo scripts -- path of js files
		* options
			* authentication options: -u user, -p password, -h server_host
* terminal> mongo may21 D:\may21\dbda\dbms\db\empdept.js

## Mongo Queries
* terminal> mongo

### Insert
* Maximum size of a document = 16 MB.

```JS
show dbs

use may21

db

show collections

db.dept.find()
db.emp.find()
```

```JS
db.people.insert({
	name: 'James Bond'
})
// WriteResult({ "nInserted" : 1 })

db.people.insertOne({
	name: 'Super Man'
})
// {
//    "acknowledged" : true,
//    "insertedId" : ObjectId("610bdf0b309f5754d4dc55c2")
// }

db.people.insertMany([
{
	name: 'SpiderMan'
},
{
	name: 'BatMan'
},
{
	name: 'ShakiMan'
}
])
// {
//   "acknowledged" : true,
//   "insertedIds" : [
//      ObjectId("610bdfa4309f5754d4dc55c3"),
//      ObjectId("610bdfa4309f5754d4dc55c4"),
//      ObjectId("610bdfa4309f5754d4dc55c5")
//    ]
// }
```

### Mongo Document Id
* In mongo each document must have a unique id -- name "_id". Similar to primary key in RDBMS.
* Internally UNIQUE index is created on this field.
* If user not given _id field, then it is auto-generated by the mongo client (for better performance).
* ObjectId --> 12 byte unique id
	* 3 bytes -- client machine id (unique id given by server).
	* 2 bytes -- client process id
	* 4 bytes -- timestamp (milli-seconds since epoch time)
	* 3 bytes -- counter (incremented for each record)

### Find

#### Syntax
```JS
db.emp.find
// args: query, fields, limit, skip, batchSize, options
//	query -- criteria (WHERE clause)
//	fields -- projection
//	limit & skip -- (LIMIT clause)
//	batchSize -- page size / record count
//	options

// if no arg given, all docs are fetched.
// Mongo shell display first 20 records only.
// for next 20 records give "it" - iterate command.

// query = {} -- empty object -- get all documents.
```

#### Projection
```JS
// projection: {field1: 1, field2: 1, ...}
// projection: {field1: true, field2: true, ...}
// projection: {field1: 0, field2: 0, ...}
// projection: {field1: false, field2: false, ...}

// SELECT _id, ename, sal, deptno FROM emp;
db.emp.find({}, {_id:1, ename:1, sal:1, deptno:1})
// inclusion projection

db.emp.find({}, {mgr:0, job:0, comm:0})
// exclusion projection

db.emp.find({}, {_id:1, ename:1, job:0, comm:0, sal:1})
// "errmsg" : "Cannot do exclusion on field job in inclusion projection"

// SELECT ename, job, sal FROM emp;
db.emp.find({}, {ename:1, job:1, sal:1});
// _id field is displayed by default.

db.emp.find({}, {_id:0, ename:1, job:1, sal:1});
// _id can be excluded in inclusion projection.
```

#### Query
* query -- { } -- json object
	* {} empty object -- no condition/criteria -- all 

```JS
db.emp.find({})
// show all documents -- non-pretty

db.emp.findOne({})
// show (arbitrary) first document matching condition -- pretty
```

* Relational operators
	* $eq, $ne, $gt, $lt, $gte, $lte

```JS
// SELECT * FROM emp WHERE deptno = 10;
db.emp.find({ deptno: 10 })

// SELECT * FROM emp WHERE job = 'MANAGER';
db.emp.find({ job: { $eq: 'MANAGER' } })

// SELECT * FROM emp WHERE sal >= 2500;
db.emp.find({ sal: { $gte: 2500 } })

// SELECT * FROM emp WHERE deptno != 30;
db.emp.find({ deptno: { $ne: 30 } })

// SELECT _id, ename, sal FROM emp WHERE sal < 1200;
db.emp.find({
	sal: { $lt: 1200 }
}, {
	ename: 1, sal: 1
})
```

```JS
//SELECT * FROM emp WHERE job IN ('MANAGER', 'ANALYST');
db.emp.find({ job: { $in: ['MANAGER', 'ANALYST'] } })

//SELECT * FROM emp WHERE job NOT IN ('SALESMAN', 'ANALYST', 'CLERK');
db.emp.find({ job: { $nin: ['SALESMAN', 'ANALYST', 'CLERK'] } })
```

* Logical operators
	* $and, $or, $nor, ~~$not~~
	* Used for combining multiple conditions -- [ {}, {}, {} ]

```JS
// SELECT * FROM emp WHERE sal BETWEEN 1500 AND 2500; -- sal >= 1500 AND sal <= 2500
db.emp.find({ sal: { $gte: 1500, $lte: 2500 } })

db.emp.find({ $and: [
	{sal: {$gte: 1500}},
	{sal: {$lte: 2500}}
]})

// SELECT * FROM emp WHERE deptno = 20 AND job = 'CLERK';
db.emp.find({
	$and: [
		{ deptno: 20 },
		{ job: 'CLERK' }
	]
})

// SELECT * FROM emp WHERE deptno = 10 OR job = 'MANAGER';
db.emp.find({
	$or: [
		{ deptno: 10 },
		{ job: 'MANAGER' }
	]
})

// SELECT * FROM emp WHERE NOT (deptno = 30);
db.emp.find({
	$nor: [
		{ deptno: 30 }
	]
});

//  SELECT * FROM emp WHERE NOT( deptno = 20 AND job = 'CLERK' );
db.emp.find({
	$nor: [
		{
			$and: [
				{ deptno: 20 },
				{ job: 'CLERK' }
			]
		}
	]
});

// SELECT * FROM emp WHERE NOT( deptno = 10 OR job = 'MANAGER' );
db.emp.find({
	$nor: [
		{
			$or: [
				{ deptno: 10 },
				{ job: 'MANAGER' }
			]
		}
	]
})

db.emp.find({
	$nor: [
		{ deptno: 10 },
		{ job: 'MANAGER' }
	]
})
```

```JS
db.emp.insertOne({_id: 1000, name: 'JOHN', sal: 1200, comm: null})
db.emp.insertOne({_id: 1001, name: 'DAGNY', sal: 1300, comm: null})

// display all emps which doesn't have comm field.
db.emp.find({ comm: { $exists: false } })

// display all emps which have comm value null.
db.emp.find({ comm: { $type: 10 } })
db.emp.find({ comm: { $type: 'null' } })

// display all emps which do not get comm i.e. either no comm field or comm is null.
db.emp.find({ comm: null })

db.emp.find({
	$or: [
		{ comm: { $exists: false } },
		{ comm: { $type: 'null' } }
	]
})
```

```JS
// SELECT * FROM emp WHERE ename REGEXP '^M';
db.emp.find({
	ename: { $regex: /^M/ }
})

// SELECT * FROM emp WHERE ename = 'King';
db.emp.find({
	ename: 'King'
})
// no record found

db.emp.find({
	ename: { $regex: /King/i }
})
// "i" -- case-insensitive regex compare.
```


















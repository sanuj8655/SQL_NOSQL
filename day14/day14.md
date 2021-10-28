# NoSQL - Mongo

## Agenda
* Update
* Delete
* Drop
* Aggregation pipeline

## Update

```JS
use may21

db

show collections

db.emp.find().pretty()

db.emp.update
// double tab

db.emp.update
// function(query, updateSpec, upsert, multi)
	// query --> WHERE clause
	// updateSpec --> modifications to be done (new object or changes)
	// upsert --> true/false
	// multi --> true (modify all records matching criteria) / false (modify one record matching criteria)

db.emp.updateOne
	// filter --> query
	// update --> updateSpec
	// only first matching record will be updated

db.emp.updateMany
	// filter --> query
	// update --> updateSpec
	// all matching records will be updated
```

* $set operator
	* Specify changes to be done into the document.
	* If new/updated document size is more than old document size, it will cause fragmentation into the collection (same as RDBMS).
* $inc operator
	* In-place update of numeric values. Specify the delta (difference).

```JS
// UPDATE emp SET sal = 1400 WHERE id = 1000;
db.emp.findOne({_id: 1000})

db.emp.update({_id: 1000}, {sal: 1400})
// old record is replaced by new record
// { "_id" : 1000, "name" : "JOHN", "sal" : 1200, "comm" : null } ---> { "_id" : 1000, "sal" : 1400 }

db.emp.findOne({_id: 1000})

// UPDATE emp SET sal = 1400 WHERE id = 1001;
db.emp.findOne({_id: 1001})

db.emp.update({_id: 1001}, {
	$set : {
		sal: 1400
	}
})

db.emp.findOne({_id: 1001})

// UPDATE emp SET ename='JOHN', age=32, comm=NULL WHERE id = 1000;
db.emp.update({_id: 1000}, {
	$set: {
		ename: 'JOHN',
		age: 32,
		comm: null
	}
})

db.emp.findOne({_id: 1000})

db.emp.update({_id: 1000}, {
	$inc: {
		age: +1
	}
})

db.emp.findOne({_id: 1000})

db.emp.update({_id: 1000}, {
	$inc: {
		sal: -100
	}
})

db.emp.findOne({_id: 1000})
```

### upsert 
* If present, UPDATE; otherwise INSERT. 

```JS
db.emp.findOne({_id: 1002})
// no record available

db.emp.update({_id: 1002}, {
	$set: {
		ename: 'JILL',
		sal: 2000,
		comm: null,
		deptno: 40
	}
})
// no record modified (because no record found).

db.emp.update({_id: 1002}, {
	$set: {
		ename: 'JILL',
		sal: 2000,
		comm: null,
		deptno: 40
	}
}, true)
// upsert = true --> if record for given criteria is not found, "insert new record with that criteria" and then update it.
// upsert = false (default) --> if record for given criteria is not found, then do nothing.

db.emp.update({
	ename: 'JACK',
	deptno: 40,
	job: 'TRAINER'
}, {
	$set: {
		sal: 2400,
		mgr: 7836
	}
},
true)
// new record will be inserted.

db.emp.findOne({ename: 'JACK'})

db.emp.update({
	ename: 'JACK',
	deptno: 40,
	job: 'TRAINER'
}, {
	$set: {
		sal: 2500,
		age: 34
	}
},
true)
// existing record will be updated.
```

## delete operation

```JS
db.emp.deleteOne
	// filter -- query

db.emp.deleteMany
	// filter -- query

db.emp.remove
	// t -- query
	// justOne -- true: delete first matching record, false: delete all matchin record (default)
```

```JS
// DELETE FROM emp WHERE ename = 'JACK';
db.emp.findOne({ename: 'JACK'})

db.emp.remove({ename: 'JACK'})

db.emp.findOne({ename: 'JACK'})

// delete all emps in which comm field is present.
db.emp.find({ comm: {$exists: true} })

db.emp.remove({ comm: {$exists: true} })

db.emp.find({ comm: {$exists: true} })

// DELETE FROM emp;
show collections

db.emp.remove({})

show collections

db.emp.find()
```

## Drop

```JS
// DROP TABLE dept
show collections

db.dept.drop()

show collections

db.dept.find({})
// no output
```

```JS
db.dropDatabase()
// drops current dropDatabas

show databases

db
```

## System databases

```JS
use admin

db

show collections

db.system.version.find()

use config

show collections

db.system.sessions.find()

use local

show collections

db.startup_log.find().pretty()
```

## Restore empdept database

```JS
show databases

use may21

db

show databases

// copy queries from empdept.js and paste on terminal.

show collections

show databases

exit
```

## mongoimport
* Nowadays is part of mongotools package. Need to install separately.
* Location: "C:\Program Files\MongoDB\Tools\100\bin"

```
terminal> mongoimport --help

terminal> mongoimport -d assignments -c restaurants C:\Users\admin\Desktop\retaurants.json

terminal> mongoimport -d may21 -c startups --type csv --headerline  C:\Users\admin\Desktop\50_startupss.csv

terminal> mongo
```

```JS
show databases

use assignments

db.restaurants.find().pretty()

use may21

show collections

db.startups.find().pretty()
```

## Mongo Aggregation pipeline

```JS
// SELECT * FROM emp WHERE deptno = 10;
db.emp.aggregate([
	{
		$match: { deptno: 10 }
	}
]).pretty()

// SELECT ename, job, sal FROM emp
db.emp.aggregate([
	{
		$project: {
			ename:1, job:1, sal:1
		}
	} 
])

// SELECT ename, job, sal FROM emp WHERE deptno = 10;
db.emp.aggregate([
{
	$match: { deptno: 10 }
},
{
	$project: {
		ename:1, job:1, sal:1
	}
} 
]).pretty()

// SELECT * FROM emp ORDER BY sal DESC LIMIT 3, 2;
db.emp.aggregate([
{
	$sort: { sal: -1 }
},
{
	$skip: 3
},
{
	$limit: 2
}
])
```

```JS
db.emp.aggregate([
{
	$group: {
		_id: '$job'
	}
}
])

// SELECT job, SUM(sal) FROM emp GROUP BY job
db.emp.aggregate([
{
	$group: {
		_id: '$job',
		total: { $sum: '$sal' }
	}
}
])
```

```JS
db.emp.aggregate([
{
	$group: {
		_id: '$job',
		salsum: { $sum: '$sal' },
		salavg: { $avg: '$sal' },
		salmax: { $max: '$sal' },
		salmin: { $min: '$sal' }
	}
}
]).pretty()

db.emp.aggregate([
{
	$group: {
		_id: '$job',
		salsum: { $sum: '$sal' },
		salavg: { $avg: '$sal' },
		salmax: { $max: '$sal' },
		salmin: { $min: '$sal' },
		cnt: { $count: {} }
	}
}
]).pretty()
// Mongo 5.x +

db.emp.aggregate([]).pretty()

db.emp.aggregate([
{
	$addFields: {
		one: 1
	}
}
]).pretty()

// SELECT job, SUM(sal), AVG(sal), MAX(sal), MIN(sal), COUNT(empno) FROM emp GROUP BY job;
db.emp.aggregate([
{
	$addFields: {
		one: 1
	}
},
{
	$group: {
		_id: '$job',
		salsum: { $sum: '$sal' },
		salavg: { $avg: '$sal' },
		salmax: { $max: '$sal' },
		salmin: { $min: '$sal' },
		cnt: { $sum: '$one' }
	}
}
]).pretty()

// SELECT deptno, job, COUNT(empno), SUM(sal) FROM emp GROUP BY deptno, job;
db.emp.aggregate([
{
	$addFields: {
		one: 1
	}
},
{
	$group: {
		_id: { dept: '$deptno', job: '$job' },
		cnt: { $sum: '$one' },
		salsum: { $sum: '$sal' }
	}
}
]).pretty()

// SELECT job, SUM(sal) FROM emp WHERE deptno IN (10, 20) GROUP BY job;
db.emp.aggregate([
{
	$match: { deptno: { $in: [10, 20] } }
},
{
	$group: {
		_id: '$job',
		salsum: { $sum: '$sal' }
	}
}
])

// SELECT job, AVG(sal) FROM emp GROUP BY job HAVING AVG(sal) >= 2700;
db.emp.aggregate([
{
	$group: {
		_id: '$job',
		avgsal: { $avg: '$sal' }
	}
},
{
	$match: {
		avgsal: { $gte: 2500.0 }
	}
}
])

// SELECT job, SUM(sal + IFNULL(comm,0)) FROM emp GROUP BY job;
db.emp.aggregate([
{
	$addFields: {
		commission: {
			$ifNull: [ '$comm', 0.0 ]
		}
	}
},
{
	$addFields: {
		income: {
			$add: [ '$sal', '$commission' ]
		}
	}
},
{
	$group: {
		_id: '$job',
		total: { $sum: '$income' }
	}
}
]).pretty()
```

```JS
// display all jobs for each dept.
//	output:
//		10:	MANAGER, PRESIDENT, CLERK
//		20: MANAGER, ANALYST, CLERK
//		30: MANAGER, SALESMAN, CLERK
db.emp.aggregate([
{
	$group: {
		_id: '$deptno',
		jobs: { $push: '$job' }
	}
}
]).pretty()

db.emp.aggregate([
{
	$group: {
		_id: '$deptno',
		jobs: { $addToSet: '$job' }
	}
}
]).pretty()

// display all emps (empno) for each job.
```

```JS
db.emp.find()
db.dept.find()

// SELECT d.*, e.* FROM emp e LEFT JOIN dept d ON e.deptno = d._id;
db.emp.aggregate([
{
	$lookup: {
		localField: 'deptno',
		from: 'dept',
		foreignField: '_id',
		as: 'depts'
	}
}
]).pretty()

// SELECT d.dname, e.ename FROM dept d LEFT JOIN emp e ON e.deptno = d._id;
db.emp.aggregate([
{
	$lookup: {
		localField: 'deptno',
		from: 'dept',
		foreignField: '_id',
		as: 'depts'
	}
},
{
	$project: {
		_id: 0,
		ename: 1,
		'depts.dname': 1
	}
}
]).pretty()

// SELECT d.*, e.* FROM dept d LEFT JOIN emp e ON e.deptno = d._id;
db.dept.aggregate([
{
	$lookup: {
		localField: '_id',
		from: 'emp',
		foreignField: 'deptno',
		as: 'emps'
	}
}
]).pretty();

// display all depts which doesn't have emps.
db.dept.aggregate([
{
	$lookup: {
		localField: '_id',
		from: 'emp',
		foreignField: 'deptno',
		as: 'emps'
	}
},
{
	$addFields: {
		empcnt: { $size: '$emps' } 
	}
},
{
	$match: { empcnt: 0 }
},
{
	$project: { dname: 1 }
}
]).pretty();

// display ename and his manager name -- self join.
```

```JS
// CREATE TABLE deptemps AS SELECT e.*, d.dname, d.loc FROM emps e INNER JOIN depts d ON e.deptno = d._id;
db.dept.aggregate([
{
	$lookup: {
		localField: '_id',
		from: 'emp',
		foreignField: 'deptno',
		as: 'emps'
	}
},
{
	$out: 'deptemps'
}
])

show collections

db.deptemps.find().pretty()
```


# NoSQL - Mongo Db

## Agenda
* Aggregation Pipeline
* Array operators
* Indexes
* Geo-spatial Indexes
* Grid FS
* Capped collection
* NoSQL concepts

## Aggregation Pipeline
* MySQL functions
	* Scalar functions
	* Group functions
	* Table functions
* $unwind is like a Table function.
	* Each document (with array field in it) will result into multiple documents.
	* $unwind unfolds array field into individual documents.

```JS
use may21

db.deptemps.find().pretty()

db.deptemps.aggregate([
{
	$unwind: '$emps'
}
]).pretty()

db.deptemps.aggregate([
{
	$unwind: '$emps'
},
{
	$addFields: { ename: '$emps.ename' }
},
{
	$project: { _id:0, dname:1, ename:1 }
}
]).pretty()
```

## Arrays

```JS
db.students.insert({
	"_id": 1,
	"name": "Nilesh",
	"hobbies": ["Programming", "Teaching"],
	"academics": [
		{
			"std": "10th",
			"passing": 1998,
			"marks": 78.66
		},
		{
			"std": "12th",
			"passing": 2000,
			"marks": 77.00
		},
		{
			"std": "B.Sc.",
			"passing": 2004,
			"marks": 60.00
		},
		{
			"std": "M.Sc.",
			"passing": 2008,
			"marks": 59.20
		}
	]
});

db.students.insert({
	"_id": 2,
	"name": "Sameer",
	"hobbies": ["Programming"],
	"academics": [
		{
			"std": "10th",
			"passing": 1998,
			"marks": 98.00
		},
		{
			"std": "12th",
			"passing": 2000,
			"marks": 94.00
		},
		{
			"std": "B.E.",
			"passing": 2004,
			"marks": 75.00
		}
	]
});

db.students.insert({
	"_id": 3,
	"name": "Unknown",
	"hobbies": ["Music", "Sports", "Drawing"],
	"academics": [
		{
			"std": "10th",
			"passing": 2004,
			"marks": 65.00
		},
		{
			"std": "12th",
			"passing": 2006,
			"marks": 64.00
		},
		{
			"std": "B.A.",
			"passing": 2009,
			"marks": 60.00
		}
	]
});

db.students.find().pretty();
```

### Query operators

```JS
// find all students which have hobbies field as array.
db.students.find({
	hobbies: { $type: 4 }
})

db.students.find({
	hobbies: { $type: 'array' }
})

// find all students which have two hobbies
db.students.find({
	hobbies: { $size: 2 }
})

// find all students who like "Programming"
db.students.find({
	hobbies: {
		$elemMatch: { $eq: 'Programming' }
	}
})

// find all students who passed 12th std in 2000.
db.students.find({
	academics: {
		$elemMatch: {
			$and: [
				{ std: '12th' },
				{ passing: 2000 }
			]
		}
	}
})

// find all students who scored less than 80% in 10th.
db.students.find({
	academics: {
		$elemMatch: {
			$and: [
				{ std: '10th' },
				{ marks: { $lt: 80.0 } }
			]
		}
	}
})

// find all students who scored less than 80% in 10th and like Programming.

// find all students who like Sports and Music.
db.students.find({
	$and: [
		{ hobbies: { $elemMatch: { $eq: 'Sports' } } },
		{ hobbies: { $elemMatch: { $eq: 'Music' } } }
	]
})

db.students.find({
	hobbies: { $all: [ 'Music', 'Sports' ] }
})

// find all students who like Teaching or Music.
db.students.find({
	$or: [
		{ hobbies: { $elemMatch: { $eq: 'Teaching' } } },
		{ hobbies: { $elemMatch: { $eq: 'Music' } } }
	]
})
```

### Projection operators

```JS
// display names of students & their hobbies who don't like Teaching.
db.students.find({
	$nor: [
		{
			hobbies: {
				$elemMatch: { $eq: 'Teaching' }
			}
		}
	]
}, {
	name: 1,
	hobbies: 1
})

// show first hobby of each student. -- hobbies[0]
db.students.find({}, {
	name: 1,
	hobbies: { $slice: [0, 1] }
})

// show second academics of each student.
db.students.find({}, {
	name: 1,
	academics: { $slice: [1, 1] }
})

// show name, std and marks for each student.
db.students.find({}, {
	name: 1,
	'academics.std': 1,
	'academics.marks': 1
}).pretty()

// show the first academics where student got more than 70%.
db.students.find({
	academics: {
		$elemMatch: {
			marks: { $gt: 70.0 }
		}
	}
}).pretty()

db.students.find({ // query
	academics: {
		$elemMatch: {
			marks: { $gt: 70.0 }
		}
	}
}, { // projection
	name: 1,
	'academics.$': 1
}).pretty()

// show the first academics where student got less than 70%.
db.students.find({ // query
	academics: {
		$elemMatch: {
			marks: { $lt: 70.0 }
		}
	}
}, { // projection
	name: true,
	'academics.$': true
}).pretty()

// find an emp in each dept having sal < 1200.0 --> deptemps collection
```

### Update operators

```JS
// add Cooking hobby to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: 'Cooking'
	}
})

db.students.findOne({name: 'Nilesh'})

// add Singing hobby to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: 'Singing'
	}
})

// add Programming hobby to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: 'Programming'
	}
})

db.students.findOne({name: 'Nilesh'})

// add Singing hobby to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$addToSet: {
		hobbies: 'Singing'
	}
})

db.students.findOne({name: 'Nilesh'})

// add Reading hobby to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$addToSet: {
		hobbies: 'Reading'
	}
})

db.students.findOne({name: 'Nilesh'})

// remove Programming hobby of Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$pull: {
		hobbies: 'Programming'
	}
})

db.students.findOne({name: 'Nilesh'})

// add multiple hobbies to Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$addToSet: {
		hobbies: {
			$each :
			 [
				'Programming',
				'Swimming',
				'Gardening',
				'Music',
				'Sports'
			]
		}
	}
})

db.students.findOne({name: 'Nilesh'})

// add Drawing hobby to Nilesh at position 4
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: {
			$each: [ 'Drawing' ],
			$position: 4
		}
	}
})

db.students.findOne({name: 'Nilesh'})

// add Painting hobby to Nilesh and sort hobbies
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: {
			$each: [ 'Painting' ],
			$sort: 1
		}
	}
})

db.students.findOne({name: 'Nilesh'})

// keep only first 5 hobbies for Nilesh
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: {
			$each: [ ],
			$slice: 5
		}
	}
})

db.students.findOne({name: 'Nilesh'})

// add Programming & Teaching and keep last 3 hobbies
db.students.update({
	name: 'Nilesh'
}, {
	$push: {
		hobbies: {
			$each: [ 'Programming', 'Teaching' ],
			$sort: 1,
			$slice: -3
		}
	}
})

db.students.findOne({name: 'Nilesh'})
```

## Query Performance

```JS
db.emp.find({ job: 'MANAGER' })

db.emp.find({ job: 'MANAGER' }).explain(false)

db.emp.aggregate([
{
	$match: { job: 'MANAGER' }
}
]);

db.emp.aggregate([
{
	$match: { job: 'MANAGER' }
}
], {
	explain: true
});
```

## Mongo Indexes
* Index types
	* Simple, Unique, Composite
	* Geo-spatial indexes
	* TTL indexes

```JS
// CREATE INDEX idx_job ON emp(job ASC);
db.emp.createIndex({job: 1})

db.emp.getIndexes()

db.emp.find({ job: 'MANAGER' }).explain(false)

db.emp.find({ job: 'MANAGER' })
```

```JS
// CREATE INDEX idx_dept_job ON emp(deptno ASC, job ASC);
db.emp.createIndex({deptno: 1, job: 1})

db.emp.getIndexes()

// SELECT * FROM emp WHERE deptno = 20 AND job = 'CLERK';
db.emp.find({
	$and: [
		{ deptno: 20 },
		{ job: 'CLERK' }
	]
})

db.emp.find({
	$and: [
		{ deptno: 20 },
		{ job: 'CLERK' }
	]
}).explain(false)
```

```JS
db.emp.createIndex

// CREATE UNIQUE INDEX idx_name ON emp(ename);
db.emp.createIndex({ename: 1}, {unique: true})

db.emp.find({ename: 'KING'})

db.emp.find({ename: 'KING'}).explain(false)
```

```JS
// DROP INDEX FROM emp(job);
db.emp.dropIndex({job: 1});

db.emp.getIndexes()
```

### TTL index
* TTL -- Time To Live

```JS
db.ttl.insert({_id: 1, time: new Date(), msg: 'Message 1'})

db.ttl.insert({_id: 2, time: new Date(), msg: 'Message 2'})

db.ttl.insert({_id: 3, time: new Date(), msg: 'Message 3'})

db.ttl.insert({_id: 4, time: new Date(), msg: 'Message 4'})

db.ttl.find()

// create an index on time field, so that records are auto-expired after 100 seconds.
// ttl index must be on "TIME" field.
db.ttl.createIndex({time: 1}, { expireAfterSeconds: 100 })

db.ttl.find()

db.ttl.insert({_id: 5, time: new Date(), msg: 'Message 5'})

db.ttl.find()

db.ttl.insert({_id: 6, time: new Date(), msg: 'Message 6'})

db.ttl.find()

db.ttl.insert({_id: 7, time: new Date(), msg: 'Message 7'})

db.ttl.find()

db.ttl.find()

```

### GeoSpatial Index
* Geo locations are "traditionally" are represented in longitude an latitude.
* Nowadays location info is used for various purposes
	* To mark some geo location (of a cab, of a building).
	* Driving directions (path -- set of points connected linearly).
	* Find nearby services (search locations/features/services within a radius)
	* To mark some region (rectangle or polygon).
* Geo information is stored as GeoJSON format (specification).
* Brower: geojson.io

* GeoJSON formats
	* type: Point, Line, Polygon
	* coordinates: 
		* Point: [long, lat]
		* Line: [ [long, lat], [long, lat], [long, lat], ... ]
		* Polygon: [ [ [long, lat], [long, lat], [long, lat], [long, lat], [long, lat], [long, lat] ] ]
			* Anti-clockwise coordinates
			* First and Last coordinates must be same

```
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [
              73.84479761123657,
              18.530255802879157
            ],
            [
              73.83906841278076,
              18.522992405106123
            ],
            [
              73.84677171707153,
              18.515403147338567
            ],
            [
              73.85717868804932,
              18.519574228807464
            ],
            [
              73.85737180709837,
              18.52773297700366
            ],
            [
              73.84479761123657,
              18.530255802879157
            ]
          ]
        ]
      }
    }
  ]
}
```

* Mongo GeoSpatial Indexes
	* 2d index -- legacy indexes on longitude & latitude (not for GeoJSON).
	* 2d sphere index -- newer indexes on GeoJSON fields.
	* haystack index -- for smaller area (within mall, ...).

* Mongo GeoSpatial operators
	* $geoWithin -- find locations within given area (rectangle or polygon).
	* $geoIntersects -- check if multiple regions/area are intersecting.
	* $geoNear -- find locations within a radius.

```JS
db.points.insert({
	name: 'p1',
	location: {
		type: 'Point',
		coordinates: [0.5, 0.5]
	}
});

db.points.insert({
	name: 'p2',
	location: {
		type: 'Point',
		coordinates: [0.75, 0.75]
	}
});

db.points.insert({
	name: 'p3',
	location: {
		type: 'Point',
		coordinates: [0.25, 0.25]
	}
});

db.points.insert({
	name: 'p4',
	location: {
		type: 'Point',
		coordinates: [1.5, 1.5]
	}
});

db.points.find();

db.points.find({
	location: {
		$geoWithin: {
			$geometry: {
				type: 'Polygon',
				coordinates: [
					[
						[0.0, 0.0],
						[1.0, 0.0],
						[1.0, 1.0],
						[0.0, 1.0],
						[0.0, 0.0]
					]
				]
			}
		}
	}
})
```

* terminal> mongoimport -d may21 -c busstops D:\may21\dbda\dbms\day15\busstops.json

```JS
show collections

db.busstops.find().pretty()

db.busstops.find({
	location: {
		$geoWithin: {
			$geometry: {
				type: 'Polygon',
				coordinates: [
					[
						[
						73.84479761123657,
						18.530255802879157
						],
						[
						73.83906841278076,
						18.522992405106123
						],
						[
						73.84677171707153,
						18.515403147338567
						],
						[
						73.85717868804932,
						18.519574228807464
						],
						[
						73.85737180709837,
						18.52773297700366
						],
						[
						73.84479761123657,
						18.530255802879157
						]
					]
				]
			}
		}
	}
});
```

```JS
db.busstops.createIndex({ location: '2dsphere' })

db.busstops.find({
	location: {
		$geoNear: {
			$geometry: {
				type: 'Point',
				coordinates: [
					73.85812282562256,
					18.52213786743616
				]
			},
			$maxDistance: 300
		}
	}
}).pretty()
```

## Capped Collections
* Collection with upper cap (limit).

```JS
db.createCollection('logs', {
	capped: true,
	size: 10240, // max size of the collection
	max: 10 // max number of documents
})
// older records will be auto-deleted if size/max exceed beyond limit.

show collections


db.logs.insert({_id: 1, time: new Date(), msg: 'Message 1'})

db.logs.insert({_id: 2, time: new Date(), msg: 'Message 2'})

db.logs.insert({_id: 3, time: new Date(), msg: 'Message 3'})

db.logs.insert({_id: 4, time: new Date(), msg: 'Message 4'})

db.logs.insert({_id: 5, time: new Date(), msg: 'Message 5'})

db.logs.insert({_id: 6, time: new Date(), msg: 'Message 6'})

db.logs.insert({_id: 7, time: new Date(), msg: 'Message 7'})

db.logs.insert({_id: 8, time: new Date(), msg: 'Message 8'})

db.logs.insert({_id: 9, time: new Date(), msg: 'Message 9'})

db.logs.insert({_id: 10, time: new Date(), msg: 'Message 10'})

db.logs.find()

db.logs.insert({_id: 11, time: new Date(), msg: 'Message 11'})

db.logs.insert({_id: 12, time: new Date(), msg: 'Message 12'})

db.logs.find()

db.logs.update({_id: 8}, {
	$set: { msg: 'Message A' }
})

db.logs.find()

db.logs.update({_id: 9}, {
	$set: { msg: 'Message XYZ' }
})
// error: cannot increase document size
```

## NoSQL
* NoSQL --> Not Only SQL.
* Goals/Reasons
	* High Performance
	* High Availability
	* High Scale (Data)
	* Flexible Schema
	* Semi-structured (JSON, XML) or Un-structured (Images, Audio, Video, Text)
* Scalability

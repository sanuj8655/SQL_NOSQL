# Neo4J
* Neo4j is the world's leading open source Graph Database.
* Developed by Neo technologies.
* Board of directors
	- Rod Johnson (Founder of Spring framework)
	- Chris Barchak
	- Magnus Christerson
	- Nikolaj Nyholm
	- Johan Svensson (CTO of Neo4j Inc).

## Features
* Developed in Java technology.
* Schema free (NoSQL).
* Highly scalable.
* ACID compliant transactional database.
* Reliable.
* Most suitable for data having many interconnecting relationships.
* Doesn't need complex joins.
* Cypher Query Language (CQL) to access/process data.
	- Easy traversal, retrieval
* Much faster than traditional databases.
* Constraints/Indexes are optional.
* Built-in web applications
	- Neo4j Browser
	- REST APIs for Java, Scala, Python, ... clients.

## Versions
* 1.0 - Feb 2010
* 2.0 - Dec 2013
* 3.0 - Apr 2016
* 3.5 - 2018
* 4.2 - Nov 2020

## Neo4J Graph concepts
* Data is stored in nodes/vertices and edges.
* Typically data is represented by nodes, while relationships are represented by edges.
* In Neo4J, relavent data can be associated with nodes as well as edges.
	- The node/edge can have properties & values (key-value pairs).
* Graph terminologies
	- Graph: Set of nodes and edges.
	- Node: Point in graph -- holds data.
	- Edge: Connections between nodes.
		- Directed graph
			- If each edge have direction, then graph is directed graph.
		- Weighted graph
			- If each edge have weight (value), then graph is weighted graph.
	- Adjacency:
		- If there is direct edge from node A to node B, then A is said to be adjacent (neighbour) to B.
	- Degree: Number of nodes connected to the node.
		- In-degree: Number of edges terminating on the node.
		- Out-degree: Number of edges originating from the node.
	- Path: Set of edges connecting two nodes.
	- Cycle: Path in which starting and ending node is same.
	- Loop: Direct edge from node to itself (shortest cycle).
* Graph algorithms
	- BFS -- Breadth First Search (Level wise search)
		- Finding shortest path between two nodes (non-weighted).
		- Searching in proxmity.
	- DFS -- Depth First Search
		- Searching in depth (for each branch).
	- Spanning tree algorithms
		- Sub-graph of a graph which contains all the nodes, but eliminates few edges to remove all cycles -- Spanning tree
		- Prim's algorithm & Kruskal's algorithm for min weight spanning tree.
			- Optimal resource usage
	- Single source shortest path
		- Shortest distance to each node from a given node.
		- e.g. Dijkstra's algo, Bellaman ford algo.
	- All pair shortest path
		- Shortest distance between each node pair.
		- e.g. Warshall Floyd algo
	- Union find
		- Cycle detection
	- A* search
		- Shortest distance between two nodes.
		- Gaming
	- Page rank
		- Developed by Google to find influential pages i.e. a page which is linked from many other pages. Used in google search.
* The graph algorithms like path finding, shortest path, page rank and many more are implemented in Neo4J.

## GraphDb vs RDBMS
* In GraphDb, data stored in graphs (nodes + properties & values). In RDBMS, data stored in tables (rows + columns & data).
* In GraphDb, node connections are represented as relationship (edges). In RDBMS, foreign key constraints represent relationship to other table.
* In GraphDb, graph traversal is used to access related data. In RDBMS, joins are used to retrieve related data.

## Installation

* On Ubuntu 18.04
	- wget --no-check-certificate -O - https://debian.neo4j.org/neotechnology.gpg.key | sudo apt-key add -
	- echo 'deb http://debian.neo4j.org/repo stable/' > /etc/apt/sources.list.d/neo4j.list
	- apt update
	- apt install neo4j

* Neo4J Sandbox
	- Free and cloud based installation
	- https://neo4j.com/sandbox/

## Neo4J data types
* Boolean
* Byte, Short, Int, Long
* Float, Double
* Char, String

## Neo4J CQL
* Cypher Query Language for access/traverse graph db.
* Declarative & pattern matching query language.
* Simple & human readable syntax.
* Supports WHERE, ORDER BY, LIMIT clauses and also Functions like SQL.

### Create Nodes

```CQL
CREATE (Nilesh:Staff { name: "Nilesh Ghule", job: "Technical Director" });

CREATE (Nitin:Staff { name: "Nitin Kudale", job: "Managing Director" });

CREATE (Pradnya:Staff { name: "Pradnya Dindorkar", job: "Course Coordinator", course: "DBDA"});

CREATE (Devendra:Staff { name: "Devendra Dhande", job: "Course Coordinator", course: "DESD" });

CREATE (Smita:Staff { name: "Smita Kadam", job: "Trainer" });
```

### Find Nodes

```CQL
MATCH (s:Staff)
RETURN s;

MATCH (s:Staff)
WHERE s.job = "Course Coordinator"
RETURN s;

MATCH (s:Staff)
WHERE s.job = "Course Coordinator" AND s.name = "Pradnya Dindorkar"
RETURN s;

MATCH (s:Staff)
WHERE s.job CONTAINS "Director"
RETURN s;
```

### Setup Relationship

```CQL
MATCH (a:Staff), (b:Staff)
WHERE a.name="Nilesh Ghule" AND b.name="Nitin Kudale"
CREATE (a)-[r: REPORTS_TO]->(b)
RETURN a, b;

MATCH (x:Staff), (a:Staff), (b:Staff), (c:Staff)
WHERE x.name="Nilesh Ghule" AND a.name="Pradnya Dindorkar" AND b.name="Devendra Dhande" AND c.name="Smita Kadam"
CREATE (a)-[r1: REPORTS_TO]->(x), (b)-[r2: REPORTS_TO]->(x), (c)-[r3: REPORTS_TO]->(x);

CREATE (a:Staff {name: "Abdul Rehman", job: "Placement Officer"})-[r: REPORTS_TO]->(b:Staff {name: "Atul Bhinge", job: "Placement Director"})
RETURN a, b;

MATCH (a:Staff), (b:Staff)
WHERE a.name="Atul Bhinge" AND b.name="Nitin Kudale"
CREATE (a)-[r: REPORTS_TO]->(b)
RETURN a, b;
```

### Create Constraints and Indexes

```CQL
CREATE CONSTRAINT ON (s:Staff) ASSERT s.name IS UNIQUE;

CREATE INDEX ON :Staff(job);

:schema

CALL db.schema.visualization;
```

## Neo4J clauses
1. RETURN: The RETURN clause is used to define what to include in the query result set.
2. ORDER BY: The ORDER BY clause is used to arrange the output of a query in order. It is used along with the clauses return or with.
3. LIMIT: The LIMIT clause is used to limit the rows in the result to a specific value.
4. SKIP: The SKIP clause is used to define from which row to start including the rows in the output.
5. WITH: The WITH clause is used to make two queries work together.
6. UNWIND: The UNWIND clause is used to expand a list into a sequence of rows.
7. UNION: The UNION clause is used to combine the result of multiple queries.
8. CALL: The CALL clause is used to invoke a procedure deployed in the database.

## Import CSV data
* https://gitlab.com/nilesh-g/test

```CQL
:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/dept.csv?inline=false" AS row
CREATE (:Dept {deptno: row.deptno, dname: row.dname, loc: row.loc });

CREATE CONSTRAINT ON (d:Dept) ASSERT d.deptno IS UNIQUE;

CREATE INDEX ON :Dept(dname);
```

```CQL
:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/emp.csv?inline=false" AS row
MERGE (d:Dept {deptno: row.deptno})
CREATE (e:Emp {empno: row.empno,ename: row.ename,job: row.job, mgr: row.mgr, hire: row.hire, sal: row.sal, comm: row.comm, deptno: row.deptno})
CREATE (e)-[:WORK_IN]->(d);

CREATE CONSTRAINT ON (e:Emp) ASSERT e.empno IS UNIQUE;

CREATE INDEX ON :Emp(ename);
```

```CQL
:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/emp.csv?inline=false" AS row
MATCH (e:Emp {empno: row.empno})
MATCH (m:Emp {empno: row.mgr})
MERGE (e)-[:REPORTS_TO]->(m);
```

## CQL Queries

* Find manager of SMITH

```CQL
MATCH (e)-[:REPORTS_TO]->(m)
WHERE e.ename='SMITH'
RETURN e, m;

MATCH (e)-[:REPORTS_TO]->(m)
WHERE e.ename='SMITH'
RETURN e.ename, m.ename;
```

* Find manager's manager of SMITH.

```CQL
MATCH (e)-[:REPORTS_TO]->(m)-[:REPORTS_TO]->(mm)
WHERE e.ename='SMITH'
RETURN e.ename, m.ename, mm.ename;
```

* Find all employees whose manager's manager is not king.

```CQL
MATCH (e)-[:REPORTS_TO]->(m)-[:REPORTS_TO]->(mm)
WHERE mm.ename<>'KING'
RETURN e.ename, m.ename, mm.ename;
```

* Find all employees whose managers are not working in their department.

```CQL
MATCH (e)-[:REPORTS_TO]->(m)
WHERE e.deptno <> m.deptno
RETURN e.ename, m.ename;
```

* Find all employees whose managers are not working in their department and also print names of departments.

```CQL
MATCH (e)-[:REPORTS_TO]->(m), (e)-[:WORK_IN]->(ed), (m)-[:WORK_IN]->(md)
WHERE e.deptno <> m.deptno
RETURN e.ename, ed.dname, m.ename, md.dname;
```

* Show schema

```CQL
:schema

CALL db.schema.visualization
```

* Find deptwise total sal of all emps.

```CQL
MATCH (e:Emp), (d:Dept)
WHERE e.deptno = d.deptno
RETURN e.deptno, d.dname, sum(toInt(e.sal))
ORDER BY e.deptno;
```

* Delete all WORK_IN relationships.

```CQL
MATCH (a)-[r:WORK_IN]->(b)
RETURN a,b;

MATCH ()-[r:WORK_IN]->()
DELETE r;
```

* Delete all depts

```CQL
MATCH (d:Dept)
DELETE d;

MATCH (d:Dept)
DETACH DELETE d;
```

* Delete all emps

```CQL
MATCH (e:Emp)
DETACH DELETE e;
```

## Bike Trips Example

```CQL
:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/stations1.csv?inline=false" AS row
CREATE (:Station {sid: row.station_id, name: row.name, landmark: row.landmark});

CREATE CONSTRAINT ON (s:Station) ASSERT s.sid IS UNIQUE;

CREATE INDEX ON :Station(name);

:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/trips1.csv?inline=false" AS row
MERGE (sfrom:Station {sid: row.start_terminal})
MERGE (sto:Station {sid: row.end_terminal})
CREATE (sfrom)-[:TRIP_TO {tid: row.id, duration: row.duration, start_date: row.start_date, start_terminal: row.start_terminal, end_date: row.end_date, end_terminal: row.end_terminal, bike: row.bike} ]->(sto);

MATCH ()-[r:TRIP_TO]->()
DELETE r;

MATCH (n:Station)
DETACH DELETE n;
```


```CQL
:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/stations.csv?inline=false" AS row
CREATE (:Station {sid: row.station_id, name: row.name, landmark: row.landmark});

CREATE CONSTRAINT ON (s:Station) ASSERT s.sid IS UNIQUE;

CREATE INDEX ON :Station(name);

:auto USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "https://gitlab.com/nilesh-g/test/raw/master/trips-sample.csv?inline=false" AS row
MERGE (sfrom:Station {sid: row.start_terminal})
MERGE (sto:Station {sid: row.end_terminal})
CREATE (sfrom)-[:TRIP_TO {tid: row.id, duration: row.duration, start_date: row.start_date, start_terminal: row.start_terminal, end_date: row.end_date, end_terminal: row.end_terminal, bike: row.bike} ]->(sto);
```

```CQL
CALL dbms.procedures();

CALL apoc.algo.pageRank('Station', 'TRIP_TO')
YIELD nodeId, score
RETURN algo.getNodeById(nodeId).name AS StationName,score
ORDER BY score DESC
LIMIT 10;
```



































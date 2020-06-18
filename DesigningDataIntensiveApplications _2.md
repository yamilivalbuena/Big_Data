# Designing Data-Intensive Applications
This is a summary of chapters 2 of "Designing Data-Intensive Applications" by Martin Kleppmann

### 1. What are some of the reasons why NoSQL databases started to be adopted?

* A need for greater scalability than relational databases can easily achieve, including very large datasets or very high write throughput
* A widespread preference for free and open source software over commercial database products
* Specialized query operations that are not well supported by the relational model
* Frustration with the restrictiveness of relational schemas, and a desire for a more dynamic and expressive data model

### 2. What is *polyglot persistence* about?

It is the idea that relational databases will continue to be used alongside a broad variety of nonrelational datastores.

### 3. What is an *object-relational impedance mismatch*?

It is a set of conceptual and technical difficulties that are often encountered when a relational database management system (RDBMS) is being served by an application program (or multiple application programs) written in an object-oriented programming language or style, particularly because objects or class definitions must be mapped to database tables defined by a relational schema.

https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch

### 4. What can you possibily do if a database does not support joins?

If the database itself does not support joins, you have to emulate a join in application code by making multiple queries to the database.

### 5. How the IMS database (the most popular database for business data processing in the 1970s) was similar to the JSON model?

The design of IMS used a fairly simple data model called the hierarchical model, which has some remarkable similarities to the JSON model used by document databases. It represented all data as a tree of records nested within records, much like the JSON structure.

### 6. What were the two most prominent solutions proposed to solve the limitations of the hierarchical model?

They were the relational model (which became SQL, and took over the world) and the network model (which initially had a large following but eventually faded into obscurity). The “great debate” between these two camps lasted for much of the 1970s.

### 7. How did the network model (CODASYL model) attempt to solve the inefficiencies of the hierarchical model?

The CODASYL model was a generalization of the hierarchical model. 
1. In the tree structure of the hierarchical model, every record has exactly one parent; in the network model, a record could have multiple parents.
2. The links between records in the network model were not foreign keys, but more like pointers in a programming language (while still being stored on disk).
3. A query in CODASYL was performed by moving a cursor through the database by iterating over lists of records and following access paths.

### 8. What was the biggest problem encountered by the network model?

The problem was that they made the code for querying and updating the database complicated and inflexible. With both the hierarchical and the network model, if you didn’t have a path to the data you wanted, you were in a difficult situation. You could change the access paths, but then you had to go through a lot of handwritten database query code and rewrite it to handle the new access paths. It was difficult to make changes to an application’s data model.

### 9. What does query optimization refer to?

Query optimization is the overall process of choosing the most efficient means of executing a SQL statement. SQL is a nonprocedural language, so the optimizer is free to merge, reorganize, and process in any order.

https://docs.oracle.com/database/121/TGSQL/tgsql_optcncpt.htm#TGSQL194

### 10. Which data model leads to simpler applcation code?

It’s not possible to say in general which data model leads to simpler application code; it depends on the kinds of relationships that exist between data items. For highly interconnected data, the document model is awkward, the relational model is acceptable, and graph models are the most natural.

### 11. What is the diference between *schema-on-read* and *schema-on-write*?

Schema-on-read: the structure of the data is implicit, and only interpreted when the data is read. 
Schema-on-write: the schema is explicit and the database ensures all written data conforms to it.

### 12. How does a declarative query language work?

In a declarative query language, like SQL or relational algebra, you just specify the pattern of the data you want—what conditions the results must meet, and how you want the data to be transformed (e.g., sorted, grouped, and aggregated)—but not how to achieve that goal. It is up to the database system’s query optimizer to decide which indexes and which join methods to use, and in which order to execute various parts of the query.

### 13. Why are declarative languages better for parallel execution compared to imperative code?

Imperative code is very hard to parallelize across multiple cores and multiple machines, because it specifies instructions that must be performed in a particular order. Declarative languages have a better chance of getting faster in parallel execution because they specify only the pattern of the results, not the algorithm that is used to determine the results. The database is free to use a parallel
implementation of the query language, if appropriate.

### 14. Is MapReduce declarative or imperative?

MapReduce is neither a declarative query language nor a fully imperative query API, but somewhere in between: the logic of the query is expressed with snippets of code, which are called repeatedly by the processing framework. It is based on the map (also known as collect) and reduce (also known as fold or inject) functions that exist in many functional programming languages.

### 15. What kinds of data can be modeled as graphs?

* Social graphs: Vertices are people, and edges indicate which people know each other.
* The web graph: Vertices are web pages, and edges indicate HTML links to other pages.
* Road or rail networks: Vertices are junctions, and edges represent the roads or railway lines between
them.

### 16. What are some characteristics of the graph model?

1. Any vertex can have an edge connecting it with any other vertex. There is no schema that restricts which kinds of things can or cannot be associated.
2. Given any vertex, you can efficiently find both its incoming and its outgoing edges, and thus traverse the graph—i.e., follow a path through a chain of vertices.
3. By using different labels for different kinds of relationships, you can store several different kinds of information in a single graph, while still maintaining a clean data model.

### 17. What is cypher?

Cypher is a declarative query language for property graphs, created for the Neo4j graph database. (It is named after a character in the movie The Matrix and is not related to ciphers in cryptography.)





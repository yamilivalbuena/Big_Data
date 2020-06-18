# Designing Data-Intensive Applications
This is a summary of chapters 1 of "Designing Data-Intensive Applications" by Martin Kleppmann

### What are some of the reasons why NoSQL databases started to be adopted?

* A need for greater scalability than relational databases can easily achieve, including very large datasets or very high write throughput
* A widespread preference for free and open source software over commercial database products
* Specialized query operations that are not well supported by the relational model
* Frustration with the restrictiveness of relational schemas, and a desire for a more dynamic and expressive data model

### What is *polyglot persistence* about?

It is the idea that relational databases will continue to be used alongside a broad variety of nonrelational datastores.

### What is an *object-relational impedance mismatch*?

It is a set of conceptual and technical difficulties that are often encountered when a relational database management system (RDBMS) is being served by an application program (or multiple application programs) written in an object-oriented programming language or style, particularly because objects or class definitions must be mapped to database tables defined by a relational schema.

https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch

### What can you possibily do if a database does not support joins?

If the database itself does not support joins, you have to emulate a join in application code by making multiple queries to the database.

### How the IMS database (the most popular database for business data processing in the 1970s) was similar to the JSON model?

The design of IMS used a fairly simple data model called the hierarchical model, which has some remarkable similarities to the JSON model used by document databases. It represented all data as a tree of records nested within records, much like the JSON structure.

### What were the two most prominent solutions proposed to solve the limitations of the hierarchical model?

They were the relational model (which became SQL, and took over the world) and the network model (which initially had a large following but eventually faded into obscurity). The “great debate” between these two camps lasted for much of the 1970s.

### How did the network model (CODASYL model) attempt to solve the inefficiencies of the hierarchical model?

The CODASYL model was a generalization of the hierarchical model. 
1. In the tree structure of the hierarchical model, every record has exactly one parent; in the network model, a record could have multiple parents.
2. The links between records in the network model were not foreign keys, but more like pointers in a programming language (while still being stored on disk).
3. A query in CODASYL was performed by moving a cursor through the database by iterating over lists of records and following access paths.

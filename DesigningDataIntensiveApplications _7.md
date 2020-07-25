# Designing Data-Intensive Applications 
This is a summary of chapter 7 of "Designing Data-Intensive Applications" by Martin Kleppmann.

### 1. What are transactions?
A transaction is a way for an application to group several reads and writes
together into a logical unit. Conceptually, all the reads and writes in a transaction are
executed as one operation: either the entire transaction succeeds (commit) or it fails
(abort, rollback). If it fails, the application can safely retry. With transactions, error
handling becomes much simpler for an application, because it doesn’t need to worry
about partial failure.

### 2. What does atomicity (provided by transactions) refer to?
ACID atomicity describes what happens if a client wants to make several
writes, but a fault occurs after some of the writes have been processed. If the writes are grouped together into an atomic
transaction, and the transaction cannot be completed (committed) due to a fault, then
the transaction is aborted and the database must discard or undo any writes it has
made so far in that transaction.

### 3. Why is it considered that the letter C does not belong in ACID?
Atomicity, isolation, and durability are properties of the database, whereas consistency
(in the ACID sense) is a property of the application. The application may rely
on the database’s atomicity and isolation properties in order to achieve consistency,
but it’s not up to the database alone. Thus, the letter C doesn’t really belong in ACID.

### 4. What does durability mean in a single-node database compared to a replicated database?
In a single-node database, durability typically means that the data has been written to
nonvolatile storage such as a hard drive or SSD. In a replicated database, durability
may mean that the data has been successfully copied to some number of nodes.

### 5. What is the technique that can provide absolute guarantees regarding replication and durability?
In practice, there is no one technique that can provide absolute guarantees. There are
only various risk-reduction techniques, including writing to disk, replicating to
remote machines, and backups—and they can and should be used together. As
always, it’s wise to take any theoretical “guarantees” with a healthy grain of salt.

### 6. Whatdoes TCP refer to?
TCP (Transmission Control Protocol) is a standard that defines how to establish and maintain a network conversation through which application programs can exchange data. TCP works with the Internet Protocol (IP), which defines how computers send packets of data to each other.

https://searchnetworking.techtarget.com/definition/TCP#:~:text=TCP%20(Transmission%20Control%20Protocol)%20is,of%20data%20to%20each%20other.
 




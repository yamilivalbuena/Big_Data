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

### 3. 



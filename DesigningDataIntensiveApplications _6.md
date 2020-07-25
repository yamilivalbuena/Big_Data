# Designing Data-Intensive Applications 
This is a summary of chapter 6 of "Designing Data-Intensive Applications" by Martin Kleppmann.

### 1. How are partitioning and replication related to each other?
Partitioning is usually combined with replication so that copies of each partition are
stored on multiple nodes. This means that, even though each record belongs to
exactly one partition, it may still be stored on several different nodes for fault tolerance.

### 2. What do the terms skewed and hotspot refer to?
If the partitioning is unfair, so that some partitions have more data or queries than
others, we call it skewed. The presence of skew makes partitioning much less effective.
In an extreme case, all the load could end up on one partition, so 9 out of 10 nodes
are idle and your bottleneck is the single busy node. A partition with disproportionately
high load is called a hot spot.

### 3. What are the disadvantages of partitioning randomly?
The simplest approach for avoiding hot spots would be to assign records to nodes
randomly. That would distribute the data quite evenly across the nodes, but it has a
big disadvantage: when you’re trying to read a particular item, you have no way of
knowing which node it is on, so you have to query all nodes in parallel.

### 4. What are the advantages and disadvantages of partitioning by key?
If you know the boundaries between the ranges, you can easily determine
which partition contains a given key. If you also know which partition is
assigned to which node, then you can make your request directly to the appropriate
node. However, the downside of key range partitioning is that certain access patterns can
lead to hot spots.

### 5. What are some tools that use automatic partitioning by key?
This partitioning strategy is
used by Bigtable, its open source equivalent HBase, RethinkDB, and MongoDB
before version 2.4.

### 6. Why when partitioning by hash of key the hash function needs not be cryptographically strong?
Many programming languages have simple hash functions built in
(as they are used for hash tables), but they may not be suitable for partitioning: for
example, in Java’s Object.hashCode() and Ruby’s Object#hash, the same key may
have a different hash value in different processes.

### 7. What are the disadvantages of partitioning by hash of key?
By using the hash of the key for partitioning we lose a nice
property of key-range partitioning: the ability to do efficient range queries. Keys that
were once adjacent are now scattered across all the partitions, so their sort order is
lost.

### 8. What does Cassandra combines both approaches for paritioning data (by key and by hash of key)?
Cassandra achieves a compromise between the two partitioning strategies. A table in Cassandra can be declared with a compound primary key consisting of
several columns. Only the first part of that key is hashed to determine the partition,
but the other columns are used as a concatenated index for sorting the data in Cassandra’s
SSTables. A query therefore cannot search for a range of values within the first column of a compound key, but if it specifies a fixed value for the first column, it
can perform an efficient range scan over the other columns of the key.

### 9. Why is a document-partitioned index also known as a local index?
It doesn’t care what data is stored in other partitions. Whenever you need to write to
the database—to add, remove, or update a document—you only need to deal with the
partition that contains the document ID that you are writing.

### 10. What are the advantages and disadvantages of a global (term-partitioned) index over a document-partitioned index?
The advantage of a global (term-partitioned) index over a document-partitioned
index is that it can make reads more efficient: rather than doing scatter/gather over
all partitions, a client only needs to make a request to the partition containing the
term that it wants. However, the downside of a global index is that writes are slower
and more complicated, because a write to a single document may now affect multiple partitions of the index (every term in the document might be on a different partition,
on a different node).

### 11. What are some minimum requirements that rebalancing is usually expected to meet no matter which partitioning scheme is used?
* After rebalancing, the load (data storage, read and write requests) should be
shared fairly between the nodes in the cluster.
* While rebalancing is happening, the database should continue accepting reads
and writes.
* No more data than necessary should be moved between nodes, to make rebalancing
fast and to minimize the network and disk I/O load.

### 12. What is a logical partition?
A logical partition (LPAR) is a subset of a computer's hardware resources, virtualized as a separate computer. In effect, a physical machine can be partitioned into multiple logical partitions, each hosting a separate instance of an operating system.

https://en.wikipedia.org/wiki/Logical_partition














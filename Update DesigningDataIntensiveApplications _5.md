# Designing Data-Intensive Applications 
This is a summary of chapter 5 of "Designing Data-Intensive Applications" by Martin Kleppmann.

## Chapter 5: Replication

### 1. What are some advantages of replicating data?
* It keeps data geographically close to the users (and thus reduce latency)
* It allows the system to continue working even if some of its parts have failed
(and thus increase availability)
* It scales out the number of machines that can serve read queries (and thus
increase read throughput)

### 2. How do leader-based replication work?
* When clients want to write to the database, they must send their requests to the
leader, which first writes the new data to its local storage.
* Whenever the leader writes new data to its local storage, it also sends
the data change to all of its followers as part of a replication log or change stream.
* When a client wants to read from the database, it can query either the leader or
any of the followers. However, writes are only accepted on the leader (the followers
are read-only from the client’s point of view).

### 3. What are some examples of tools that have replication as a built-in feature?
* Relational databases: PostgreSQL, MySQL, Oracle Data Guard, and SQL Server’s
AlwaysOn Availability Groups.
* Nonrelational databases: MongoDB, RethinkDB, and Espresso.
* Distributed message brokers: Kafka and RabbitMQ.
* Network filesystems: DRBD.

### 4. What is the difference between synchronous and asynchronous replication?
Most synchronous replication products write data to primary storage and the replica simultaneously. As such, the primary copy and the replica should always remain synchronized.
In contrast, asynchronous replication products copy the data to the replica after the data is already written to the primary storage.

https://cloudbasic.net/white-papers/synchronous-vs-asynchronous-replication/#:~:text=Most%20synchronous%20replication%20products%20write,written%20to%20the%20primary%20storage.

### 5. What are the advantages and disadvatanges of synchronous replication?
The advantage of synchronous replication is that the follower is guaranteed to have
an up-to-date copy of the data that is consistent with the leader. The disadvantage
is that if the synchronous follower doesn’t respond, the write cannot be processed.
The leader must block all writes and wait until the synchronous replica is available
again.

### 6. What is semi-synchronous replication?
If the synchronous follower becomes
unavailable or slow, one of the asynchronous followers is made synchronous.

### 7. What are the advantages and disadvatanges of fully asynchronous replication? 
Often, leader-based replication is configured to be completely asynchronous. In this
case, if the leader fails and is not recoverable, any writes that have not yet been replicated
to followers are lost. However, a fully asynchronous configuration
has the advantage that the leader can continue processing writes, even if all of its
followers have fallen behind.

### 8. How do you ensure that the new follower has an accurate copy of the leader’s data?
1. Take a consistent snapshot of the leader’s database at some point in time
2. Copy the snapshot to the new follower node.
3. The follower connects to the leader and requests all the data changes that have
happened since the snapshot was taken.
4. When the follower has processed the backlog of data changes since the snapshot, it can now continue to process data changes from the
leader as they happen.

### 9. How do you carry out a catch-up recovery for a follower failure?
If a follower crashes and is restarted, or if the network between the leader
and the follower is temporarily interrupted, the follower can recover quite easily:
from its log, it knows the last transaction that was processed before the fault occurred.
Thus, the follower can connect to the leader and request all the data changes that
occurred during the time when the follower was disconnected. When it has applied
these changes, it has caught up to the leader and can continue receiving a stream of
data changes as before.

### 10. What are the steps in an automatic failover process?
1. Determining that the leader has failed: most systems simply use a
timeout.
2. Choosing a new leader: the best candidate for
leadership is usually the replica with the most up-to-date data changes from the
old leader.
3. Reconfiguring the system to use the new leader: the system
needs to ensure that the old leader becomes a follower and recognizes the new
leader. 

### 11. What are the different replication methods?
* Statement-based replication: The leader logs every write request (statement) that it executes
and sends that statement log to its followers.
* Write-ahead log (WAL) shipping: The log is an append-only sequence of bytes containing all writes to the
database. When the follower processes this log, it builds a copy of the exact same data structures
as found on the leader.
* Logical (row-based) log replication: To use different log formats for replication and for the storage
engine, which allows the replication log to be decoupled from the storage engine
internals.
* Trigger-based replication: A trigger lets you register custom application code that is automatically executed
when a data change (write transaction) occurs in a database system. The trigger has
the opportunity to log this change into a separate table, from which it can be read by
an external process. That external process can then apply any necessary application
logic and replicate the data change to another system.

### 12. What does eventual consistency refer to?
If an application reads from an asynchronous follower, it may see outdated
information if the follower has fallen behind. This inconsistency is just a temporary state—if you stop writing to the
database and wait a while, the followers will eventually catch up and become consistent
with the leader.

### 13. What does read-after-write consistency refer to?
It is a guarantee that if the user reloads the page, they will always
see any updates they submitted themselves. It makes no promises about other users:
other users’ updates may not be visible until some later time. However, it reassures
the user that their own input has been saved correctly.

### 14. What do monotonic reads mean?
Monotonic reads only means
that if one user makes several reads in sequence, they will not see time go backward—
i.e., they will not read older data after having previously read newer data.

### 15. What does consistent prefix reads refer to?
This guarantee says that if a sequence of writes happens in a certain order,
then anyone reading those writes will see them appear in the same order.

### 16. In what situations the use of multi-leader replication is reasonable?
* Multi-datacenter operation: a database with replicas in several different datacenters can have a leader in each datacenter. Within each datacenter, regular leader–
follower replication is used; between datacenters, each datacenter’s leader replicates
its changes to the leaders in other datacenters.
* Clients with offline operation: if you have an
application that needs to continue to work while it is disconnected from the internet. From an architectural point of view, this setup is essentially the same as multi-leader
replication between datacenters.
* Collaborative editing: this approach allows multiple users
to edit simultaneously, but it also brings all the challenges of multi-leader replication,
including requiring conflict resolution.

### 17. What are multi-reader replication topologies?
A replication topology describes the communication paths along which writes are
propagated from one node to another. The most general topology is all-to-all, in which every leader sends its
writes to every other leader.

### 18. What is a leaderless replication?
Abandoning the concept of a
leader and allowing any replica to directly accept writes from clients. Amazon used it for its in-house Dynamo system
[37].vi Riak, Cassandra, and Voldemort are open source datastores with leaderless
replication models inspired by Dynamo.

### 19. Are failovers possible in a leaderless configuration?
In a leaderless configuration, failover does not exist. Two mechanisms are often used in Dynamo-style datastores:
* Read repair: When a client makes a read from several nodes in parallel, it can detect any stale
responses. The client sees that replica has a stale value and writes the newer value back to that replica. This approach
works well for values that are frequently read.
* Anti-entropy process: In addition, some datastores have a background process that constantly looks for
differences in the data between replicas and copies any missing data from one
replica to another. 

### 20. Can multi-datacenter operation be handled by leaderless replication?
Leaderless replication is
also suitable for multi-datacenter operation, since it is designed to tolerate conflicting
concurrent writes, network interruptions, and latency spikes. Cassandra and Voldemort implement their multi-datacenter support within the normal
leaderless model.

### CAP Theorem

The CAP theorem, also named Brewer's theorem after computer scientist Eric Brewer, states that it is impossible for a distributed data store to simultaneously provide more than two out of the following three guarantees:

1. Consistency: Every read receives the most recent write or an error
2. Availability: Every request receives a (non-error) response, without the guarantee that it contains the most recent write
3. Partition tolerance: The system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes

https://en.wikipedia.org/wiki/CAP_theorem

![](cap.png)

source: https://medium.com/system-design-blog/cap-theorem-1455ce5fc0a0

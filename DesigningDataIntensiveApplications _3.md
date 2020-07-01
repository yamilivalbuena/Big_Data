# Designing Data-Intensive Applications 
This is a summary of chapter 3 of "Designing Data-Intensive Applications" by Martin Kleppmann

### 1. What are the two things a database needs to do on the most fundamental level?
When you give it some data, it should store the data, and when you ask it again later, it should give the
data back to you.

### 2. What is a database log?
The database log, sometimes referred to as the transaction log, is a fundamental component of a database management system. All changes to application data in the database are recorded serially in the database log. Using this information the DBMS can track which transaction made which changes to the database. Furthermore, rollback and recovery operations utilize the database log to reset the database to a particular point-in-time.

(https://datatechnologytoday.wordpress.com/2014/02/10/the-log-is-the-database/)

### 3. What is an index?
An index is an additional structure that is derived from the primary data. Many databases
allow you to add and remove indexes, and this doesn’t affect the contents of the
database; it only affects the performance of queries.

### 4. Is an index as good for writing as it is for reading data?
For writes, it’s hard to beat the performance of
simply appending to a file, because that’s the simplest possible write operation. Any
kind of index usually slows down writes, because the index also needs to be updated
every time data is written.

### 5. Why is it better to choose the indexes manually rather than by default?
Well-chosen indexes speed up read queries, but every index slows down writes. For this reason, databases don’t usually
index everything by default, but require you—the application developer or database
administrator—to choose indexes manually, using your knowledge of the application’s
typical query patterns. You can then choose the indexes that give your application
the greatest benefit, without introducing more overhead than necessary.

### 6. If the database needs to handle several writes, how do we avoid eventually running out of disk space?
A good solution is to break the log into segments of a certain
size by closing a segment file when it reaches a certain size, and making subsequent
writes to a new segment file. We can then perform compaction on these
segments. Moreover, since compaction often makes segments much smaller (assuming that a
key is overwritten several times on average within one segment), we can also merge
several segments together at the same time as performing the compaction.

### 7. Are CSV files the best format for logs?
CSV is not the best format for a log. It’s faster and simpler to use a binary format
that first encodes the length of a string in bytes, followed by the raw string
(without need for escaping).

### 8. What is a convenient way to handle the deletion of records for logging?
If you want to delete a key and its associated value, you have to append a special
deletion record to the data file (sometimes called a tombstone). When log segments
are merged, the tombstone tells the merging process to discard any previous
values for the deleted key.

### 9. What could be a crash recovery solution for an in-memory hash map?
You can speed up recovery by storing a snapshot of each segment’s hash map on disk, which can be loaded into memory
more quickly.

### 10. How can the concurrency control of a log be ensured?
As writes are appended to the log in a strictly sequential order, a common implementation
choice is to have only one writer thread. Data file segments are
append-only and otherwise immutable, so they can be read concurrently by multiple
threads.

### 11. What are the advantages of having an append-only design for logs?
* Appending and segment merging are sequential write operations, which are generally
much faster than random writes, especially on magnetic spinning-disk
hard drives. To some extent sequential writes are also preferable on flash-based
solid state drives (SSDs).
* Concurrency and crash recovery are much simpler if segment files are appendonly
or immutable. For example, you don’t have to worry about the case where a
crash happened while a value was being overwritten, leaving you with a file containing
part of the old and part of the new value spliced together.
* Merging old segments avoids the problem of data files getting fragmented over
time.

### 12. What are some limitations of a hash table?
* The hash table must fit in memory, so if you have a very large number of keys,
you’re out of luck. In principle, you could maintain a hash map on disk, but
unfortunately it is difficult to make an on-disk hash map perform well. It
requires a lot of random access I/O, it is expensive to grow when it becomes full,
and hash collisions require fiddly logic.
* Range queries are not efficient. For example, you cannot easily scan over all keys
between kitty00000 and kitty99999—you’d have to look up each key individually
in the hash maps.

### 13. What is a SSTable?
SSTable stands for Sorted Strings Table a concept borrowed from Google BigTable which stores a set of immutable row fragments in sorted order based on row keys. SSTable files of a column family are stored in its respective column family directory.

(http://distributeddatastore.blogspot.com/2013/08/cassandra-sstable-storage-format.html#:~:text=SSTable%20stands%20for%20Sorted%20Strings,its%20respective%20column%20family%20directory.)

### 14. What are some tools that can be used for sorting data by key?
Maintaining a sorted structure on disk is possible, but
maintaining it in memory is much easier. There are plenty of well-known tree data
structures that you can use, such as red-black trees or AVL trees. With these data
structures, you can insert keys in any order and read them back in sorted order.

### 15. How can a memtable and a SSTable work together for writing the logs?
When a write comes in, it is added to an in-memory balanced tree data structure (for
example, a red-black tree). This in-memory tree is sometimes called a memtable. When the memtable gets bigger than some threshold—typically a few megabytes
—it is written out to disk as an SSTable file.

### 16. What is a bloom filter?
A Bloom filter is a data structure designed to tell you, rapidly and memory-efficiently, whether an element is present in a set. The price paid for this efficiency is that a Bloom filter is a probabilistic data structure: it tells us that the element either definitely is not in the set or may be in the set.

(https://llimllib.github.io/bloomfilter-tutorial/#:~:text=A%20Bloom%20filter%20is%20a,may%20be%20in%20the%20set.)

### 17. What is the difference between size-tiered and leveled compaction?
In size-tiered compaction,
newer and smaller SSTables are successively merged into older and larger
SSTables. In leveled compaction, the key range is split up into smaller SSTables and
older data is moved into separate “levels,” which allows the compaction to proceed
more incrementally and use less disk space.

### 18. What are some differences between LSM-Tress and B-Trees?
* LSM-Trees break the database down into variable-size
segments, typically several megabytes or more in size, and always write a segment
sequentially. By contrast, B-trees break the database down into fixed-size blocks or
pages, traditionally 4 KB in size (sometimes bigger), and read or write one page at a
time. This design corresponds more closely to the underlying hardware, as disks are
also arranged in fixed-size blocks.
* LSM-trees are typically faster for writes, whereas B-trees
are thought to be faster for reads. Reads are typically slower on LSM-trees
because they have to check several different data structures and SSTables at different
stages of compaction.
* LSM-trees can be compressed better, and thus often produce smaller files on disk
than B-trees. B-tree storage engines leave some disk space unused due to fragmentation:
when a page is split or when a row cannot fit into an existing page, some space
in a page remains unused.

### 19. What data structure can make a database with a B-Tree implementation resilient to crashes?
A write-ahead log (WAL, also known as a redo log). This is an append-only file to which every B-tree modification
must be written before it can be applied to the pages of the tree itself. When the database
comes back up after a crash, this log is used to restore the B-tree back to a consistent
state.

### 20. What is a data warehouse?
A data warehouse (DW or DWH), also known as an enterprise data warehouse (EDW), is a system used for reporting and data analysis, and is considered a core component of business intelligence. DWs are central repositories of integrated data from one or more disparate sources. They store current and historical data in one single place that are used for creating analytical reports for workers throughout the enterprise.

(https://en.wikipedia.org/wiki/Data_warehouse#:~:text=In%20computing%2C%20a%20data%20warehouse,one%20or%20more%20disparate%20sources.)


# Designing Data-Intensive Applications 
This is a summary of chapter 4 of "Designing Data-Intensive Applications" by Martin Kleppmann

## Chapter 4: Encoding and Evolution

### 1. Difference between schema-on-write and schema-on-read
Schema on write is defined as creating a schema for data before writing into the database. Schema on read differs from schema on write because you create the schema only when reading the data. 

https://www.thomashenson.com/schema-read-vs-schema-write-explained/

### 2. What is a rolling upgrade?
It means deploying new versions to a few nodes at a time,
checking whether the new version is running smoothly, and gradually working
your way through all the nodes. This allows new versions to be deployed without
service downtime, and thus encourages more frequent releases and better evolvability.

### 3. What is serialization and deserialization?
The translation from the in-memory representation of data to a byte sequence is called encoding (also
known as serialization or marshalling), and the reverse is called decoding (parsing,
deserialization, unmarshalling).

### 4. Does every programming language come with a built-in support for serialization?
Several object-oriented programming languages directly support object serialization (or object archival), either by syntactic sugar elements or providing a standard interface for doing so. The languages which do so include Ruby, Smalltalk, Python, PHP, Objective-C, Delphi, Java, and the .NET family of languages. There are also libraries available that add serialization support to languages that lack native support for it.

https://en.wikipedia.org/wiki/Serialization#Programming_language_support

### 5. What are some disadvantages of the most common standardized serialization formats (JSON, XML and CSV)?
* There is a lot of ambiguity around the encoding of numbers.
* JSON and XML have good support for Unicode character strings (i.e., humanreadable
text), but they don’t support binary strings.
* CSV does not have any schema, so it is up to the application to define the meaning
of each row and column.

### 6. How do Thrift and Protocol Buffers handle schema changes while keeping backward and forward compatibility?
*For field tags:*
* Each field is identified by its tag number and annotated with a datatype. If a field
value is not set, it is simply omitted from the encoded record.
* You can change the name of a field in the schema, since the encoded data never refers to field names, but
you cannot change a field’s tag, since that would make all existing encoded data
invalid.
* You can add new fields to the schema, provided that you give each field a new tag
number.
* If you add a new field, you cannot make it required.
* You can only remove a field that is optional and you can never use the same tag number
again.

*For datatypes*
* Changing the datatype of a field may be possible, but there is a risk that values will lose precision or get truncated.
* Thrift has a dedicated list datatype, which is parameterized with the datatype of the
list elements. This does not allow the same evolution from single-valued to multivalued
as Protocol Buffers does, but it has the advantage of supporting nested lists.

### 7. How does Avro handle datatypes?
The encoding simply consists of values concatenated together. A
string is just a length prefix followed by UTF-8 bytes. To parse the binary data, you go through the fields in the order that they appear in
the schema and use the schema to tell you the datatype of each field. This means that
the binary data can only be decoded correctly if the code reading the data is using the
exact same schema as the code that wrote the data.

### 8. How does Avro support schema evolution?
* The writer’s schema and the reader’s schema need to be compatible. When data is decoded (read), the Avro library resolves the differences by looking at the writer’s schema and the
reader’s schema side by side and translating the data from the writer’s schema into
the reader’s schema.
* If the code reading the data encounters a field that appears in the
writer’s schema but not in the reader’s schema, it is ignored.
* If the code reading the data expects some field, but the writer’s schema does not contain a field of that name,
it is filled in with a default value declared in the reader’s schema.

### 9. What are some rules for having a proper schema evolution in Avro?
* To maintain compatibility, you may only add or remove a field that has a default
value.
* If you want to allow a field to be null, you have to use a
union type.You can only use null as a default value if
it is one of the branches of the union.
* Changing a field name is backward compatible but not forward compatible. Similarly, adding a branch to a union type is backward compatible
but not forward compatible.

### 10. How does the reader know the writer’s schema with which a particular piece of data was encoded?
* A common use for Avro is for storing a large file containing millions of records, all encoded with the same schema. In this case, the writer of that
file can just include the writer’s schema once at the beginning of the file.
* Including a version number at the beginning of every encoded record, and to keep a list of schema versions in your database. A reader can fetch a record, extract the version number, and then fetch the
writer’s schema for that version number from the database.
* When two processes are communicating over a bidirectional network connection,
they can negotiate the schema version on connection setup and then use
that schema for the lifetime of the connection.

### 11. Is it necessary to always generate code that can implement schemas in a certain programming language?
* This is useful in statically typed languages such as Java, C++, or C#,
because it allows efficient in-memory structures to be used for decoded data, and it
allows type checking and autocompletion in IDEs when writing programs that access
the data structures.
* In dynamically typed programming languages such as JavaScript, Ruby, or Python,
there is not much point in generating code, since there is no compile-time type
checker to satisfy.

### 12. Is it possible to migrate data into a new schema?
Rewriting (migrating) data into a new schema is certainly possible, but it’s an expensive
thing to do on a large dataset, so most databases avoid it if possible. Most relational
databases allow simple schema changes, such as adding a new column with a
null default value, without rewriting existing data.

### 13. What is the basis of a microservices architecture?
Decomposing a large application into smaller services by area of functionality, such that one service
makes a request to another when it requires some functionality or data from that
other service. A key design goal of a service-oriented/microservices architecture is to make the
application easier to change and maintain by making services independently deployable
and evolvable.






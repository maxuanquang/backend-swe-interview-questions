# Topic: DATABASE

## Compare Relational DB (SQL) vs NoSQL. It's also really nice to know about newSQL (a kind of auto sharding DB which support SQL stuff but scale like NoSQL)

- Relational DB is organized by schema, table. All records in a table must be in the same structure, follow some constraints. Relational DB guarantees ACID characteristics so it is safe and stable.

- NoSQL is something that is not relational DB. Currently, it contains 4 main kinds:
  - The first kind is Document. In this kind, each piece of data is a JSON document (e.g. MongoDB, Amazon Dynamo DB).

    ```json
    {
        "_id": "12345",
        "name": "John Doe",
        "age": 30,
        "occupation": "Software Engineer",
        "hobbies": ["Hiking", "Reading", "Music"],
        "address": {
            "street": "10 Main Street",
            "city": "New York",
            "state": "NY",
            "zip": "10001"
        }
    }
    ```

  - The second is Key-Value DB. Simply, it is like a hash map that maps one key to one value (e.g. Redis).

    ```json
    key: "user:12345"
    value: {
        "name": "John Doe",
        "age": 30,
        "occupation": "Software Engineer"
    }
    ```

  - The third is Wide Column DB. Records in a table can have a different number of columns (e.g. Cassandra).

    ```json
    RowKey: "user:12345"
    ColumnFamily: "profile"
    Column: "name"
    Value: "John Doe"
    Column: "age"
    Value: 30
    Column: "occupation"
    Value: "Software Engineer"
    ```

  - The last one is Graph which is suitable for some graph-like data, for example, connections of people on a social network.

- NewSQL is a class of RDBMS that aims to bridge the gap between traditional relational database and NoSQL databases. It seeks to provide scalability and performance of NoSQL while maintaining the ACID compliance.

## How these 2 types of database scale up?

- Usually, people scale up Relational DB vertically. Add more CPU, more RAM, more storage. An additional thing we can do is sharding, that device data into different clusters, different nodes. Due to ACID characteristics, relational DB needs to do more work when writing and reading data to ensure those characteristics.
- Cause NoSQL sacrifices one or some ACID characteristics, it can be easily scaled horizontally just by simply adding nodes.

## 3 normal forms in DBMS

- First Normal Form (1NF):
  - A table is in 1NF if it meets the following criteria:
    - Each column in the table contains only atomic (indivisible) values.
    - Each row in the table is unique; there are no duplicate rows.
  - 1NF ensures that each cell in the table contains a single value and that rows are distinct.

- Second Normal Form (2NF):
  - A table is in 2NF if it meets the following criteria:
    - It is already in 1NF.
    - It does not have partial dependencies.
  - Partial dependency occurs when a non-key attribute depends on only part of the primary key.
  - To achieve 2NF, you often need to break the table into multiple related tables and establish relationships between them.

- Third Normal Form (3NF):
  - A table is in 3NF if it meets the following criteria:
    - It is already in 2NF.
    - It does not have transitive dependencies.
  - Transitive dependency occurs when a non-key attribute depends on another non-key attribute. In 3NF, all non-key attributes must depend directly on the primary key.

- Boyce-Codd Normal Form (BCNF):
  - A table is in BCNF if it meets the following criteria:
    - It is already in 3NF.
    - A table is in BCNF if, for every non-trivial functional dependency (X -> Y), X is a key.
  - Functional dependency: X is Security Number, Y is Name then X -> Y.
  - Achieving BCNF often requires breaking a table into multiple related tables and establishing relationships between them.

- Fourth Normal Form (4NF):
  - A table is in 4NF if it meets the following criteria:
    - It is already in BCNF.
    - A table is in 4NF if, for every multi-valued dependency (X ->> Y), X is a key.

## ACID of SQL and BASE of NoSQL? Why NoSQL is eventual consistency?

ACID:

- Atomicity: A transaction is considered an atomic unit, either all operations within the transaction succeed, or all of them fail.
- Consistency: Data should remain in consistent state after each transaction.
- Isolation: In case many transactions running concurrently, they shouldn't affect each order.
- Durability: Once a transaction is committed, its changes should be permanent and durable.

BASE:

- Basically Availability: NoSQL databases prioritize availability.
- Soft state: Data is not guaranteed to be consistent across all nodes at all time.
- Eventually consistency: Given enough time, all nodes will eventually have the same latest version of data.

## CAP theorem in this case. [This is a so nice graph](http://blog.nahurst.com/visual-guide-to-nosql-systems)

In CAP theorem:

- C stands for Consistency
- A stands for Availability
- P stands for Network Partition

It's kinda confused when people say we can only choose 2 out of three things because network partition is not something we can choose in a distributed system.
In the good condition, when there is no network partition, a system can guarantee both consistency and availability. But when the network fault comes, we have to choose one thing between Consistency and Availability to sacrifice.

## What is parameterized statement (in Java it's prepared statement)? How does it work **internally**?

A parameterized statement is a statement that we can customize, like changing filter conditions.

## What is SQL injection? How to avoid it?

SQL injection is when the attacker sends a parameter that contains SQL queries and hopes this query will be executed in the system's database.
SQL injection can be avoided by escaping special characters in the params that users submit to the system.

## How many "requests" you have to send to Database in a single prepared statement query? // one for compile and one for execute

It includes 2 steps, compile and execute.

First, the database management system will compile the statement template then store the result without executing it.

Next, the necessary values will be bound into the compiled statement template, then the statement will be executed.

## Can you reuse the compiled query multiple times? (does it help to speed up your application?)

Yes, we can reuse the compiled query multiple times with different bound values. And yes it speeds up the application.

## How indexing works internally?

Indexing adds a data structure with columns for the search conditions and a pointer. The pointer is the address on the memory disk of the row with the rest of the information. The query looks for the specific row in the index; the index refers to the pointer which will find the rest of the information.

## What algorithm and data structure indexing used? And why?

Index can use a hash table or B-Tree. [The index is decided to use depending on the data type and how you query the data](https://dev.mysql.com/doc/refman/8.0/en/index-btree-hash.html).

## How composite indexing works?

It's like a single index but the key is a compound key, which is the combined values of these columns that are indexed.

Let’s say we have an index defined on 4 columns — col1, col2, col3, col4. With a composite index, we have search capability on col1, (col1, col2) , (col1, col2, col3) , (col1, col2, col3, col4). So we can use any left side prefix of the indexed columns, but we can’t omit a column from the middle & use that like — (col1, col3) or (col1, col2, col4) or col3 or col4 etc. These are invalid combinations.

## How to know your query is using index?

By adding "explain" in front of the query, we will get how the query ran and if it uses index or not.

The query optimizer will use index automatically if possible and chose the index that it thinks is the best. And we can force our query to use an index by specifying it in the query.

## How index work in this case: `WHERE age = 5` and `Where age > 5`? The complexity to go to the next record?

It depends on the index type is hashing or B-Tree. If the index type is B-Tree, there is no difference between these two cases. The complexity to go to the next record is O(1) because all the leaves are linked together as a doubly linked list.

## Indexing with char?

For text fields, index on that column only is used when filter condition filters a prefix of this column.

## The complexity of SQL query? How to measure it? How SQL optimize a query?

//TODO: Answer this

## Compare `WHERE id = 'a' AND id = 'b' AND id = 'c'` vs `WHERE id in (a, b, c)`?

In MySQL, the values in the IN list will be sorted and MySQL will use binary search to check if the current value is in the list. So the check operator will cost O(log N) complexity, with N being the number of elements that need to be compared.

Some links to read more:

- <https://www.postgresql.org/message-id/12553.1135634231@sss.pgh.pa.us>

- <https://www.cybertec-postgresql.com/en/postgresql-indexing-index-scan-vs-bitmap-scan-vs-sequential-scan-basics/>

- <https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781785284335/11/ch11lvl1sec104/running-bitmap-heap-and-index-scan>

- <https://www.oreilly.com/library/view/high-performance-mysql/9780596101718/ch04.html>"

## Complexity of this query `SELECT * FROM abc ORDER BY name LIMIT 10 OFFSET 1000000` // SELECT 10 record from offset 10^6 after sort by name (which is a char)? How to optimize it?

O(N log N) with N being the total number of records.

We can optimize the query by indexing the column name.

## What is the complexity of COUNT(\*) query?

In MySQL, it depends on the storage engine that the table uses. For MyISAM, the total number of rows is stored with each table so the complexity is O(1), but for InnoDB, it is O(N).

## How to write query to avoid full table scan?

We need to write a query that takes advantage of index if any.

## Complexity of JOIN, INNER JOIN, OUTER JOIN?

To be defined.

## What is Database Replicating? When do we need it?

Database replicating is the act of making one or many copies of a database to increase the availability of data.

## What is bin log? How Master DB sync with Slave DB?

When data is written into DB, first it will be stored in Write Ahead Logs (bin logs). Master DB send these log to Slave DB so Slave DB can also have those changes.

## Can a Slave DB be a slave of another Slave DB (we do not need to sync from Master DB directly)?

Yes it can.

## What is Database Sharding? When we need it?

Sharding happens when a database is too big so the query time increases. We shard database to improve query time and also increase the availability of the system. If one shard is down, there is only a portion failure.

## Which rule we can apply to DB Sharding?

- Ensure unique key for the whole system.

- Distribute load.

## How to ensure primary key is globally unique when sharding?

We can have a key generating system that generates keys for all servers. But this server can be a central point of failure. To improve that, we can have multiple servers to generate keys, each server is in charge of some range of keys.

## How we can shard a table to multiple tables (same server) and multiple DB (multiple servers)?

To be defined.

## How query can work when we sharding (the data might be in the same or different tables/dbs)?

For a query that we might know exactly where data is using its key, we can detect which shard is holding necessary data and query on this shard.

For a query that data might be on a lot of shards, we can execute the query on all possible shards then aggregate data later.

## What is database transaction?

Relational DB ensures Atomicity by Transaction. A transaction is a collection of operations. Either all of those operations are executed, or non of them is executed. It ensures there is no time when something is failed in the middle and we have no idea when or how to roll back.

## How rollback works internally?

DBMS maintains a transaction log, which records all changes made to database. If a rollback is issued, DBMS use transaction log to undo the changes made during transaction, it read the transaction log in reverse order and do reverse action in each line.

## What is dirty read, dirty write, read skew (non-repeatable read), phantom read, write skew, lost update?

|  | Dirty read | Non-repeatable read | Phantom read |
|-----------------|------------|---------------------|--------------|
|Read Uncommited| Possible | Possible | Possible |
|Read Commited| Not Possible | Possible | Possible |
|Repeatable Read| Not Possible | Not Possible | Possible |
|Serializable | Not Possible | Not Possible | Not Possible |

- Dirty read is when transaction A is allowed to read a value that has been modified by transaction B but has yet been committed. This may cause a problem when transaction B fails and rollback happens, so the value that A reads is not the value in database.

- Read skew is when someone needs to read one value multiple times in a transaction. But the result each time is different. This happens when another transaction modifies the data between the two reads of the first transaction.

- Phantom read is when a transaction needs to find a collection of records multiple times in a transaction. But the number of records each time is different due to new rows being added or existing rows beign deleted by other transaction.

- Dirty write happens when a transaction overwrites data that another uncommitted transaction has written. This can lead to inconsistencies, as the first transaction's changes are lost, and the data integrity is compromised.

- Write skew is when someone needs to read some object then update some object based on the result of the read value.

- Lost update occurs when two transactions read the same data and then both update the data based on the read value. As a result, one of the updates is lost.

Read committed is a weak isolation level that is used to guarantee no dirty read and no dirty write. It assures that all object that can be read is already committed and no write can happen to an uncommitted object.

2 stronger isolation levels are snapshot isolation and serializable snapshot isolation. For snapshot isolation, a transaction can read the old version of a value while other transactions are writing. For serializable snapshot isolation, a transaction cannot read while other transactions are writing. It is called 2PL - two-phase locking.

## How transaction work when there are many concurrent requests?

To be defined.

## How to avoid race condition in DB? Read/Write/Range lock?

#### Read Lock (Shared Lock)

- Purpose: A read lock is used when a transaction needs to read data without modification.
- Behavior:
  - Multiple transactions can hold a read lock on the same data item simultaneously, allowing concurrent reads.
  - However, if a read lock is in place, transactions that wish to write to the locked data must wait until all read locks are released.
- Use Case: Useful in scenarios with frequent read operations and where it is acceptable to have concurrent reads, but updates need to be regulated to maintain data integrity.

#### Write Lock (Exclusive Lock)

- Purpose: A write lock is used when a transaction intends to modify (write or update) data.
- Behavior:
  - When a write lock is placed on a data item, no other transaction can read or write that data until the lock is released.
  - This is an exclusive lock, meaning it completely restricts other transactions from accessing the locked data.
- Use Case: Essential in scenarios where data integrity during updates is critical, preventing concurrent transactions from accessing data that is being modified.

#### Range Lock

- Purpose: A range lock is a more specific type of lock used to lock a range of records or keys.
- Behavior:
  - It locks a contiguous range of items based on key values or record positions, rather than a single item or entire table.
  - This is particularly useful in scenarios involving range queries or operations on a subset of records within a certain range.
- Use Case: Common in serializable isolation level to prevent phenomena like phantom reads. For instance, it's used to lock a range of records from being inserted, updated, or deleted by other transactions during a range query.

## What is optimistic locking and pessimistic locking?

Optimistic locking and pessimistic locking are two models for locking data in a database.

- Optimistic locking is based on the assumption that conflicts due to concurrent data updates are rare. Rather than locking data, it allows multiple transactions to proceed. Here's how it works:
  - Read Phase: A transaction reads data without accquiring locks, it just takes a version number of the data.
  - Write Phase (Commit Attempt): When the transaction attemps to write to the database, it checks if the data has been modified by another transactions by comparing the current data version with the one it having in hand.
  - Conflict Handling:
    - If the data has not been modified, the write is allowed to proceed, version of data is updated.
    - If the data has been modified, it needs to retry.
- Pessimistic locking assumes that conflicts are likely and thus locks data to prevent concurrent access by other transactions. Here's how it works:
  - Lock Acquisition: When a transaction wants to read or write data, it first acquires a lock on that data. Depends on what operation needed, read lock or write lock can be used.
  - Lock Release: After done reading, writing data, it releases the lock.

## Distributed transaction? How to make a transaction when a query needs to access multiple DB?

A distributed transaction is needed when data that suppose to be in a transaction is stored in different databases. There is a technique called 2 phase commit (2PC) that helps to resolve this situation.

There will be a coordinator which orchestrates the whole transaction.

- The first phase - Prepare: When the transaction begins, the coordinator generates a global transaction ID for all participants of this transaction. Each participant will execute their own transaction that attaches to the global transaction. When all local transactions are done, the coordinator sends the prepare request for all participants. Participants can respond yes or no, indicate if they can commit this transaction or not.
- The second phase - Commit: If all participants respond "yes", the coordinator can decide to commit this transaction and send the commit request to all participants. This is the second phase of 2PC, the commit phase. If any participant responds "no", the coordinator sends a rollback message to all participants.

## What is Try-Confirm-Cancel?

To be defined.

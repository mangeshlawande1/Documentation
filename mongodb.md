# MongoDB Commands – With Descriptions (Cheat Sheet)

This document explains the MongoDB commands you used, grouped by category, with short and clear descriptions.

---

## Database Commands

### Show all databases

```js
show dbs
```

Lists all databases on the MongoDB server.

### Switch / Create database

```js
use newdb
```

Switches to `newdb`. Creates it when first data is inserted.

---

## Create / Insert Commands

### Insert one document (old syntax)

```js
db.user.insert({ name: "Shridhar", age: 27 })
```

Inserts a single document (deprecated but still supported).

### Insert one document (recommended)

```js
db.user.insertOne({ name: "Manish", age: 40 })
```

### Insert multiple documents

```js
db.cars.insertMany([
  { Car: "c1", wt: 500 },
  { Car: "c2", wt: 600 },
  { Car: "c3", wt: 900 }
])
```

---

## Read / Find Commands

### Find all documents

```js
db.cars.find()
```

### Find with equality

```js
db.cars.find({ wt: 500 })
```

### Find using $in

```js
db.cars.find({ wt: { $in: [600, 900] } })
```

### Comparison operators

```js
db.cars.find({ wt: { $lt: 600 } })
db.cars.find({ wt: { $gt: 600 } })
```

### Multiple conditions (AND)

```js
db.cars.find({ Car: 'c1', wt: { $lt: 600 } })
```

### OR condition

```js
db.cars.find({ $or: [ { Car: 'c2' }, { wt: { $lt: 600 } } ] })
```

### AND condition (explicit)

```js
db.cars.find({ $and: [ { Car: 'c1' }, { wt: { $lt: 600 } } ] })
```

---

## Update Commands

### Update one document

```js
db.cars.updateOne({ Car: "c1" }, { $set: { wt: 400 } })
```

### Update multiple documents

```js
db.cars.updateMany(
  { wt: { $lt: 700 } },
  { $set: { Car: 'c0' } }
)
```

### Replace entire document

```js
db.cars.replaceOne(
  { Car: 'c3' },
  { Car: 'c3', wt: 1000 }
)
```

---

## Delete Commands

### Delete one

```js
db.bikes.deleteOne({ _id: ObjectId("563237a41a4d68582c2509da") })
```

### Delete many

```js
db.cars.deleteMany({})
```

### Remove (deprecated)

```js
db.students.remove({ age: { $gt: 20 } })
```

### Drop collection

```js
db.collection.drop()
```

---

## Sort Commands

### Ascending

```js
db.students.find().sort({ name: 1 })
```

### Descending

```js
db.students.find().sort({ age: -1 })
```

### Aggregation sort

```js
db.students.aggregate([{ $sort: { age: -1 } }])
```

---

## Skip & Limit

### Skip documents

```js
db.students.find().skip(1)
```

### Limit documents

```js
db.students.find().limit(1)
```

### Pagination

```js
db.students.find().skip(1).limit(1)
```

---

## Data Types

Common BSON types:

| Type    | Example    |
| ------- | ---------- |
| String  | "abc"      |
| Int     | 10         |
| Double  | 10.5       |
| Boolean | true       |
| Null    | null       |
| Array   | [1,2,3]    |
| Object  | {a:1}      |
| Date    | new Date() |

Insert example:

```js
var myDate = new Date()
```

---

## Conditional (IF–ELSE using $cond)

### Object syntax

```js
db.students.aggregate([
  { $project: { height: { $cond: { if: { $gt: ["$age", 30] }, then: 6.5, else: 7 } } } }
])
```

### Array syntax

```js
db.students.aggregate([
  { $project: { height: { $cond: [ { $gte: ["$age", 30] }, 6.5, 7 ] } } }
])
```

---

## Variables in Mongo Shell

```js
var x = 5
db.post.insertOne({ age: x })
```

---

## $type Operator

```js
db.type.find({ value: { $type: "object" } })
db.type.find({ value: { $type: "string" } })
db.type.find({ value: { $type: "bool" } })
db.type.find({ value: { $type: "double" } })
db.type.find({ value: { $type: "int" } })
db.type.find({ value: { $type: "array" } })
db.type.find({ value: { $type: "null" } })
```

---

## Index Commands

### Create index

```js
db.students.createIndex({ age: 1 })
db.students.createIndex({ height: 1 })
db.students.createIndex({ age: 1, height: 1 })
```

### View indexes

```js
db.students.getIndexes()
```

### Drop index

```js
db.students.dropIndex({ height: 1 })
db.students.dropIndexes({ age: 1, height: 1 })
```

---

## Pretty Print

```js
db.type.find({ _id: 7 }).pretty()
```

---

## Data Modeling

### Embedded Model

```js
db.model.insertOne({
  _id: 1,
  Std_ID: "1000DF3",
  name: { name: "Shridhar" },
  address: { addr: "ABC Gali" },
  phone: { phoneno: 9999999999 }
})
```

Used when data is frequently accessed together.

---

### Normalized Model

```js
db.normal.insertOne({ _id: 1, pid: "1001" })
db.normal.insertOne({ _id: 2, pcid: "1001", Name: "Shridhar" })
db.normal.insertOne({ _id: 3, pcid: "1001", mail: "abc@gmail.com" })
```

Used when data is large or reused.

---

## Query Operators Summary

| Operator | Meaning               |
| -------- | --------------------- |
| $eq      | equal                 |
| $ne      | not equal             |
| $gt      | greater than          |
| $gte     | greater than or equal |
| $lt      | less than             |
| $lte     | less than or equal    |
| $in      | in array              |
| $and     | logical AND           |
| $or      | logical OR            |
| $type    | data type filter      |

---

## Utility

Clear screen (Mongo shell):

```js
cls
```

(Node console):

```js
console.clear()
```

---


# MongoDB Interview Q&A (Beginner → Advanced)

This document contains commonly asked MongoDB interview questions with clear, concise answers. Suitable for freshers to experienced candidates.

---

## Beginner Level

### 1. What is MongoDB?

MongoDB is a **NoSQL, document-oriented database** that stores data in flexible JSON-like documents (BSON format). It is designed for high performance, high availability, and scalability.

---

### 2. What is a document in MongoDB?

A document is a single record stored in BSON format:

```json
{ "name": "Shridhar", "age": 27 }
```

It is similar to a row in relational databases.

---

### 3. What is a collection?

A collection is a group of documents, similar to a table in SQL.

---

### 4. Difference between SQL and MongoDB?

| SQL          | MongoDB                           |
| ------------ | --------------------------------- |
| Tables       | Collections                       |
| Rows         | Documents                         |
| Columns      | Fields                            |
| Fixed schema | Flexible schema                   |
| JOINs        | Embedded documents / manual joins |

---

### 5. What is BSON?

BSON is **Binary JSON**, a binary-encoded format used internally by MongoDB to support additional data types like Date, ObjectId, etc.

---

### 6. What is ObjectId?

A 12-byte unique identifier automatically generated for `_id` field containing:

* Timestamp
* Machine ID
* Process ID
* Counter

---

## CRUD Operations

### 7. How to insert data in MongoDB?

```js
db.users.insertOne({ name: "Amit", age: 30 })
```

---

### 8. How to read data?

```js
db.users.find({ age: { $gt: 25 } })
```

---

### 9. How to update data?

```js
db.users.updateOne({ name: "Amit" }, { $set: { age: 31 } })
```

---

### 10. How to delete data?

```js
db.users.deleteOne({ name: "Amit" })
```

---

## Indexing

### 11. What is an index?

An index improves **query performance** by avoiding full collection scans.

---

### 12. How to create an index?

```js
db.users.createIndex({ age: 1 })
```

---

### 13. Types of indexes in MongoDB?

* Single field
* Compound
* Multikey
* Text
* Geospatial
* Hashed

---

## Schema Design

### 14. Embedded vs Normalized model?

**Embedded:**

* Faster reads
* Data stored together

**Normalized:**

* Reduces duplication
* More flexible

---

### 15. When to embed data?

* One-to-few relationships
* Data accessed together
* Small document size

---

## Aggregation Framework

### 16. What is aggregation?

Used for **data processing and transformation** using pipeline stages like `$match`, `$group`, `$sort`.

---

### 17. Example aggregation

```js
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$user", total: { $sum: "$amount" } } }
])
```

---

## Advanced Level

### 18. What is replication?

Replication provides **high availability** by maintaining multiple copies of data using a **replica set**.

---

### 19. What is sharding?

Sharding is horizontal scaling by distributing data across multiple servers using a **shard key**.

---

### 20. Difference between replication and sharding?

| Replication       | Sharding           |
| ----------------- | ------------------ |
| High availability | Horizontal scaling |
| Same data copies  | Data partitioning  |

---

### 21. What is CAP theorem?

MongoDB follows **CP**:

* Consistency
* Partition tolerance

---

### 22. What is write concern?

Defines how many nodes must confirm a write before it is considered successful.

---

### 23. What is read preference?

Specifies whether reads go to primary or secondary nodes.

---

### 24. What is TTL index?

Automatically deletes documents after a certain time.

```js
db.logs.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })
```

---

### 25. What is journaling?

Ensures durability by writing operations to a log before committing to disk.

---

### 26. How MongoDB ensures ACID?

MongoDB supports ACID at:

* Document level (single document)
* Multi-document transactions (from v4.0+)

---

### 27. What is a covered query?

A query that is satisfied entirely using an index without accessing the documents.

---

### 28. What is $lookup?

Performs left outer join between collections.

```js
$lookup
```

---

### 29. What is schema validation?

Allows defining rules to enforce document structure.

---

### 30. What is Atlas?

MongoDB Atlas is MongoDB's fully managed cloud database service.

---

## Performance & Best Practices

### 31. How to optimize MongoDB performance?

* Use proper indexes
* Avoid large documents (>16MB)
* Use projection
* Use pagination
* Avoid unbounded arrays

---

### 32. What is projection?

Returns only selected fields.

```js
db.users.find({}, { name: 1, _id: 0 })
```

---

### 33. What is explain()?

Used to analyze query execution plan.

```js
db.users.find({ age: 25 }).explain("executionStats")
```

---

## Tricky Interview Questions

### 34. Max document size?

16 MB

---

### 35. Difference between remove() and deleteOne()?

* remove(): deprecated
* deleteOne(): recommended

---

### 36. Can MongoDB do joins?

Yes, using `$lookup` in aggregation.

---

### 37. Is MongoDB schema-less?

No. It is **schema-flexible**, not schema-less.

---

### 38. Can we rollback transactions?

Yes, using multi-document transactions.

---

### 39. Difference between find() and aggregate()?

* find(): simple queries
* aggregate(): data processing pipeline

---

### 40. When NOT to use MongoDB?

* Heavy joins required
* Strong relational constraints
* Complex multi-row transactions

---

## Bonus: SQL → MongoDB Mapping

| SQL      | MongoDB     |
| -------- | ----------- |
| SELECT   | find()      |
| INSERT   | insertOne() |
| UPDATE   | updateOne() |
| DELETE   | deleteOne() |
| JOIN     | $lookup     |
| GROUP BY | $group      |

---

Here is your content rewritten in a **clean, professional, and technically accurate** format, suitable for documentation, notes, or interview preparation.

---

# Parquet File Format

## Description

Apache Parquet is a **columnar storage file format** widely used in the **big data ecosystem**.
It is designed for **efficient analytical workloads** and is commonly used with tools such as:

* Apache Spark
* Hive
* Presto / Trino
* AWS Athena
* Google BigQuery (external tables)
* Hadoop ecosystems

---

## Key Features

---

### 1. Columnar Storage Format

Parquet stores data **column by column**, instead of row by row.

#### Benefits:

* Efficient queries on specific columns (only required columns are read)
* Reduced disk I/O
* Better CPU cache utilization
* Improved compression because similar data types are stored together

---

### 2. Efficient Compression

Because of columnar layout, Parquet achieves high compression using algorithms such as:

* Snappy (fast, commonly used)
* GZIP (higher compression ratio)
* Brotli
* ZSTD

#### Advantages:

* Reduced storage costs
* Faster read performance
* Lower network transfer size

---

### 3. Schema Evolution Support

Parquet supports controlled schema changes over time, including:

* Adding new columns
* Making columns optional/nullable
* Reordering columns

This allows:

* Backward compatibility
* Forward compatibility
* Stable long-running data pipelines

---

### 4. Splittable and Optimized for Big Data

Parquet files are **splittable**, meaning:

* A single large file can be processed by multiple workers in parallel
* Enables distributed processing in Spark / Hadoop

This leads to:

* Faster query execution
* Better cluster resource utilization

---

### 5. Support for Complex Data Types

Parquet supports nested and hierarchical structures, including:

* Arrays
* Maps
* Structs (similar to JSON objects)

Example:

```json
{
  "user": {
    "id": 123,
    "roles": ["admin", "editor"],
    "address": {
      "city": "Pune",
      "country": "India"
    }
  }
}
```

These structures are efficiently stored and queried using Parquet’s encoding model.

---

## When to Use Parquet

✅ Best for:

* Data warehouses
* Analytics workloads
* Data lakes
* Batch processing
* Reporting and BI queries

❌ Not ideal for:

* High-frequency small writes
* OLTP workloads
* Log streaming (Avro/JSON better)
* Real-time ingestion

---

## Quick Comparison

| Feature           | Parquet   | CSV  | JSON |
| ----------------- | --------- | ---- | ---- |
| Storage type      | Columnar  | Row  | Row  |
| Compression       | Excellent | Poor | Poor |
| Schema support    | Yes       | No   | Weak |
| Query performance | Very high | Low  | Low  |
| Big data friendly | Yes       | No   | No   |

---

## Final Summary

Apache Parquet is:

* Column-oriented
* Highly compressed
* Schema-aware
* Parallel-processing friendly
* Optimized for analytics at scale

It is one of the **default storage formats for modern data lakes and analytics platforms**.

---

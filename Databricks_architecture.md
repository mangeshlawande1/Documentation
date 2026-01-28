# Databricks & Lakehouse Architecture – Clean Notes and Requirements

This document rewrites and organizes the provided content in a clear, professional, and technically correct manner. Missing pieces and improvements have been added where appropriate.

---

## 1. Project Requirements

The platform should satisfy the following core requirements:

1. **Scalability**
   The system must scale horizontally and vertically to handle increasing or decreasing workloads and data volumes.

2. **Agility (Cost‑aware scalability)**
   The system should support easy **scale up / scale down** of compute resources at any time without worrying about long‑term infrastructure costs (pay‑as‑you‑go model).

3. **Unified Platform**
   A single platform to support:

   * Data engineering
   * Data analytics
   * Machine learning
   * Governance and security

---

## 2. Problem Statement

1. There is no **linear learning path** for users to move from fundamentals to advanced concepts.
2. The system should support a **project‑based approach** so users can start working on real use cases immediately and get productive quickly.

---

## 3. Databricks Overview

* Databricks (including the Free Edition) provides a **lakehouse platform** for data engineering, analytics, and AI.
* It typically follows a **3‑layer architecture** for data processing:

  * Bronze
  * Silver
  * Gold

### Distributed Computing in Databricks

Databricks uses Apache Spark for distributed computing:

* Large datasets are divided into smaller partitions.
* Each partition is processed in parallel on different machines.
* Results are aggregated to produce the final output.

This approach is commonly known as the **MapReduce** paradigm:

* **Map** → Process data in parallel
* **Reduce** → Aggregate results

---

## 4. Managed Tables vs External Tables

### Managed Tables

* Databricks manages:

  * Metadata
  * Actual data storage
* Multi‑tool access: **Difficult**
* Data governance: **Easy** (fully controlled by Unity Catalog)

**Use cases:**

* Quick analytics
* Fully governed datasets
* Internal teams using Databricks only

---

### External Tables

* Databricks manages **only metadata**
* Data is stored externally (e.g., S3, ADLS, GCS)
* Multi‑tool access: **Easy**
* Data governance: **Flexible but requires discipline**

**Use cases:**

* Data shared with other tools
* Pre‑existing datasets
* Multi‑platform environments

---

## 5. Medallion Architecture (Multi‑Hop Architecture)

The **Medallion Architecture** is a data design pattern used to logically organize data in a lakehouse. It incrementally improves data quality as it flows through layers:

> Bronze → Silver → Gold

It is also called a **multi‑hop architecture**.

---

## 6. Delta Lake and Lakehouse

### Definition

**Delta Lake = Data Lake + Data Warehouse capabilities**

It brings:

* ACID transactions
* Schema enforcement
* Time travel
* Versioning

Delta Lake is built on:

> Parquet files + Transaction log + Metadata

---

## 7. Data Flow (OLTP → OLAP)

* OLTP systems generate:

  * Batch data
  * Streaming data

This raw data is ingested into the lakehouse and processed as follows:

### Bronze Layer – Raw Data

* Landing zone for raw data
* No strict schema required
* Stores data exactly as received

### Silver Layer – Cleaned & Structured Data

* Data is filtered, cleaned, and enriched
* Schema is defined and enforced
* Schema evolution supported

### Gold Layer – Business Aggregates

* Business‑level aggregated data
* Optimized for analytics and reporting
* Continuously updated for downstream users and applications

---

## 8. Unity Catalog (Governance Layer)

Unity Catalog is a **unified governance solution** for data and AI assets in Databricks.

It provides:

* Centralized access control
* Auditing
* Data lineage
* Data discovery
* Cross‑workspace governance

---

## 9. Role‑Based Access Control (RBAC) Example

| Role / Group        | Access Type                 |
| ------------------- | --------------------------- |
| Data Analysts       | Read                        |
| Data Engineers      | Read + Write                |
| AI Engineers (ML)   | Read + Write (train models) |
| Software Developers | Read (model consumption)    |

---

## 10. Catalog Structure Design

Recommended structure based on Medallion Architecture:

### Level 1 – Catalogs (Domain‑based)

Examples:

* `sales_catalog`
* `marketing_catalog`
* `finance_catalog`

### Level 2 – Schemas (Layers)

Inside each catalog:

* `bronze`
* `silver`
* `gold`

### Level 3 – Objects

* Tables
* Views
* ML Models

Structure:

```
Catalog
  └── Schema (Database)
        └── Objects (Tables, Views, Models)
```

---

## 11. Data Lineage

Data lineage tracks:

* Where data originated
* Which systems processed it
* What transformations were applied
* When changes occurred

Example:

A transaction table may be updated by multiple systems. Lineage allows you to trace the full path of the data, which helps with:

* Debugging
* Root cause analysis
* Compliance
* Auditing

This is provided automatically by Unity Catalog.

---

## 12. Data Discovery

* You can add **tags and metadata** to datasets
* Enables easy searching across large organizations
* Improves collaboration and reuse

---

## 13. ACID Properties

### A – Atomicity

All steps in a transaction succeed together or fail together.

Example:

```
BEGIN TRANSACTION
UPDATE account A
UPDATE account B
COMMIT
```

No partial updates are allowed.

---

### C – Consistency

Transactions must maintain all rules and constraints.

Example constraint:

```
CHECK (balance >= 0)
```

The database always moves from one valid state to another valid state.

---

### I – Isolation

Concurrent transactions do not interfere with each other.
Each transaction behaves as if it is running alone.

---

### D – Durability

Once committed, data remains permanent even if the system crashes.

---

### Simple Example

When transferring $100:

* Atomicity → both debit and credit happen or none
* Consistency → balances remain valid
* Isolation → other transactions do not interfere
* Durability → transfer is permanent

---

## 14. Time Travel in Delta Lake

Delta Lake maintains historical versions of data using:

1. **Copy‑on‑write** – new records written instead of overwriting
2. **Transaction log** – records all changes (similar to Git history)

### Benefits

* Error recovery
* Audit and compliance
* Historical analysis

### Example Queries

```sql
SELECT * FROM my_table VERSION AS OF 5;

SELECT * FROM my_table TIMESTAMP AS OF '2024-12-01 10:00:00';

RESTORE TABLE my_table TO VERSION AS OF 10;
```

---

## 15. Delta Lake with AWS S3

S3 by itself:

* Stores files only
* No ACID guarantees
* No version control for tables
* No time travel
* Risk of accidental overwrite or corruption

Delta Lake on S3 provides:

* ACID transactions
* Schema enforcement
* Version history
* Time travel

External tables are typically used to connect Databricks to S3 data.

---

## 16. Problems Solved by Delta Lake

* No audit trail
* Data corruption
* Schema drift
* Unreliable batch pipelines

---

## 17. Data Lakehouse Workflow

1. Raw data (CSV, Parquet, etc.) stored in S3
2. Databricks creates external tables pointing to raw data
3. Bronze tables created in Delta format
4. Silver tables (cleaned & structured) in Delta
5. Gold tables (aggregated) in Delta

Delta Lake is the **core storage layer** of the lakehouse architecture.

---

## 18. Traditional (Legacy) Architecture

### Typical Flow

```
Application Server
   ↓
OLTP System (Relational DB)
   ↓
Python ETL Scripts
   ↓
S3 (Data Lake)
   ↓
Amazon Redshift (Data Warehouse)
   ↓
BI Tools
```

---

## 19. Issues with Legacy Architecture

* Fragmented ETL pipelines
* High maintenance overhead
* Limited scalability
* High cost
* No unified governance platform

---

## 20. Lakehouse Catalog Setup (Recommended)

* Use domain‑based catalogs
* Use Medallion layers as schemas
* Apply RBAC via Unity Catalog
* Enable lineage and discovery

---

## 21. Dimension Data Processing (Optional Enhancement)

* Start with **dimension tables** (customers, products, regions, etc.)
* Then process fact tables
* Supports star/snowflake schema design in the Gold layer

---

## 22. Missing / Suggested Additions

To make this architecture complete for production use, consider adding:

1. **Data Quality checks** (Great Expectations, Delta Live Tables expectations)
2. **Orchestration** (Airflow, Databricks Workflows)
3. **Monitoring & alerting**
4. **Cost optimization strategy** (job clusters, auto‑termination)
5. **CI/CD for data pipelines**
6. **Data security** (encryption at rest & in transit)
7. **Streaming architecture** (Kafka / Kinesis → Bronze)

---

# OCSF Mapping Guide – Legacy to Latest (Windows & OS Logs)

**Date:** 29–30 Dec 2025
**Scope:** OCSF v1.0.0 → v1.7.0 (dev)

---

## 1. Why OCSF Mapping Changes Over Time

From **OCSF v1.0.0 to v1.7.0 (dev)**, the framework evolved from a generic schema into a more **granular, platform-aware, and extensible system**.

The goals of this evolution:

* Support **legacy and modern OS logs** simultaneously
* Improve **event classification accuracy**
* Standardize analytics using **strict enums & numeric IDs**
* Enable vendors to extend safely without breaking compatibility

---

## 2. Key Naming & Categorization Changes

### 2.1 Shift to Platform Extensions

Early versions (v1.0.0) tried to normalize everything in the **core schema**.

Later versions introduced **platform extensions** (e.g., Windows extension):

* Legacy Windows logs (2008–2012) → mapped using extension logic
* Modern Windows logs (2016+, Windows 11, Server 2025) → richer mappings

This prevents cluttering the core schema while preserving OS-specific behavior.

---

### 2.2 Granular Event Class Naming

Earlier:

* Generic classes such as *Application Activity*

Later versions (v1.3–v1.6):

* Highly specific classes such as:

  * `IAM Analysis Finding`
  * `Software Inventory`
  * `Vulnerability Finding`

This improves:

* Detection precision
* Query accuracy
* SOC reporting clarity

---

### 2.3 Enriched Attribute Dictionary

Later versions added:

* OS-specific fields: `boot_uid`, `cpid`
* Better Windows registry objects
* Improved process lineage support

---

### 2.4 Standardized Enums

Modern OCSF enforces numeric enums:

* `severity_id`
* `activity_id`
* `network.direction_id`

Example:

```
Local = 4
Inbound = 1
Outbound = 2
```

This ensures analytics work identically across:

* Old OS logs
* New OS logs
* Different vendors

---

## 3. Strategy for Robust OCSF Mapping

### 3.1 Use Schema Browser as Source of Truth

Always verify against:

* OCSF schema definitions
* Attribute deprecations
* Extensions & profiles

---

### 3.2 Layered Mapping Architecture

#### Base Layer (Core)

Map common fields:

* Timestamp
* Actor (user, process)
* Device / host
* Class UID, category UID

#### Extension Layer

Use platform extensions:

* Windows Event IDs (4624, 4688, etc.)
* Sysmon specific fields
* Linux audit fields

---

### 3.3 Prefer Numeric Enums

Never store raw strings for analytics logic.

Map:

```
"Information" → severity_id = 1
"Error" → severity_id = 4
```

---

### 3.4 Versioned Pipelines

Use transformation layers such as:

* AWS Security Lake
* Datadog pipelines
* FluentBit / Vector / Logstash

This allows:

* Raw logs stay unchanged
* Output OCSF version can change independently

---

### 3.5 Metaschema Validation

Use JSON Schema validation (introduced v1.1.0) to:

* Enforce structure
* Detect missing mandatory fields
* Validate extensions & profiles

---

## 4. OCSF Event Model Refresher

### Category → Class → Activity

| UID | Category        | Description                |
| --- | --------------- | -------------------------- |
| 1   | System Activity | Processes, files, registry |
| 2   | Findings        | Alerts, detections         |
| 3   | IAM             | Auth, accounts, groups     |
| 4   | Network         | DNS, flows, firewall       |
| 5   | Discovery       | Inventory                  |
| 6   | Application     | APIs, web                  |
| 7   | Remediation     | Quarantine, block          |

---

### Event Classes (Templates)

Example:

IAM Category (3):

* 3001 → Account Change
* 3002 → Authentication
* 3003 → Group Management

Network Category (4):

* 4001 → Network Activity
* 4002 → HTTP Activity
* 4003 → DNS Activity

---

### Activity IDs (Actions)

Authentication (3002):

| activity_id | Meaning       |
| ----------- | ------------- |
| 1           | Logon success |
| 2           | Logoff        |
| 3           | Failure       |
| 6           | Lockout       |

`type_uid = class_uid * 100 + activity_id`

Example: `300201`

---

### Objects & Profiles

Objects:

* user
* process
* file
* network_connection

Profiles (optional overlays):

* cloud
* host
* security_control

---

## 5. How to Read an OCSF Event

Example:

* Category: 3 (IAM)
* Class: 3002 (Authentication)
* Activity: 1 (Logon)

Meaning:

> A successful login event

---

## 6. Windows Raw Log Variations

### A. Security Logs

Source: Microsoft-Windows-Security-Auditing

Legacy (2008–2012):

```
<Data Name="User">jdoe</Data>
<Data Name="Domain">CORP</Data>
```

Modern (2016+):

```
<Data Name="TargetUserName">jdoe</Data>
<Data Name="TargetDomainName">CORP</Data>
<Data Name="TargetUserSid">S-1-5-21-…</Data>
```

---

### B. Sysmon Logs

Versions differ:

* v10
* v13
* v15

Field changes example:

| Field  | v10                  | v13+        | OCSF                |
| ------ | -------------------- | ----------- | ------------------- |
| Hashes | MD5, SHA256 separate | combined    | process.file.hashes |
| Parent | ParentImage          | ParentImage | parent_process.name |
| User   | User                 | User        | actor.user.name     |

---

### C. PowerShell Logs

Event IDs:

* 4104 → Script block
* 4103 → Module logging

Contains embedded script text.

---

### D. Application/System Logs

* IIS
* SQL Server
* Exchange

Highly variable formats: XML, JSON, text

---

## 7. Windows Field Evolution Mapping Example

| Purpose      | Legacy          | Modern         | OCSF             |
| ------------ | --------------- | -------------- | ---------------- |
| Username     | User            | TargetUserName | actor.user.name  |
| SID          | UserSid         | TargetUserSid  | actor.user.uid   |
| Source IP    | WorkstationName | IpAddress      | src_endpoint.ip  |
| Process name | ProcessName     | NewProcessName | process.name     |
| Command line | N/A             | CommandLine    | process.cmd_line |

---

## 8. Consistent Hashing (Supporting Infrastructure Concept)

Consistent hashing distributes keys and servers into the same hash space so that:

> When servers change, only a small subset of keys are remapped.

Traditional hashing requires full rehashing when server count changes.

Consistent hashing uses a **hash ring (0 → 2³²)** where:

* Servers and keys are placed on the ring
* Keys map to nearest clockwise server

Used in:

* Kafka partitions
* Distributed caches
* Load balancers

---

## 9. OCSF Version Evolution Summary

| Version      | Key Changes                        |
| ------------ | ---------------------------------- |
| 1.0.0 (2023) | 6 categories                       |
| 1.1.0        | Validation schema, vulnerability   |
| 1.2.0        | Data security findings             |
| 1.3.0        | Remediation category               |
| 1.6.0        | Better Windows support             |
| 1.7.0-dev    | Enum refinement, platform maturity |

---

## 10. Final Best Practices

✔ Layered mapping (core + extension)
✔ Always map enums numerically
✔ Validate with schema
✔ Version your pipelines
✔ Normalize legacy and modern logs separately
✔ Use profiles for context
✔ Keep raw logs immutable

---

## 11. Final Takeaway

A robust OCSF mapping system must be:

* Version aware
* Platform aware
* Enum driven
* Validated
* Extension friendly

This ensures your SOC analytics remain stable even as:

* OS versions change
* Vendors update formats
* OCSF evolves

---

If you want, I can also add:

* Sample Windows 4624 → OCSF JSON mapping
* Sysmon → OCSF pipeline example
* Detection queries
* MITRE ATT&CK mappings
* Validation scripts

Just tell me 👍

---

## 12. File System Activity (Class UID: 1001)

File System Activity events report when a **process performs an action on a file or folder** (create, modify, delete, read, rename, etc.).

### Mandatory Constraints (for endpoint identity)

At least **one** of the following attributes must be present in the event:

* `hostname`
* `instance_uid`
* `interface_name`
* `interface_uid`
* `ip`
* `name`
* `uid`

This ensures the event can always be associated with a specific system or entity.

---

### Required & Optional Core Attributes

#### 1. activity_id (Required)

Valid values: **0–14, 99**

Defines the exact action performed.

Examples:

* 1 → Create
* 2 → Read
* 3 → Modify
* 4 → Delete
* 7 → Rename
* 99 → Other

---

#### 2. activity_name (Optional)

Human‑readable name derived from `activity_id`.

Example:

```json
"activity_name": "Delete"
```

---

#### 3. actor (Required)

The entity that performed the action.

Typically contains:

* `actor.process`
* `actor.user`

Example:

```json
"actor": {
  "process": { "name": "powershell.exe" },
  "user": { "name": "admin" }
}
```

---

#### 4. category_name (Optional)

Derived from `category_uid`.

For class 1001:

```json
"category_name": "System Activity"
```

---

#### 5. category_uid (Required)

| Value | Category        |
| ----- | --------------- |
| 1     | System Activity |

---

#### 6. class_uid (Required)

| Value | Class                |
| ----- | -------------------- |
| 1001  | File System Activity |

---

## 13. Container Object (Recommended)

The `container` object describes the container instance where the file system activity occurred.

A container is a **prepackaged, portable system image** running in isolation using a runtime such as `containerd`, `Docker`, or CRI‑O.

---

### Container Object Constraints

At least **one** of the following must be present:

* `name`
* `uid`

---

### Recommended Container Attributes

#### 1. hash (type: Fingerprint) – Recommended

Commit hash of the image or SHA‑256 hash of the container.

Example:

```json
"hash": {
  "sha256": "13550340a8681c84c861aac2e5b440161c2b33a3e4f302ac680ca5b686de48de"
}
```

---

#### 2. image (type: image) – Recommended

Container image used as the template to run the container.

Example:

```json
"image": {
  "name": "nginx",
  "tag": "1.25"
}
```

---

#### 3. name – Recommended

Human‑readable container name.

Example:

```json
"name": "payments-api-7f9c9"
```

---

#### 4. orchestrator – Optional

Platform managing the container.

Examples:

* `ECS`
* `EKS`
* `Kubernetes`
* `OpenShift`

---

#### 5. size (type: long) – Recommended

Size of the container image in bytes.

Example:

```json
"size": 134217728
```

---

#### 6. uid (type: string) – Recommended

Unique container instance identifier.

Example:

```json
"uid": "ac2ea168264a08f9aaca0dfc82ff3551418dfd22d02b713142a6843caa2f61bf"
```

---

## 14. Example: File Delete Inside a Container (OCSF)

```json
{
  "category_uid": 1,
  "class_uid": 1001,
  "activity_id": 4,
  "activity_name": "Delete",
  "actor": {
    "process": { "name": "rm" },
    "user": { "name": "root" }
  },
  "file": {
    "name": "secrets.txt",
    "path": "/app/secrets.txt"
  },
  "container": {
    "name": "payments-api-7f9c9",
    "uid": "ac2ea168264a08f9aaca0dfc82ff3551418dfd22d02b713142a6843caa2f61bf",
    "orchestrator": "Kubernetes",
    "image": {
      "name": "payments-api",
      "tag": "v3.4"
    }
  }
}
```

---

If you’d like, I can also add:

* Full activity_id table for File System Activity
* Windows vs Linux file activity mappings
* Sysmon Event ID 11 → OCSF 1001 mapping
* Kubernetes audit → OCSF file mapping
* Detection use‑cases (ransomware, web shells, data exfiltration)


I’ve added a complete, structured section to your canvas covering:

* **File System Activity (class_uid: 1001)**

  * Mandatory constraints
  * Required & optional attributes
  * Category and class details
  * activity_id explanation

* **Container object (recommended)**

  * Constraints
  * All recommended fields (hash, image, name, orchestrator, size, uid)
  * Clear descriptions

* **A full real-world OCSF JSON example**
  → File deletion inside a Kubernetes container

You can now use this as:

* SOC documentation
* OCSF mapping reference
* Training material
* Interview/system design notes

If you want, I can extend it with:

* Complete activity_id enumeration table (0–14, 99)
* Sysmon Event ID 11 → OCSF 1001 mapping
* Windows vs Linux file activity mapping
* Kubernetes audit logs → OCSF mapping
* Detection scenarios (ransomware, web shells, container escape)

Just tell me 👍

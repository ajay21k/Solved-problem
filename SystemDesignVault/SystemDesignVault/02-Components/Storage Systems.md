---
tags: [system-design, components, storage]
links: [[Databases]], [[CDN]], [[Design YouTube]]
---

# 💾 Storage Systems

> [!abstract] Summary
> Understanding when to use block, file, or object storage is key to designing scalable systems.

---

## 🔷 Block Storage

- Data stored as fixed-size **blocks** (like a hard drive)
- OS formats with a filesystem
- Low latency, high performance

| | Details |
|-|---------|
| **Examples** | AWS EBS, Google Persistent Disk, Azure Disk |
| **Use Case** | Databases, VMs, OS boot disks |
| **Access** | Mounted to single VM (usually) |

---

## 🔷 File Storage (NAS)

- Hierarchical **file/folder** structure
- Multiple clients can access simultaneously via NFS/SMB

| | Details |
|-|---------|
| **Examples** | AWS EFS, Azure Files, NFS |
| **Use Case** | Shared file systems, home directories, media |
| **Access** | Multiple VMs simultaneously |

---

## 🔷 Object Storage ⭐

- Data stored as **objects** with: data + metadata + unique ID
- Flat structure (no folders), accessed via HTTP API
- Infinite horizontal scale

| | Details |
|-|---------|
| **Examples** | AWS S3, Google Cloud Storage, Azure Blob |
| **Use Case** | Images, videos, backups, logs, static websites |
| **Access** | HTTP API from anywhere |

> [!tip] Object Storage is Your Friend
> For most user-generated content (profile photos, uploaded files, videos), use object storage + CDN. It's cheap, durable (11 9s), and globally accessible.

---

## 🆚 Storage Comparison

| Type | Structure | Scalability | Latency | Use Case |
|------|-----------|-------------|---------|----------|
| **Block** | Blocks | Limited (per volume) | Lowest | DB, VM disks |
| **File** | Hierarchy | Moderate | Low | Shared file access |
| **Object** | Flat (key-value) | Massive (infinite) | Higher | Media, backups |

---

## 🏭 Data Warehouse vs Data Lake

| Feature | Data Warehouse | Data Lake |
|---------|---------------|-----------|
| Data Type | Structured only | All types (raw, semi, unstructured) |
| Schema | Schema-on-write | Schema-on-read |
| Purpose | BI, reporting | ML, data exploration |
| Examples | Redshift, BigQuery, Snowflake | S3 + Athena, Hadoop, Databricks |
| Cost | Higher | Lower (raw storage) |

---

## 🔗 Related Notes
- [[Databases]] — Structured data storage
- [[CDN]] — Distribute object storage content globally
- [[Design YouTube]] — Videos in object storage + CDN


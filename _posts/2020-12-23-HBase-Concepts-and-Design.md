---
title: HBase Concepts and Design
date: 2020-12-23 10:31:22 +0800
categories: [bigdata, hbase]
tags: [hbase]
---

# What is HBase?
[Official Documents](http://hbase.apache.org/book.html#number.of.cfs)
Apache HBase is a **NoSQL database** that runs on top of Hadoop as a distributed and scalable big data store.
[Introduction to HBase Schema Design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)
# ACID Semantics
[acid-semantics documents](https://hbase.apache.org/acid-semantics.html)
- **Atomicity**
an operation is atomic if it either completes entirely or not at all
- **Consistency**
all actions cause the table to transition from one valid state directly to another (eg a row will not disappear during an update, etc)
- **Isolation**
an operation is isolated if it appears to complete independently of any other concurrent transaction
- **Durability**
any update that reports "successful" to the client will not be lost

# HBase Architecture
[Interview Q&A](https://blog.csdn.net/weixin_42298209/article/details/112096326)
![](/assets/img/sample/hbase-architecture.png)

- **Row Key**: Each row has a unique row key; the row key does not have a data type and is treated internally as a byte array. Row keys are uninterpreted bytes. Rows are lexicographically sorted with the lowest order appearing first in a table. The empty byte array is used to denote both the start and end of a tables' namespace.

Rows are sorted alphabetically by the row key as they are stored. For this reason, the design of the row key is very important. The goal is to store data in such a way that related rows are near each other. A common row key pattern is a website domain. If your row keys are domains, you should probably store them in reverse (org.apache.www, org.apache.mail, org.apache.jira). This way, all of the Apache domains are near each other in the table, rather than being spread out based on the first letter of the subdomain.
- **Column**:A column in HBase consists of a column family and a column qualifier, which are delimited by a : (colon) character.
- **Column Family**: Data inside a row is organized into column families; each row has the same set of column families,
but across rows, the same column families do not need the same column qualifiers. Column families must be declared up front at schema definition time whereas columns do not need to be defined at schema time but can be conjured on the fly while the table is up and running.
- **Column Qualifier**: A column qualifier is added to a column family to provide the index for a given piece of data. Given a column family content, a column qualifier might be content:html, and another might be content:pdf. Though column families are fixed at table creation, column qualifiers are mutable and may differ greatly between rows.
- **Cell**:A cell is a combination of row, column family, and column qualifier, and contains a value and a timestamp, which represents the value’s version.
- **Timestamp**: A timestamp is written alongside each value, and is the identifier for a given version of a value. By default, the timestamp represents the time on the RegionServer when the data was written, but you can specify a different timestamp value when you put data into the cell. Timestamps are stored in descending order.

If multiple writes to a cell have the same version, only the last written is fetchable.

It is OK to write cells in a non-increasing version order.
## Column Metadata
There is no store of column metadata outside of the internal KeyValue instances for a ColumnFamily. Thus, while HBase can support not only a wide number of columns per row, but a heterogeneous set of columns between rows as well, it is your responsibility to keep track of the column names.
## Joins
HBase doesn't support joins in the way that RDBMS' support them. However, that doesn’t mean that equivalent join functionality can’t be supported in your application, but you have to do it yourself.

![HBase Four-Dimensional Data Model](/assets/img/sample/hbase-data-model.png "HBase Four-Dimensional Data Model")

# Data Model Operations
## Get, Scan, Put, Delete
### Delete
Deletes work by creating tombstone markers. For example, let’s suppose we want to delete a row. For this you can specify a version, or else by default the currentTimeMillis is used. What this means is delete all cells where the version is less than or equal to this version. When HBase does a major compaction, the tombstones are processed to actually remove the dead values, together with the tombstones themselves.

# HBase and Schema Design
- [table_schema_rules_of_thumb](https://hbase.apache.org/book.html#table_schema_rules_of_thumb)
## RowKey Design
### Hotspotting
#### Techniques for avoiding hotspotting
- Salting
salting attempts to increase throughput on writes, but has a cost during reads.
```
a-foo0003   // add random prefix to rowkeys
b-foo0001
c-foo0004
d-foo0002
```
- Hashing
- Reversing the Key
A third common trick for preventing hotspotting is to reverse a fixed-width or numeric row key so that the part that changes the most often (the least significant digit) is first. This effectively randomizes row keys, but sacrifices row ordering properties.

## why the design of the row key is important?
- Table scans operate against the row key, so the design of the row key controls how much real-time/direct access you can perform against HBase.
- When running HBase in a production environment, it runs on top of the Hadoop Distributed File System (HDFS) and the data is distributed across the HDFS based on the row key.
If all your row keys start with “user-” then most likely the majority of your data will be isolated to a single node
(which defeats the purpose of distributing the data in the first place). Your row keys, therefore, should be different enough to be distributed across the entire deployment.

## how to design row keys
The manner in which you design your row keys depends on how you intend to access those rows. If you store data on a per user basis,
then one strategy is to leverage the fact that row keys are ultimately stored as byte arrays in HBase, so we can create a hash
(such as an MD5 or SHA-1 hash code) of the user ID and then append the time (as a long) to hash.
The importance in using a hash is two-fold: (1) it distributes values so that the data can be distributed across the cluster
and (2) it ensures that the length (in bytes) of the key is consistent and hence easier to use in table scans.

# two ways to access HBase data
- Through their row key or via a table scan for a range of row keys
- In a batch manner using map-reduce

# Basic Concepts Summary
[This post](https://www.shuzhiduo.com/A/kPzOW0v8zx/) introduces HBase data model, memory mechanism.


# snapshot
[snapshot](http://hbasefly.com/2017/09/17/hbase-snapshot/)


# Reference
[Introduction to HBase, the NoSQL Database for Hadoop](https://www.informit.com/articles/article.aspx?p=2253412)

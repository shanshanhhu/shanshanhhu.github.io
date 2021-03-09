---
title: HBase Concepts and Design
date: 2020-12-23 10:31:22 +0800
categories: [bigdata, hbase]
tags: [hbase]
---

# What is HBase?
[Official Documents](http://hbase.apache.org/book.html#number.of.cfs)
Apache HBase is a **NoSQL database** that runs on top of Hadoop as a distributed and scalable big data store.

# HBase data model
- **Row Key**: Each row has a unique row key; the row key does not have a data type and is treated internally as a byte array.
- **Column Family**: Data inside a row is organized into column families; each row has the same set of column families,
but across rows, the same column families do not need the same column qualifiers. Under-the-hood, HBase stores
column families in their own data files, so they need to be defined upfront, and changes to column families are difficult to make.
- **Column Qualifier**: Column families define actual columns, which are called column qualifiers. You can think of column qualifiers as the columns themselves.
- **Version**: Each column can have a configurable number of versions, and you can access the data for a specific version of a column qualifier.
![HBase Four-Dimensional Data Model](/assets/img/sample/hbase-data-model.png "HBase Four-Dimensional Data Model")

# RowKey design
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

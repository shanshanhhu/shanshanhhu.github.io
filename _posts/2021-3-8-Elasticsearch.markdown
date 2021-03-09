---
title: Elasticsearch
date: 2021-03-08 13:10:22 +0800
categories: [bigdata, elasticsearch]
tags: [elasticsearch]
---

# What is Elasticsearch?
Elasticsearch is a distributed document store, a NoSQL database which stores a data in a JSON document form. Instead of storing information as rows of columnar data, Elasticsearch stores complex data structures that have been serialized as JSON documents.

## documents and indices
An index can be thought of as an optimized collection of documents and each document is a collection of fields, which are the key-value pairs that contain your data. Data in the index is spread through the **shards**, which are searched independently.
-  inverted index
An inverted index lists every unique word that appears in any document and identifies all of the documents each word occurs in.By default, Elasticsearch indexes all data in every field and each indexed field has a dedicated, optimized data structure. For example, text fields are stored in inverted indices, and numeric and geo fields are stored in BKD trees.
![](../assets/img/sample/inverse-index.webp)
- schema-less
Elasticsearch also has the ability to be schema-less, which means that documents can be indexed without explicitly specifying how to handle each of the different fields that might occur in a document.

[Guide to Elasticsearch in Java](https://www.baeldung.com/elasticsearch-java)
[ElasticSearch as Flink Sinker](https://www.flinkinpractice.com/2019/10/29/ElasticSearch-as-Sinker/)
[Search APIs](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/master/java-rest-high-search.html)
[ES Demo](https://www.javacodegeeks.com/2018/03/elasticsearch-tutorial-beginners.html)

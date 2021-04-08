---
title: Data Warehouse
date: 2021-3-19 14:00:22 +0800
categories: [bigdata, datawarehouse]
tags: [datawarehouse]
---
# A general data layered design
![](../assets/img/sample/data-warehouse-layer.jpeg)
[](https://cloud.tencent.com/developer/article/1396891)
- the advantages of data layered design
![](../assets/img/sample/benefits-of-dw.jpg)
1. 数据结构清晰：每一个数据分层都有它的作用域和职责，在使用表的时候能更方便地定位和理解
2. 减少重复开发：规范数据分层，开发一些通用的中间层数据，能够减少极大的重复计算
3. 统一数据口径：通过数据分层，提供统一的数据出口，统一对外输出的数据口径
4. 复杂问题简单化：将一个复杂的任务分解成多个步骤来完成，每一层解决特定的问题
#  OLTP & OLAP
- Online Transaction Processing
- Online Analytical Processing


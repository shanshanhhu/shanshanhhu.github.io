---
title: HBase Java API
date: 2020-12-17 14:41:22 +0800
categories: [bigdata, hbase]
tags: [hbase]     # TAG names should always be lowercase
---

## Reading Data
### Scan
1. 方法类合集[Scan](http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html)
2. 常用方法
    - addColumn(byte[] family, byte[] qualifier)
    - addFamily(byte[] family)
        Limiting the scan to specific column families or columns by using addFamily or addColumn can reduce
        IO and network. IO is reduced because each column family is represented by a Store on each RegionServer,
        and only the Stores representing the specific column families in question need to be accessed.
    - withStartRow(byte[] startRow)
        Set the start row of the scan. If the specified row does not exist, the Scanner will start from
        the next closest row after the specified row.
    - withStopRow(byte[] stopRow)
    - setTimeRange(long minStamp, long maxStamp)
    - setTimeStamp(long timestamp)
    - setCaching(int caching)
        Specify a scanner cache that will be filled before the Scan result is returned, setting setCaching to the number
        of rows to cache before returning the result. If not set, the Configuration setting HConstants.HBASE_CLIENT_SCANNER_CACHING
        will apply. Higher caching values will enable faster scanners but will use more memory.
    - setBatch(int batch)
        To limit the number of columns if your table has very wide rows (rows with a large number of columns),
        use setBatch(int batch) and set it to the number of columns you want to return in one batch.
    - setMaxResultSize(long maxResultSize)

    - setFilter(Filter filter)
    [Subclasses](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/filter/class-use/Filter.html) of Filter in org.apache.hadoop.hbase.filter
        - SingleColumnValueFilter(byte[] family, byte[] qualifier, CompareOperator op, byte[] value)
        CompareOperator: EQUAL, GREATER, GREATER_OR_EQUAL, LESS, LESS_OR_EQUAL, NO_OP, NOT_EQUAL
        - RowFilter(CompareOperator op, ByteArrayComparable rowComparator)
        - ValueFilter(CompareOperator valueCompareOp, ByteArrayComparable valueComparator)
        This filter is used to filter based on column value
        -
    - setRowPrefixFilter(byte[] rowPrefix)
        NOTE: Doing a withStartRow(byte[]) and/or withStopRow(byte[]) after this method will yield undefined results.






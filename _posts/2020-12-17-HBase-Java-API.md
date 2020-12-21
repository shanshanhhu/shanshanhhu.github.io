---
title: HBase Java API
date: 2020-12-17 14:41:22 +0800
categories: [bigdata, hbase]
tags: [hbase]     # TAG names should always be lowercase
---

# Reading Data
## Scan
1. **方法类合集[Scan](http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html)**
2. **常用方法**
    - **addColumn(byte[] family, byte[] qualifier)**
    - **addFamily(byte[] family)**
    Limiting the scan to specific column families or columns by using addFamily or addColumn can reduce
    IO and network. IO is reduced because each column family is represented by a Store on each RegionServer,
    and only the Stores representing the specific column families in question need to be accessed.
    - **withStartRow(byte[] startRow)**
    Set the start row of the scan. If the specified row does not exist, the Scanner will start from
    the next closest row after the specified row.
    - **withStopRow(byte[] stopRow)**
    - **setTimeRange(long minStamp, long maxStamp)**
    - **setTimeStamp(long timestamp)**
    - **setCaching(int caching)**
    Specify a scanner cache that will be filled before the Scan result is returned, setting setCaching to the number
    of rows to cache before returning the result. If not set, the Configuration setting HConstants.HBASE_CLIENT_SCANNER_CACHING
    will apply. Higher caching values will enable faster scanners but will use more memory.
    - **setBatch(int batch)**
    To limit the number of columns if your table has very wide rows (rows with a large number of columns),
    use setBatch(int batch) and set it to the number of columns you want to return in one batch.
    - **setMaxResultSize(long maxResultSize)**

    - **setFilter(Filter filter)**
    [Subclasses](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/filter/class-use/Filter.html) of Filter in org.apache.hadoop.hbase.filter
        - **SingleColumnValueFilter(byte[] family, byte[] qualifier, CompareOperator op, byte[] value)**
        CompareOperator: EQUAL, GREATER, GREATER_OR_EQUAL, LESS, LESS_OR_EQUAL, NO_OP, NOT_EQUAL
        - **RowFilter(CompareOperator op, ByteArrayComparable rowComparator)**
        - **ValueFilter(CompareOperator valueCompareOp, ByteArrayComparable valueComparator)**
        This filter is used to filter based on column value
    - **setRowPrefixFilter(byte[] rowPrefix)**
    **NOTE:** Doing a withStartRow(byte[]) and/or withStopRow(byte[]) after this method will yield undefined results.

3.**代码样例**
   - **样例一**
```java
    public class demo1(){
        public CarTracks getAccidentCarTracks(String id, Long endTime) throws IOException {
            CarTracks carTracks = new CarTracks();
            List<CarTrackView> carTrackViews = new ArrayList<>();
            TableName name = TableName.valueOf(props.getBackTrackTable());
            Long timePeriod = 150L;
            Long startTime = endTime - timePeriod * 1000L;
            try (Table table = HconnectionFactory.connection.getTable(name)) {
                Scan scan = new Scan();

                FilterList filterList = new FilterList(FilterList.Operator.MUST_PASS_ALL);

                scan.setRowPrefixFilter(Bytes.toBytes(id));

               //根据时间筛选车辆轨迹
                Filter startTimeFilter = new SingleColumnValueFilter(Bytes.toBytes("cartrack"), Bytes.toBytes("timeStamp_"), CompareOperator.GREATER_OR_EQUAL, Bytes.toBytes(startTime+""));
                filterList.addFilter(startTimeFilter);
                Filter endTimeFilter = new SingleColumnValueFilter(Bytes.toBytes("cartrack"), Bytes.toBytes("timeStamp_"), CompareOperator.LESS_OR_EQUAL, Bytes.toBytes(endTime+""));
                filterList.addFilter(endTimeFilter);

                scan.setFilter(filterList);

                ResultScanner scanner = table.getScanner(scan);
                for (Result rs : scanner) {
                    CarTrackView carTrackView = new CarTrackView();
                    carTrackView.setId(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("ptcId"))));
                    carTrackView.setLatitude(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("lat"))));
                    carTrackView.setLongitude(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("long_"))));
                    carTrackView.setAltitude(new Integer(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("elevation")))));
                    carTrackView.setCreateTime(new Long(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("timeStamp_")))));
                    carTrackView.setBearing(new Integer(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("heading")))));
                    carTrackView.setSpeed(new Integer(new String(rs.getValue(Bytes.toBytes("cartrack"), Bytes.toBytes("speed")))));
                    carTrackViews.add(carTrackView);
                }
                //按照时间由小到大进行排序
                Collections.sort(carTrackViews, new Comparator<CarTrackView>() {
                    @Override
                    public int compare(CarTrackView o1, CarTrackView o2) {
                        return o1.getCreateTime().compareTo(o2.getCreateTime());
                    }
                });

                carTracks.setCarTrackViews(carTrackViews);
                scanner.close();
            } catch (Exception e) {
                logger.error("query HBase error", e);
            }
            return carTracks;
        }
    }
```
   - **样例二**
```java
    public class demo2() {
        public void delCarTracksData() {
            TableName name = TableName.valueOf(props.getCarTrackTable());
            try (Table table = HconnectionFactory.connection.getTable(name)) {
                Scan scan = new Scan();

                Long STOP_TS = (System.currentTimeMillis() / 1000) - 3600;
                scan.setTimeRange(0, STOP_TS);
                scan.setCaching(1000);

                FilterList filters = new FilterList(FilterList.Operator.MUST_PASS_ALL,
                        new FirstKeyOnlyFilter(), new KeyOnlyFilter());   //只获取RowKeys
                scan.setFilter(filters);
                ResultScanner scanner = table.getScanner(scan);
                List<Delete> deletes = new ArrayList<>(1000);
                Result [] rr;
                do {
                    rr = scanner.next(1000);
                    if (rr.length > 0) {
                        for (Result r: rr) {
                            Delete delete = new Delete(r.getRow(), STOP_TS);
                            deletes.add(delete);
                        }
                        table.delete(deletes);
                        deletes.clear();
                    }
                } while(rr.length > 0);
            } catch (Exception e) {
                logger.error("删除车辆轨迹数据失败！");
            }
        }
    }
```
##Get
1.**方法合集**[Get](http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html)

2.**代码样例**
```java
public class RetriveData{

   public static void main(String[] args) throws IOException, Exception{

      // Instantiating Configuration class
      Configuration config = HBaseConfiguration.create();

      // Instantiating HTable class
      HTable table = new HTable(config, "emp");

      // Instantiating Get class
      Get g = new Get(Bytes.toBytes("row1"));

      // Reading the data
      Result result = table.get(g);

      // Reading values from Result class object
      byte [] value = result.getValue(Bytes.toBytes("personal"),Bytes.toBytes("name"));
      byte [] value1 = result.getValue(Bytes.toBytes("personal"),Bytes.toBytes("city"));

      // Printing the values
      String name = Bytes.toString(value);
      String city = Bytes.toString(value1);

      System.out.println("name: " + name + " city: " + city);
   }
}
```








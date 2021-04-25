---
title: Flink DataSources And SinkFunctions
date: 2021-03-05 10:55:22 +0800
categories: [bigdata, flink]
tags: [flink]
---

# 继承关系
![](/assets/img/sample/flink-source.png)

## RichSourceFunction(可并行)
![](/assets/img/sample/flink_rich_source_function.png)

# Flink Run Parameters

1. 参数必选 ：
     -n,--container <arg>   分配多少个yarn容器 (=taskmanager的数量)
2. 参数可选 ：
     -D <arg>                        动态属性
     -d,--detached                   独立运行
     -jm,--jobManagerMemory <arg>    JobManager的内存 \[in MB]
     -nm,--name                      在YARN上为一个自定义的应用设置一个名字
     -q,--query                      显示yarn中可用的资源 (内存, cpu核数)
     -qu,--queue <arg>               指定YARN队列.
     -s,--slots <arg>                每个TaskManager使用的slots数量
     -tm,--taskManagerMemory <arg>   每个TaskManager的内存 \[in MB]
     -z,--zookeeperNamespace <arg>   针对HA模式在zookeeper上创建NameSpace
     -id,--applicationId <yarnAppId> YARN集群上的任务id，附着到一个后台运行的yarn session中

3. run \[OPTIONS] <jar-file> <arguments>

    run操作参数:
    -c,--class <classname>  如果没有在jar包中指定入口类，则需要在这里通过这个参数指定
    -m,--jobmanager <host:port>  指定需要连接的jobmanager(主节点)地址，使用这个参数可以指定一个不同于配置文件中的jobmanager
    -p,--parallelism <parallelism>   指定程序的并行度。可以覆盖配置文件中的默认值。

4. 启动一个新的yarn-session,它们都有一个y或者yarn的前缀

    例如：./bin/flink run -m yarn-cluster -yn 2 ./examples/batch/WordCount.jar

    连接指定host和port的jobmanager：
    ./bin/flink run -m SparkMaster:1234 ./examples/batch/WordCount.jar -input hdfs://hostname:port/hello.txt -output hdfs://hostname:port/result1

    启动一个新的yarn-session：
    ./bin/flink run -m yarn-cluster -yn 2 ./examples/batch/WordCount.jar -input hdfs://hostname:port/hello.txt -output hdfs://hostname:port/result1

5. 注意：命令行的选项也可以使用./bin/flink 工具获得。
![flink-run-parameters](/assets/img/sample/flink-run-parameters.png)

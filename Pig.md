## About Pig

* 基于Hadoop的大规模数据分析平台
* 提供类SQL语言：Pig Latin，该语言的编译器会把类SQL的数据分析请求转换为一系列经过优化处理的MapReduce运算。（**提供了更高层次的抽象**）
* 为复杂的海量数据并行计算提供了一个简单的操作和编程接口。
* 包括两部分：
  - 用于描述数据流的语言，称为Pig Latin。
  - 用于执行Pig Latin程序的执行环境，当前有两个环境：单JVM中的本地执行环境和Hadoop集群上的分布式执行环境。

## Pig VS Hive

* HIVE介于Pig和传统的RDBMS之间，和Pig一样，Hive也被设计为HDFS作为存储，但是他们之间有着显著的区别。Hive的查询语言HiveQL，是基于SQL的。和RDBMS相同。Hive要求所有数据必须存储在表中，表必须有模式，而模式由Hive进行管理。
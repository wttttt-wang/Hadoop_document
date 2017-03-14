## 计数器与自定义计数器

- What for？

  - 计数器是用来记录job的执行进度和状态。
  - 其作业可理解为日志，但比日志更为便利
  - 通常可以在程序的某个位置插入计数器，用以记录数据或者进度的变化情况

- eg：每次运行完MR之后，窗口最后会打印类似的信息

  ```java
  17/03/13 21:30:21 INFO mapred.JobClient:	Counters: 19 // Counter表示计数器，19表示有19个计数器（下面一共4计数器组）
  17/03/13 21:30:21 INFO mapred.JobClient:	   File Output Format Counters // 文件输出格式化计数器组
  17/03/13 21:30:21 INFO mapred.JobClient:	     Bytes Written=19 // reduce输出到hdfs的字节数，一共19个字节
  17/03/13 21:30:21 INFO mapred.JobClient:	   FileSystemCounters// 文件系统计数器组
  17/03/13 21:30:21 INFO mapred.JobClient:	     FILE_BYTES_READ=481
  17/03/13 21:30:21 INFO mapred.JobClient:	     HDFS_BYTES_READ=38
  17/03/13 21:30:21 INFO mapred.JobClient:	     FILE_BYTES_WRITTEN=81316
  17/03/13 21:30:21 INFO mapred.JobClient:	     HDFS_BYTES_WRITTEN=19
  17/03/13 21:30:21 INFO mapred.JobClient:	   File Input Format Counters // 文件输入格式化计数器组
  17/03/13 21:30:21 INFO mapred.JobClient:	     Bytes Read=19 // map从hdfs读取的字节数
  17/03/13 21:30:21 INFO mapred.JobClient:	   Map-Reduce Framework // MapReduce框架
  17/03/13 21:30:21 INFO mapred.JobClient:	     Map output materialized bytes=49
  17/03/13 21:30:21 INFO mapred.JobClient:	     Map input records=2 // map读入的记录行数，读取两行记录,”hello you”,”hello me”
  17/03/13 21:30:21 INFO mapred.JobClient:	     Reduce shuffle bytes=0 // 规约分区的字节数
  17/03/13 21:30:21 INFO mapred.JobClient:	     Spilled Records=8
  17/03/13 21:30:21 INFO mapred.JobClient:	     Map output bytes=35
  17/03/13 21:30:21 INFO mapred.JobClient:	     Total committed heap usage (bytes)=266469376
  17/03/13 21:30:21 INFO mapred.JobClient:	     SPLIT_RAW_BYTES=105
  17/03/13 21:30:21 INFO mapred.JobClient:	     Combine input records=0 // 合并输入的记录数
  17/03/13 21:30:21 INFO mapred.JobClient:	     Reduce input records=4 // reduce从map端接收的记录行数
  17/03/13 21:30:21 INFO mapred.JobClient:	     Reduce input groups=3  // reduce函数接收的key数量，即归并后的k2数量
  17/03/13 21:30:21 INFO mapred.JobClient:	     Combine output records=0 // 合并输出的记录数
  17/03/13 21:30:21 INFO mapred.JobClient:	     Reduce output records=3 // reduce输出的记录行数。<helllo,{1,1}>,<you,{1}>,<me,{1}>
  17/03/13 21:30:21 INFO mapred.JobClient:	     Map output records=4 // map输出的记录行数，输出4行记录
  ```

  如上所示，计数器有19个，分为四个组：File Output Format Counters、FileSystemCounter、File Input Format Counters和Map-Reduce Framkework。

  分组File Output Format Counters包括一个计数器Bytes Written，表示job执行结束后输出文件的内容包括19个字节。

- 用户自定义计数器

  - 以上是Hadoop中系统内置的标准计数器，除此之外我们可以自定义使用计数器。

  - eg:我们试着在mapper中加了一个counter，具体代码见FYI2。运行之后，counter多了一个组叫*Sensitive words*，其中有一项*never*。如下：

    ```
    	Sensitive Words:
    		Hello=2
    	File Input Format Counters 
    		Bytes Read=85
    	File Output Format Counters 
    		Bytes Written=93
    	...
    ```

    ​

## FYI

1. [计数器与自定义计数器](http://www.cnblogs.com/edisonchou/p/4297599.html)
2. [Counter代码](https://github.com/wttttt-wang/hadoop_inaction/blob/master/CounterDemo.java)
# MapReduce Tutorial

## overview

*  TODO



## Inputs and Outputs

* The MR framework operates exclusively on **<key, value>** pairs.  MR基于键值对操作。
* The key and value classes have to be **serializable** by the framework, by implementing the **Writable** interface. 键值对需要实现Writable接口以实现序列化。
* The key classes have to implement the **WritableComparable** interface to facilitate sorting by the framework. 键需要实现WritableComparable接口来排序。
* (input) `<k1, v1> ->` **map** `-> <k2, v2> ->` **combine** `-> <k2, v2> ->` **reduce** `-> <k3, v3>` (output).



## MapReduce — User Interfaces

* This section we see details on every user-facing aspect of MR framework. 
* Note that the **javadoc** for each class/interface remains the most comprehensive documentation available. 学会利用javadoc。
* 接下来我们介绍几个常用接口：
  1. Mapper：
     * Mapper maps input key/value pairs to a set of intermediate key/value. mapper将键值对映射成中间键值对。
     * Maps are the individual tasks. One map task for each Inputsplit.每个输入分片对应一个map任务。
     * Mapper implementations are passed the Job for the job via Job.setMapperClass(Class) method. The framework then calls map() function for each k/v pair in the inputsplit. 通过setMapperClass函数将mapper类传给job，然后框架会调用map()函数。
     * Applications can override the *cleanup(Context)* method to perform any required **cleanup()**. cleanup()方法会在每个map任务的最后执行一次，即处理完该map的所有inputsplit记录后。
     * All intermediate valyes associated with a given output key are subsequently grouped by the framework, and passed to the Reducers. 相同key的value会汇聚到一起传给reducer。
     * Users can control the grouping by specifying a **Comparator** via *Job.setGroupingComparatorClass(Class)*. 用户可以通过Comparator控制哪些keys分到一起。
     * The mapper outputs are sorted and then partioned per Reducer. The total number of partitions is the same as the number of reduce tasks for the job. Users can control which keys go to which Reducer by implementing a custom **Partitioner**. Mapper的输出会排序然后划分到Reducer中，分区数取决于reduce任务的数。用户可以通过Partitioner来实现自定义的划分。
     * How many Maps? —> The number of maps is usually driven by the total size of the inputs, that is, the total number of blocks of the input files. map的个数取决于分块数。
  2. Reducer：
     * Reducer reduces a set of intermediate values which share a key to a smaller set of values.
     * The number of reduces for the job is set by the user via Job.setNumReduceTask(int). 用户可以通过Job.setNumReduceTask(int)来设置reduce个数。
     * Same  as mapper, reducer calls reduce() and cleanup().
     * Reducer has 3 primary phases: shuffle, sort and reduce. reducer有三个阶段:
       * ​**Shuffle**: Input to the Reducer is the sorted output of the mappers. In this phase the framework fetches the relevant partition of the output of all the mappers, via HTTP. Reducer的输入是mapper的排序输出。在此阶段，框架提高HTTP从所有的mapper中获取相应的分区。
       * **Sort**: The framework groups Reducer inputs by keys in this stage. The shuffle and sort phases occur simultaneously; while map-outputs are being fetched they are merged. 在此阶段，框架将输入按keys分组。shuffle和sort阶段同时进行。
       * **Secondary Sort**: Users can use **comparator** to control how intermediate keys are grouped, these can be used in conjunction to simulate secondary sort on values. 可以通过comparator来实现value上的二次排序。
       * **Reduce**: The reduce() method is called for each `<key, (list of values)>` pair in the grouped inputs. The output of the reduce task is typically written to the FileSystem via Context.write(). 该阶段会调用reduce()函数，输出会写到文件系统。
       * How many Reduces? —> The right number of reduces seems to be `0.95` or `1.75` multiplied by (<*no. of nodes*> * <*no. of maximum containers per node*>).
       * Reducer NONE: It is legal. In this case, the framework does not sort the map-outputs before writing them to the FileSystem. 可以设置reduce个数为0，此时map的结果直接写入文件系统，不经过排序。
  3. Partitioner：
     * Partitioner partitions the key space. 划分key空间。
     * Controls which of the `m` reduce tasks the intermediate key (and hence the record) is sent to for reduction. 控制map输出的记录送往哪个reducer。
     * [HashPartitioner](http://hadoop.apache.org/docs/r2.6.5/api/org/apache/hadoop/mapreduce/lib/partition/HashPartitioner.html) is the default `Partitioner`. 默认使用HashPartitioner。
  4. Counter:
     * [Counter](http://hadoop.apache.org/docs/r2.6.5/api/org/apache/hadoop/mapreduce/Counter.html) is a facility for MapReduce applications to report its statistics. Counter用以报告应用的统计信息。
* **Job Configuration**
  * [Job](http://hadoop.apache.org/docs/r2.6.5/api/org/apache/hadoop/mapreduce/Job.html)  is the primary interface for a user to describe a MapReduce job to the Hadoop framework for execution.  Job是描述MR job的主要接口。
  * What can Job do?
    * Specify the Mapper, combiner, Partitioner, Reducer, InputFOrmat, OutputFormat,.
    * Specify Comparator to be used
    * Specify files to be put in the **DistributedCache**
    * Specify whether intermediate or job outputs are to be compressed
* Task Execution & Environment
  * To be continued...


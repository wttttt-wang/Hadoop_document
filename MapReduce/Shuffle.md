## About Shuffle

* Hadoop的shuffle过程就是从map端输出到reduce端输入之间的过程。
* Hadoop的核心部分，涉及到最珍贵的网络资源。



## Shuffle过程

* Map端：

  InputSplit —> Mapper —> < k2, v2 > —>存放在**缓存**中(每个map有一个环形内存缓冲区，默认100MB，阈值0.8) —> **partition、sort、combine**… —> **spill**溢写(一个后台进程将内容写到本地磁盘中的指定目录*mapred.local.dir*下的新建的一个溢写文件)

  * map过程的输出是**本地磁盘**而非HDFS。
  * 数据并非直接写入磁盘而是**缓存在内存**中 —> 好处是**减少磁盘I/O**的开销，提高合并和排序的速度。
  * 写磁盘前，要进行partition、sort和combine等。通过分区将不同类型的数据分开处理，之后对不同分区的数据进行排序。如果有combiner，还要对排序后的数据进行combine。
  * 等最后记录写完，将全部溢写文件合并为一个分区且排序的文件。溢写是由单独线程来完成，不影响往缓冲区写map结果的线程。
  * 最后将磁盘中的数据送到Reducer中。
  * 在写磁盘的时候采用压缩的方式将map的输出结果进行**压缩**是一个减少网络开销很有效的方法。

* Reduce端：

  **copy** —> **merge** 

  * copy阶段：reducer通过Http方式得到输出文件的分区，默认有5个数据复制线程。
  * merge阶段：从map端复制过来的文件首先会写到reduce端的缓存中，同样缓存占用达到一定阈值后会将数据写到磁盘中，同样会进行partition、combine、排序等过程。如果形成了多个磁盘文件还会进行合并。合并的结果作为reducer的输入。
    * Shuffle阶段Reducer不执行 —> 因此绝大部分的内存都给Shuffle
    * merge有三种形式：
      * 内存到内存：默认不启用
      * 内存到磁盘：类似spill，此时如果有设置combiner，也是会启用的
      * 磁盘到磁盘：生成“最终文件”，这个文件默认存放于磁盘中，但也可以存于内存中以优化
  * 在reducer的输入文件确定后，整个shuffle操作才最终结束。之后就是reducer的执行了。



## FYI

* [Reduce阶段的shuffle过程](http://www.cnblogs.com/edisonchou/p/4298423.html)
* [MapReduce:详解Shuffle过程](http://langyu.iteye.com/blog/992916)


























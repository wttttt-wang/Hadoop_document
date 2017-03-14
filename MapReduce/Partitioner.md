## About Partitioner

* partitioner规定record到reducer如何分配
* 在一些集群应用中，例如分布式缓存集群中，缓存的数据大多是靠**哈希函数**进行数据的**均匀分配**，在Hadoop中也不例外。

## Hadoop内置Partitioner

* 默认的分区函数使用hash方法（比如常见的hash(key) mod R）进行分区。

* hash方法能够产生非常平衡的分区。鉴于此，Hadoop中自带了一个默认的分区类**HashPartitioner**，它继承了Partitioner类，提供了一个getPartition方法。

  ```java
  /** Partition keys by their {@link Object#hashCode()}. */
  public class HashPartitioner<K, V> extends Partitioner<K, V> {
    /** Use {@link Object#hashCode()} to partition. */
    public int getPartition(K key, V value,
                            int numReduceTasks) {
      return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
    }
  }
  ```

* 关键代码：*(key.hashCode() & Integer.MAX_VALUE) % numReduceTasks*，其目的是将key均匀分布在reduce tasks上。【注：关于hash函数，往下拉。】

## 自定义Partitioner

* 实例demo程序请参考FYI3

* 自定分区的作用：

  1. 根据业务需要，产生多个输出文件；
  2. 多个reduce任务在并发进行，提高整体job的运行效率。

* 关于Partitioner的一个神奇的用法：**Hadoop For Global Sort**

  (Partition)分区出现的必要性，如何使用Hadoop产生一个全局排序的文件？最简单的方法就是使用一个分区，但是该方法在处理大型文件时效率极低，因为一台机器必须处理所有输出文件，从而完全丧失了MapReduce所提供的并行架构的优势。

  事实上我们可以这样做，首先创建一系列排好序的文件；其次，串联这些文件（类似于归并排序）；最后得到一个全局有序的文件。主要的思路是使用一个partitioner来描述全局排序的输出。比方说我们有1000个1-10000的数据，跑10个ruduce任务， 如果我们运行进行partition的时候，能够将在1-1000中数据的分配到第一个reduce中，1001-2000的数据分配到第二个reduce中，以此类推。即第n个reduce所分配到的数据全部大于第n-1个reduce中的数据。这样，每个reduce出来之后都是有序的了，我们只要cat所有的输出文件，变成一个大的文件，就都是有序的了。

  基本思路就是这样，但是现在有一个问题，就是数据的区间如何划分，在数据量大，还有我们并不清楚数据分布的情况下。一个比较简单的方法就是采样，假如有一亿的数据，我们可以对数据进行采样，如取10000个数据采样，然后对采样数据分区间。在Hadoop中，patition我们可以用TotalOrderPartitioner替换默认的分区。然后将采样的结果传给他，就可以实现我们想要的分区。在采样时，我们可以使用hadoop的几种采样工具，RandomSampler,InputSampler,IntervalSampler。        

  这样，我们就可以对利用分布式文件系统进行大数据量的排序了，我们也可以重写Partitioner类中的compare函数，来定义比较的规则，从而可以实现字符串或其他非数字类型的排序，也可以实现二次排序乃至多次排序。





## About hash函数：

- 一个好的hash函数一般有以下两个特点：
  1. 速度快；
  2. 能够将散列键均匀分布在整个表中，且保证不会产生聚集。
- hash函数通常形式：hash-key = calculated-key % tablesize。为了提高散列键的离散程度，tablesize通常取素数。


- 当key是字符串时：
  - 一种选择策略是简单地将字符串中的每个字符的ASCII码相加。该hash函数实现简单而且能够很快的算出答案，不过，如果表很大，则函数就不能很好的分配键，例如，设tableSize=10949，并设所有的键至多8个字符长，由于ASCII字符的值最多是127，因此hash函数只能在0~1016(1016=127*8)之间取值，显然这不是一种均匀的分配。
  - 更多的改进，see FYI2



## FYI

1. [Partitioner与自定义Partitioner](http://www.cnblogs.com/edisonchou/p/4297828.html)
2. [hash函数](http://blog.csdn.net/qll125596718/article/details/7005369)
3. [PartitionerDemo.java](https://github.com/wttttt-wang/hadoop_inaction/blob/master/PartitionerDemo.java)
4. [DIYWritable.java](https://github.com/wttttt-wang/hadoop_inaction/blob/master/DIYWritable.java)
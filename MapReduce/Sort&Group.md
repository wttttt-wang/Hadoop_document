## 默认的排序：

* 在Hadoop默认的排序算法中，只会针对key进行排序

## 自定义排序

* eg：封装一个自定义类型作为key的新类型，来实现类似二次排序
  * 代码参见FYI1



## 默认分组

* 分组是在Mapper端的第四步，分组也是基于key进行的，将相同key的value放到一个集合中。

## 自定义分组

* 代码见FYI2



## FYI

1. [SortDemo.java](https://github.com/wttttt-wang/hadoop_inaction/blob/master/SortDemo.java)ß
2. [GroupDemo.java](https://github.com/wttttt-wang/hadoop_inaction/blob/master/GroupDemo.java)
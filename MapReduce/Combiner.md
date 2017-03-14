## 性能瓶颈

* **网络带宽**严重被占用降低程序效率：大量键值对在网络中传输
* 单一结点承载过重降低程序性能：**数据倾斜**，大多数键值对会聚集于一个单一的Reducer上。

## About Combiner

* 作用：对map端的输出先做一次合并，以减少map和reduce节点之间的数据传输量，提高网络IO性能。MR的优化手段之一。

* 过程：

  ```
  map: (K1, V1) → list(K2, V2) 
  combine: (K2, list(V2)) → list(K2, V2) 
  reduce: (K2, list(V2)) → list(K3, V3)
  ```

  * combiner ==> 迷你／本地reduce，只处理单台机器生成的数据

## FYI

* [Combiner与自定义combiner](http://www.cnblogs.com/edisonchou/p/4297786.html)
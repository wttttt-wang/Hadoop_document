## About RPC

* Remote Procdure Call远程过程调用
* 允许一台计算机程序远程调用另一台计算机的子程序，而不用关心底层的网络通信细节，对操作者是透明的
* 常用于分布式网络通信中
* 特点：
  * 透明性：像调用本地方法一样
  * 高性能：RPC Server可以并发处理多个来自Client的请求
  * 可控性：
* RPC采用C/S模式：CLient端发送一个带有参数的请求信息导Server，Server调用相应的程序，之后将计算好的结果发给Client。

## Hadoop与RPC

* Hadoop的进程间交互都是通过RPC来进行的。比如Namenode和DataNode，JobTrakcer和TaskTracker。
* Hadoop中的RPC机制，分为四个部分：
  * 序列化层：Client与Server间通信传递的信息采用Hadoop内提供的序列化类或自定义的Writable类型；
  * 函数调用层：Hadoop RPC通过**动态代理**以及**java反射**实现函数调用；
  * 网络传输层：Hadoop RPC采用基于TCP/IP的socket机制；
  * 服务器端框架层：RPC Server利用java NIO(非阻塞的异步I/O)以及采用了事件驱动的I/O模型，提高RPC Server的并发处理能力。
* 如何使用RPC
  * Hadoop RPC对外提供的接口：
    * 包 org.apache.hadoop.ipc.RPC



## FYI

* More about RPC in Hadoop, see [Hadoop RPC机制](http://www.cnblogs.com/edisonchou/p/4285817.html)
* Java动态代理机制，see [java动态代理](http://www.ibm.com/developerworks/cn/java/j-lo-proxy1/)
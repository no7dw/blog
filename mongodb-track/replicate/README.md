## 读写分离后的并发问题 ##

因为服务器最近压力稍大，想做读写分离，以减少主服务器的压力，将读转移到replicate sets 中。
但代码执行时：
  

 1. create order record
 2. find order
 
第二步 找不到。报错。

原因。读写分离后，写 master 后，同步到replicate 需要时间。
这时候访问（读）replicate ，会有数据不一致的问题。

<2.6 版本的mongodb ，可以通过getLasterError的mongodb command ， j 选项，来确保数据写道磁盘中。
对于2.6 + 以上mongodb;
mongodb 有write-concert 选项。我们用的sailsjs 框架，默认也是｛w:1｝。带write concert 。 但值得注意的时，write-concert 时确保数据写道磁盘中。
但对一个 复制集的 部署架构，要确保数据也同时同步到复制集中。{w:number},number 要是复制集的数目。
1 只代表写到 standalone的 hosts ，活着 复制集架构的的master。

注意：
如果对数据一致性要求不高，可以使用读写分离。
如果对数据一致性要求高，要慎重开启读写分离，或者配置写操作的option {w:$number}。

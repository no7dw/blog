项目上线后，用户越来越多，有一天出现一个issue：用户访问特别慢。

 - 首先介绍下架构：

        haproxy/Nginx
            /     \
        node1     node2
          ｜        ｜
        redis     redis(slave)
          |         |
      mongodb   mongodb(replecate) 

 - 统计

查系统状态是什么情况,首先确定外部总体现象
![UV line][1]
压力是有些
![realtime of GA ][2]
但应该不至于很慢

 - check 网络

看下发末尾的流量，也不是特高。（忽略接近末尾的峰值）
![network][3]

 - log

然后追查node 节点log对应时间点(8:00PM)的log

2015-08-04 19:35:50 +08:00: Mozilla/5.0 (Linux; U; Android 4.4.2; zh-CN; SM-G9006V Build/KOT49H) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 UCBrowser/10.5.1.597 U3/0.8.0 Mobile Safari/534.30" "referer:http://wap.10086.cn/index.html" "112.85.32.217 GET /wx/caiyun HTTP/1.1 200" response size : -1 need time 1053ms.

获取个页面不需要数据库都要秒级别。有些js 要几秒！


 - 检查htop (内存偏低)
 - 检查pm2
 

![pm2 cpu & mem][4]

![take a close watch at pm2][5]
**CPU 奇怪的高**
逐个时间点与GA同时看

> $ grep 'GET /wx/caiyun'  /var/log/finance/applogs/koala-9031-out.log | grep "20:[0-9][0-9]:" | wc -l
3794
$ grep 'GET /wx/caiyun'  /var/log/finance/applogs/koala-9031-out.log.1 | grep "20:[0-9][0-9]:" | wc -l
3045

无特别大的波动。

 - 检查异常原因(入侵？)： less /var/log/syslogs 无果
 - 数据库mms 显示正常。查询不慢
 
后续分析了错误的log

>node1
2015-08-04 21:10:53 +08:00: FATAL ERROR: JS Allocation failed - process out of memory
...
node2
...
2015-08-04 23:00:50 +08:00: FATAL ERROR: CALL_AND_RETRY_2 Allocation failed - process out of memory

最终发现时内存不足导致。

 - 查看htop 排序mem 后发现 mongodb 最多，node 其次
 - pm2 部分节点特高mem 占用>2G
 - 


综此，估计是mem 占用高，而涌入的流量读取静态文件，在disk io <-> mem 之间由于不够内存，经常swap。导致等待特别长。同时导致处理一个connection request 特别长，涌入的流量消耗了大量cpu 资源去维持。

优化措施后续更新。


  [1]: http://7xk67t.com1.z0.glb.clouddn.com/mem-ga.png
  [2]: http://7xk67t.com1.z0.glb.clouddn.com/mem-realtime.png
  [3]: http://7xk67t.com1.z0.glb.clouddn.com/mem-network.png
  [4]: http://7xk67t.com1.z0.glb.clouddn.com/mem-pm2.png
  [5]: http://7xk67t.com1.z0.glb.clouddn.com/mem-pm2-detail.png

# Master&Woker模式

Nginx启动之后，启动了80端口进行服务监听，那么进程中就存在一个Mater主进程和多个Woker进程；

![Master&Woker%E6%A8%A1%E5%BC%8F%20f50e707256cd4c8b83c1c4e1761e0c7d/Untitled.png](Master&Woker%E6%A8%A1%E5%BC%8F%20f50e707256cd4c8b83c1c4e1761e0c7d/Untitled.png)

Master进程的作用就是：读取&验证nginx.conf配置文件并且管理多个woker进程；接受外部信号；监控Woker，如果Woker挂掉，将自动重启Woker；

Woker进程的作用就是：多个Woker会拦截所有的请求并做出处理；每一个woker进程维护一个线程；woker的个数和CPU有关，从nginx.conf配置woker个数，配置几个就是几个，但是要避免配置过多，要充分利用CPU；

**一个请求到响应的流程：**

1. Nginx启动，Matster进程根据nginx.conf初始化；初始化监听socket；fork出多个woker进程；
2. 发起请求
3. woker进程们一起竞争，胜出者通过三次握手，建立socket连接，处理请求。
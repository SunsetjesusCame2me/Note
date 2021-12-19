# 通用知识

## 网络带宽、Qps、Tps

- **网络带宽**：单位是bps，多少位bits在1s内被传输，通常为 1GB/s 512KB/s

- **TPS**：Transaction Per sec

  - 每秒的事务数：“事务”具体看如何定义：

    i. 单接口： Client向Server请求 ---> 服务器内部处理 ---->  返回结果给Client

    ​	              | ----------------------     Transcation    -------------------------------------|

    

    ii. 多接口： A 、B、C三个接口

    ​				A接口：Client向Server请求 ---> 服务器内部处理 ---->  返回结果给Client

    ​				B接口：Client向Server请求 ---> 服务器内部处理 ---->  返回结果给Client

    ​				C接口：Client向Server请求 ---> 服务器内部处理 ---->  返回结果给Client

    ​							 | ----------------------     Transcation    -------------------------------------|

    > TPS = 1s内完成Transaction的次数

- **QPS**：Queries Per sec

  - 每秒查询率：通常是指的服务器响应请求
    - 比如：这个接口1mins被访问了600次， QPS = 600次 / 60s = 10 qps

- **区别**：

  - TPS中可能会包含多个QPS，即Transaction会包含多个Queries



## 网关 

### **物理网关**：

> 计网中的网关，是指的是 **同一个广播域** 里面的子网的出口，gateway

![image-20211216230310979](pics/API_gateway_2.jpg)



### **Restful API:**

![image-20211216235943160](pics/API_gateway_3.jpg)

RestAPI是一种 **设计理念**：

> **url** 定位资源，**method** 描述操作
>
> 在之前：/room/123
>
> - 打开房间：open/room/123
> - 添加东西：put_something/room/123
>
> 极为混乱
>
> 
>
> 引入 **RestAPI** 理念后
>
> - 打开房间：room/123  GET
> - 关闭房间：room/123  POST



### **API网关** 和 **CGI**

> 一言以蔽之：
>
> - API网关 是通过url分发给不同服务，CGI 是通过对应服务的子url对DB进行不同的操作
> - API网关是 **不同服务**，CGI是 **一种服务下不同的功能**

![image-20211219132915255](pics/API_gateway_4.jpg)

​					原先每个用户需要访问 **不同url** 来访问不同服务，并且每个服务都要自己做 **流量管理**

![image-20211219135015014](pics/API_gateway_5.jpg)

> 将这些服务通用的、需要的功能：**服务降级**、**负载均衡**、**流量监控** 抽象出来放到API网关处理

![image-20211219171551849](pics/API_gateway_6.jpg)

​			CGI实际上是某一个服务内部的一个划分，通用网关接口，通过url对DB有不同的操作

### Nginx

> Nginx就是通常用来实现API网关的一个工具，充当反向代理服务器的作用：

- **正向代理 && 反向代理**

  > 一言以蔽之：  正向隐藏client，反向隐藏server
  >
  > ​						只不过是概念上的偏差、本质上都是走一个代理而已

  ![image-20211219220359702](pics/API_gateway_8.jpg)

  ​							正向代理：**给Client代理**，Server端看不见Client的ip，只能看见proxy服务器的信息

  ![image-20211219220628274](pics/API_gateway_9.jpg)

  ​							反向代理：**给Server代理**，Client端看不见访问服务器的ip，只能看见proxy服务器信息

  

- **好处**：

  > 1. 保护**内网安全**，通过中介服务器的方式，可以访问到之前访问不了的资源
  > 2. **负载均衡**，减少原服务器的压力
  > 3. API网关服务器可以用来做**静态文件的缓存**、提高访问速度

- **工作原理**：

![image-20211219212840403](pics/API_gateway_7.jpg)

> **master进程：**用来管理 worker进程，监听想连接的**socketID**
>
> **woker进程：** 有了**socketID**后，从master里fork出来一个worker进程，worker进程来**accept**这个**socket**
>
> ​					   nginx提供了accept锁，实现当前只有一个进程可以**accept**这个**socket**


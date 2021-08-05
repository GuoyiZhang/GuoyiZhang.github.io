# 【架构】Nginx、HAProxy、LVS三者的优缺点分析


## 一、Nginx服务器

### Nginx优点：

1、工作在网络7层之上，可针对http应用做一些分流的策略，如针对域名、目录结构，它的正规规则比HAProxy更为强大和灵活，所以，目前为止广泛流行。

2、Nginx对网络稳定性的依赖非常小，理论上能ping通就能进行负载功能。

3、Nginx安装与配置比较简单，测试也比较方便，基本能把错误日志打印出来。

4、可以承担高负载压力且稳定，硬件不差的情况下一般能支撑几万次的并发量，负载度比LVS小。

5、Nginx可以通过端口检测到服务器内部的故障，如根据服务器处理网页返回的状态码、超时等，并会把返回错误的请求重新提交到另一个节点。

6、不仅仅是优秀的负载均衡器/反向代理软件，同时也是强大的Web应用服务器。LNMP也是近些年非常流行的Web架构，在高流量环境中稳定性也很好。

7、可作为中层反向代理使用。

8、可作为静态网页和图片服务器。

9、Nginx社区活跃，第三方模块非常多，相关的资料在网上比比皆是。

**Nginx常规的和HTTP请求和相应流程图：**

![](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy4xNzhsaW51eC5jb20vd3AtY29udGVudC91cGxvYWRzLzIwMTcvMDYvMjAxNzA2MjQxNDU2MDhfMzM3MjcucG5n?x-oss-process=image/format,png)

### Nginx缺点：

1、适应范围较小，仅能支持http、https、Email协议。

2、对后端服务器的健康检查，只支持通过端口检测，不支持url来检测。比如用户正在上传一个文件，而处理该上传的节点刚好在上传过程中出现故障，Nginx会把上传切到另一台服务器重新处理，而LVS就直接断掉了，如果是上传一个很大的文件或者很重要的文件的话，用户可能会因此而不满。

## 二、LVS服务器

### LVS优点：

1、抗负载能力强、是工作在网络4层之上仅作分发之用，没有流量的产生，这个特点也决定了它在负载均衡软件里的性能最强的，对内存和cpu资源消耗比较低。

2、配置性比较低，这是一个缺点也是一个优点，因为没有可太多配置的东西，所以并不需要太多接触，大大减少了人为出错的几率。

3、工作稳定，因为其本身抗负载能力很强，自身有完整的双机热备方案，如LVS+Keepalived，不过我们在项目实施中用得最多的还是LVS/DR+Keepalived。

4、无流量，LVS只分发请求，而流量并不从它本身出去，这点保证了均衡器IO的性能不会收到大流量的影响。

5、应用范围比较广，因为LVS工作在4层，所以它几乎可以对所有应用做负载均衡，包括http、数据库、在线聊天室等等。

**LVS DR(Direct Routing)模式的网络流程图：**

![](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy4xNzhsaW51eC5jb20vd3AtY29udGVudC91cGxvYWRzLzIwMTcvMDYvMjAxNzA2MjQxNDU2MThfMTMwNTQucG5n?x-oss-process=image/format,png)

### LVS缺点：

1、软件本身不支持正则表达式处理，不能做动静分离；而现在许多网站在这方面都有较强的需求，这个是Nginx/HAProxy+Keepalived的优势所在。

2、如果是网站应用比较庞大的话，LVS/DR+Keepalived实施起来就比较复杂了，特别后面有Windows Server的机器的话，如果实施及配置还有维护过程就比较复杂了，相对而言，Nginx/HAProxy+Keepalived就简单多了。

## 三、HAProxy服务器：

### HAProxy优点：

1、HAProxy是支持虚拟主机的，可以工作在4、7层(支持多网段)

2、HAProxy的优点能够补充Nginx的一些缺点，比如支持Session的保持，Cookie的引导；同时支持通过获取指定的url来检测后端服务器的状态。

3、HAProxy跟LVS类似，本身就只是一款负载均衡软件；单纯从效率上来讲HAProxy会比Nginx有更出色的负载均衡速度，在并发处理上也是优于Nginx的。

4、HAProxy支持TCP协议的负载均衡转发，可以对MySQL读进行负载均衡，对后端的MySQL节点进行检测和负载均衡，大家可以用LVS+Keepalived对MySQL主从做负载均衡。

5、HAProxy负载均衡策略非常多，HAProxy的负载均衡算法现在具体有如下8种

**①　roundrobin**

表示简单的轮询，每个服务器根据权重轮流使用，在服务器的处理时间平均分配的情况下这是最流畅和公平的算法。该算法是动态的，对于实例启动慢的服务器权重会在运行中调整。最大支持4095个后端主机；

**②　leastconn**

连接数最少的服务器优先接收连接。leastconn建议用于长会话服务，例如LDAP、SQL、TSE等，而不适合短会话协议。如HTTP.该算法是动态的，对于实例启动慢的服务器权重会在运行中调整。

**③　static-rr**

每个服务器根据权重轮流使用，类似roundrobin，但它是静态的，意味着运行时修改权限是无效的。另外，它对服务器的数量没有限制。该算法一般不用；

**④　source**

对请求源IP地址进行哈希，用可用服务器的权重总数除以哈希值，根据结果进行分配。只要服务器正常，同一个客户端IP地址总是访问同一个服务器。如果哈希的结果随可用服务器数量而变化，那么客户端会定向到不同的服务器；该算法一般用于不能插入cookie的Tcp模式。它还可以用于广域网上为拒绝使用会话cookie的客户端提供最有效的粘连；该算法默认是静态的，所以运行时修改服务器的权重是无效的，但是算法会根据“hash-type”的变化做调整。

**⑤　uri**

表示根据请求的URI左端（问号之前）进行哈希，用可用服务器的权重总数除以哈希值，根据结果进行分配。只要服务器正常，同一个URI地址总是访问同一个服务器。一般用于代理缓存和反病毒代理，以最大限度的提高缓存的命中率。该算法只能用于HTTP后端；该算法一般用于后端是缓存服务器；该算法默认是静态的，所以运行时修改服务器的权重是无效的，但是算法会根据“hash-type”的变化做调整。

**⑥　url_param**

在HTTP GET请求的查询串中查找<param>中指定的URL参数，基本上可以锁定使用特制的URL到特定的负载均衡器节点的要求；该算法一般用于将同一个用户的信息发送到同一个后端服务器；该算法默认是静态的，所以运行时修改服务器的权重是无效的，但是算法会根据“hash-type”的变化做调整。

**⑦　hdr(name)**

在每个HTTP请求中查找HTTP头<name>，HTTP头<name>将被看作在每个HTTP请求，并针对特定的节点；如果缺少头或者头没有任何值，则用roundrobin代替；该算法默认是静态的，所以运行时修改服务器的权重是无效的，但是算法会根据“hash-type”的变化做调整。

**⑧　rdp-cookie（name）**

为每个进来的TCP请求查询并哈希RDP cookie<name>；该机制用于退化的持久模式，可以使同一个用户或者同一个会话ID总是发送给同一台服务器。如果没有cookie，则使用roundrobin算法代替；该算法默认是静态的，所以运行时修改服务器的权重是无效的，但是算法会根据“hash-type”的变化做调整。

**haproxy的工作模型图：**

![](https://imgconvert.csdnimg.cn/aHR0cDovL3d3dy4xNzhsaW51eC5jb20vd3AtY29udGVudC91cGxvYWRzLzIwMTcvMDYvMjAxNzA2MjQxNDU2MjhfODM1ODIucG5n?x-oss-process=image/format,png)

### HAPorxy缺点：

1. 不支持POP/SMTP协议

2. 不支持SPDY协议

3. 不支持HTTP cache功能。现在不少开源的lb项目，都或多或少具备HTTP cache功能。

4. 重载配置的功能需要重启进程，虽然也是soft restart，但没有Nginx的reaload更为平滑和友好。

5. 多进程模式支持不够好


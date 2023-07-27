---
layout: post
title: 一致性哈希(Consistent hashing)算法
date: 2018-05-25 14:45:00.000000000 +09:00
tags: 一致性哈希 Consistent hashing 分布式系统
---

# 一致性哈希（Consistent hashing）原理及实现

**Note. 本文翻译参考至[consistent-hashing](https://www.toptal.com/big-data/consistent-hashing),结合github上的一个开源实现[lafikl/consistent](https://github.com/lafikl/consistent)对consistent hashing原理和细节作介绍。希望能帮助读者了解consistent hashing算法以及在分布式系统中的作用，解决一些分布式系统中遇到的问题。**


-  [0. 简介](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#0-%E7%AE%80%E4%BB%8B)

-  [1. 什么是hashing(哈希)？](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#1-%E4%BB%80%E4%B9%88%E6%98%AFhashing%E5%93%88%E5%B8%8C)

-  [2. 扩展：分布式哈希](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#2-%E6%89%A9%E5%B1%95%E5%88%86%E5%B8%83%E5%BC%8F%E5%93%88%E5%B8%8C)

-  [3. rehashing问题（再散列问题）](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#3-rehashing%E9%97%AE%E9%A2%98%E5%86%8D%E6%95%A3%E5%88%97%E9%97%AE%E9%A2%98)

-  [4. 解决方案：一致性哈希（Consistent hashing）](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#4-%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8Cconsistent-hashing)

    - [4.1 情况一：减少服务器数量](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#41-%E6%83%85%E5%86%B5%E4%B8%80%E5%87%8F%E5%B0%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%B0%E9%87%8F)
    
    - [4.2 情况二：增加服务器数量](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#42-%E6%83%85%E5%86%B5%E4%BA%8C%E5%A2%9E%E5%8A%A0%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%B0%E9%87%8F)

-  [5. 实现](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#5-%E5%AE%9E%E7%8E%B0)

-  [6. 总结](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-25-%E4%B8%80%E8%87%B4%E6%80%A7%E5%93%88%E5%B8%8C(Consistent%20hashing)%E7%AE%97%E6%B3%95.md#6-%E6%80%BB%E7%BB%93)

## 0. 简介

近年来，随着云计算和大数据等概念的出现，分布式系统越来越普及。

特别是高流量动态网站和web应用一般都会[分布式缓存](https://en.wikipedia.org/wiki/Distributed_cache)如redis等来提高并发能力，而像redis等分布式缓存使用了一致性哈希算法来实现请求的动态均衡。

什么是一致性哈希？为什么需要一致性哈希？

在本文中，我们先回顾哈希的一般概念以及用途，然后介绍分布式哈希以及存在的问题。最后，介绍一致性哈希算法以及实现。

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122753/toptal-blog-image-1492518934737-69e260a540e1c772e502104e933214f7.jpg">	
</div>

<p align="center">
  <b>图 1 一致性哈希概念图</b><br>
</p>

## 1. 什么是hashing(哈希)？

对于哈希函数，大家都不会陌生，各种编程语言都会有内置的hash map、hash table等数据结构。这些数据结构都有使用了特定的哈希函数，将一段数据(通常为某种对象，任意大小)映射到另一段数据（通常为整数，称作*hash code*）。

例如，一些用于散列字符串的哈希函数的输出范围位```0 .. 100```，可以将字符串```Hello```映射到数字`57`，把```Hasta la vista, baby```映射到数字```33```，以及把任何其他可能的字符串映射到0～100。当输入越来越多的时候，有可能有多个不同的字符串映射到相同的数字上，这种现象称为```冲突```。冲突可以通过开放地址法（再散列）或和链地址法（使用链存储冲突值）来解决，感兴趣的读者可参考[解决哈希表的冲突-开放地址法和链地址法](https://blog.csdn.net/w_fenghui/article/details/2010387)。好的散列函数应该是尽可能减少冲突，以便不同输入值能够尽可能均匀地分布在哈希范围内。

哈希函数有很多用途，而每一种哈希函数的属性也不太一样。从安全角度来看，哈希函数分为两类，一类是```加密散列函数```，一类是```非加密散列函数```。其中加密散列函数需要满足一组限制性的属性并用于安全目的。包括诸如密码保护、消息完整性检查和指纹识别以及数据损坏检测等应用。在区块链领域有广泛使用，比如证书指纹，用于检测证书内容是否被篡改。

非加密散列函数也有不同用途，这也是本文接下来所讨论的内容。

## 2. 扩展：分布式哈希

既然我们已经讨论了哈希，那么接下来准备介绍*分布式哈希*。

在某些情况下，很有必要将哈希表分成不同部分，每部分托管在不同的服务器上面。这样能够避免单个计算机的内存无法装下整个哈希表的情况，允许构建任意大的散列表（给定足够的服务器）。

在这种情况下，对象（和它们对应的key）*分布*在几个服务器中。这也是分布式哈希名称来源。

一个典型的用例就是内存缓存，比如[Memcached](https://en.wikipedia.org/wiki/Memcached)。

内存缓存由一组缓存服务器组成，这些缓存服务器托管许多key/value对，并用于对外提供数据的快速访问。例如，为了减少数据库服务器上的负载并同时提高性能，应用程序可以设计为首先从缓存服务器获取数据，并且只有缓存服务器上也没有所查询的数据时（*缓存未命中*），才会访问数据库获取数据。获取完数据后，再次把数据缓存到服务器的内存里，下次就可以直接访问内存获取到数据。访问内存速度比访问数据库（I/O）是要快很多的。

利用分布式集群分散地存储数据，可以提高并发。但究竟怎么把对象散列到不同机器上呢？散列原则是什么？

最简单的散列方式就是通过计算某个key的哈希值，然后模上机器数量，根据结果散列到指定机器。就是说，```server = hash(key) mod N```,其中```N```是机器数量。为了存储或检索某个key的时候，客户端首先计算key的哈希值，应用```mod N```操作，并根据mod结果的值找到对应的机器。

我们来看一个例子。假设我们有三台服务器，**A**，**B**，**C**，并且我们有一些带有哈希值的字符串key：

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>HASH mod 3</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>2</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>0</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>1</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>0</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>2</td>
    </tr>
</table>

用户想要检索key为**john**的value。这个key的hash值mod 3为2，因此会这个key/value对会关联到服务器**C**。这个key/value对不在C服务器上，用户从源中获取数据并把数据对添加到C中。数据分布如下：

<table>
    <tr>
        <th>A</th><th>B</th><th>C</th>
    </tr>
    <tr>
        <td></td><td></td><td>"john"</td>
    </tr>
</table>

接下来，另一个用户想要检索key为**bill**的value。这个key的hash值mod 3为0，所以这个key/value对会关联到服务器**A**。这个kv对不在A上，用户从源获取数据，并把它添加到A中。数据分布如下：

<table>
    <tr>
        <th>A</th><th>B</th><th>C</th>
    </tr>
    <tr>
        <td>"bill"</td><td></td><td>"john"</td>
    </tr>
</table>

余下的kv对分别添加到对应的服务器后，数据分布如下：

<table>
    <tr>
        <th>A</th><th>B</th><th>C</th>
    </tr>
    <tr>
        <td>"bill"</td><td>"jane"</td><td>"john"</td>
    </tr>
    <tr>
        <td>"steve"</td><td></td><td>"kate"</td>
    </tr>
</table>

## 3. rehashing问题（再散列问题）

上面通过key的hash值mod上服务器数量这种分配方案简单、直观，并且能正常运行。但是，如果**服务器数量发生了变化**,如某些服务器崩溃了，或者需要添加新机器提高集群的负载，整个方案会有什么问题呢？首先，当某台服务器挂了，原本存储在这台服务器上的kv对必须重新分配，否则会丢失数据。类似地，如果增加新机器，需要重新分配kv对，保证原来的数据能正确被检索到并且新增的数据能够被分配到新的机器上。如果使用上面的简单的分配方案，服务器数量发生变化时，大多数**hash modulo N**会发生改变，所以大部分的kv对需要迁移到不同机器上。就算是仅仅增加或减少一台机器，都会出现这种情况。

上面的例子假设删除掉服务器**C**必须使用**hash modulo 2**而不是**hash modulo 3**来重新哈希所有的kv对，数据分布如下：

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>HASH mod 2</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>0</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>1</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>0</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>1</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>1</td>
    </tr>
</table>

<table>
    <tr>
        <th>A</th><th>B</th>
    </tr>
    <tr>
        <td>"john"</td><td>"bill"</td>
    </tr>
    <tr>
        <td>"jane"</td><td>"steve"</td>
    </tr>
    <tr>
        <td></td><td>"kate"</td>
    </tr>
</table>

可以发现，不仅仅是服务器**C**上的kv对位置发生了变化，所有的kv对的散列位置都发生了变化。

在我们之前提到的典型用例（缓存）中，这意味着，所有kv对都不能被正常检索。

因此，大多数查询都不能命中缓存，并且原始数据可能需要再次从源检索，从而给源服务器（通常是数据库）带来沉重的负担。这可能会严重降低性能，并可能导致原始服务器崩溃。

## 4. 解决方案：一致性哈希（Consistent hashing）

鉴于以上问题，需要有一种更好的散列方式。新的散列方式，能够不依赖于服务器数量，因此，在添加或删除服务器时，需要重新散列的kv对数量会减至最小。*一致性哈希*正式登场，这就是本篇博客重要介绍内容，这种方案非常简单但很有效。首先由[MIT的Karger等人](http://courses.cse.tamu.edu/caverlee/csce438/readings/consistent-hashing.pdf)在1997年的一篇学术paper里面提出。

一致性哈希是一种分布式哈希方案，它通过分配给抽象的圆或哈希环上的位置来独立于分布式*哈希表*中的服务器或对象的数量进行操作。这样可以在不影响整个系统的情况下扩展服务器和对象。

想象一下，我们将散列输出范围映射到一个圆环上。这意味着最小可能的散列值零将对应于零角度，最大可能值（我们称之为**INT_MAX**）将对应于2𝝅弧度（或360度）的角度，并且所有其他哈希值将线性地分布在这两个值之间。给定一个key，计算出它的hash值，就能找到这个key所在圆的位置。

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122754/toptal-blog-image-1492519110829-2b83d00078842cb94e5c8abc1f36e29c.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td>
    </tr>
</table>

现在想象服务器分布在圆上，通过伪随机分配一个角度。这应该以可重复的方式完成（或者至少以所有客户都同意服务器的角度方式）。这样做的一种便利方法是将服务器名称（或IP地址、某个ID）散列来计算出角度。

服务器拓扑图和数据分布如下：

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122755/toptal-blog-image-1492519238710-e11edbf0d1962133a8cfdbdf9dbce004.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td>
    </tr>
    <tr>
        <td>"A"</td><td>5572014558</td><td>200.6</td>
    </tr>
    <tr>
        <td>"B"</td><td>8077113362</td><td>290.8</td>
    </tr>
    <tr>
        <td>"C"</td><td>2269549488</td><td>81.7</td>
    </tr>
</table>

由于kv对和服务器都分布在同一个逻辑上的圆，我们可以定义一个简单的规则来将两者关联起来：每个key分配到逆时针方向上离它最近的服务器（或顺时针，取决于所使用的约定）。kv对以及服务器的关系图如下：

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122756/toptal-blog-image-1492519276035-d42459e7afcbed4a7ccb88d45b3d750b.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th><th>LABEL</th><th>SERVER</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td><td>"C"</td><td>C</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td><td>"A"</td><td>A</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td><td>"A"</td><td>A</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td><td>"B"</td><td>B</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td><td>"C"</td><td>C</td>
    </tr>
</table>

从编程的角度来看，我们要做的是保存一个服务器值的有序列表（可以是角度或数字列表），然后遍历此列表（或使用**二分查找**，后续的实现也是用了这种方法）以找到第一个值大于或等于检索的key的hash值的服务器，然后从该服务器取出key对应的value。

为了确保kv对能够均匀分布在服务器间，需要应用一个简单的技巧：为每个服务器分配多个标签。因此，可以用**A0 .. A9**，**B0 ... B9**和**C0 ... C9**来代替**A**，**B**,**C**。每个服务器会有一个权重，权重越高，标签越多，分布在圆上的位置也越多，因此负载也会更高。可以综合考虑服务器性能等特点来调整权重。例如，如果服务器B的CPU运行速度、内存和外存空间为其他服务器的2倍，那么可以分配两倍的标签，结果，它最终会拥有两倍的kv对（平均而言）。

对于上面的例子，假设都有相同的权重10，那么服务器在圆上的分布图如下：

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122757/toptal-blog-image-1492519537971-cf17c6af62e3457ecf2ee8cd3ae5d18e.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th>
    </tr>
    <tr>
        <td>"C6"</td><td>408965526</td><td>14.7</td>
    </tr>
    <tr>
        <td>"A1"</td><td>473914830</td><td>17</td>
    </tr>
    <tr>
        <td>"A2"</td><td>548798874</td><td>19.7</td>
    </tr>
    <tr>
        <td>"A3"</td><td>1466730567</td><td>52.8</td>
    </tr>
    <tr>
        <td>"C4"</td><td>1493080938</td><td>53.7</td>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.7</td>
    </tr>
    <tr>
        <td>"B2"</td><td>1808009038</td><td>65</td>
    </tr>
    <tr>
        <td>"C0"</td><td>1982701318</td><td>71.3</td>
    </tr>
    <tr>
        <td>"B4"</td><td>2058758486</td><td>74.1</td>
    </tr>
      <tr>
        <td>"C9"</td><td>3359725419</td><td>120.9</td>
    </tr>
      <tr>
        <td>"kate"</td><td>3421657995</td><td>123.1</td>
    </tr>
      <tr>
        <td>"A5"</td><td>3434972143</td><td>123.6</td>
    </tr>    
    <tr>
        <td>"C1"</td><td>3672205973</td><td>132.1</td>
    </tr>
    <tr>
        <td>"C8"</td><td>3750588567</td><td>135</td>
    </tr>    
    <tr>
        <td>"B0"</td><td>4049028775</td><td>145.7</td>
    </tr>
    <tr>
        <td>"B8"</td><td>4755525684</td><td>171.1</td>
    </tr>
    <tr>
        <td>"A9"</td><td>4769549830</td><td>171.7</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td>
    </tr>
    <tr>
        <td>"C7"</td><td>5014097839</td><td>180.5</td>
    </tr>
    <tr>
        <td>"B1"</td><td>5444659173</td><td>196</td>
    </tr>
    <tr>
        <td>"A6"</td><td>6210502707</td><td>223.5</td>
    </tr>
      <tr>
        <td>"A0"</td><td>6511384141</td><td>234.4</td>
    </tr>
      <tr>
        <td>"B9"</td><td>7292819872</td><td>262.5</td>
    </tr>
      <tr>
        <td>"C3"</td><td>7330467663</td><td>263.8</td>
    </tr>
      <tr>
        <td>"C5"</td><td>7502566333</td><td>270</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td>
    </tr>
    <tr>
        <td>"A4"</td><td>8047401090</td><td>289.7</td>
    </tr>
    <tr>
        <td>"C2"</td><td>8605012288</td><td>309.7</td>
    </tr>
    <tr>
        <td>"A8"</td><td>8997397092</td><td>323.9</td>
    </tr>
      <tr>
        <td>"B7"</td><td>9038880553</td><td>325.3</td>
    </tr>
      <tr>
        <td>"B5"</td><td>9368225254</td><td>337.2</td>
    </tr>
    <tr>
        <td>"B6"</td><td>9379713761</td><td>337.6</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td>
    </tr>
</table>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th><th>LABEL</th><th>SERVER</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td><td>"B2"</td><td>B</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td><td>"A5"</td><td>A</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td><td>"C7"</td><td>C</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td><td>"A4"</td><td>A</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td><td>"C6"</td><td>C</td>
    </tr>
</table>

### 4.1 情况一：减少服务器数量

那么，通过把kv对映射到圆环的方式有没有好处呢？假设服务器**C**被删除掉，圆环上的标签**C0 ... C9**将被删除。这会导致被删除的标签上存储的kv对将重新分配到临近的标签**Ax**或者**Bx**中，相当于重新分配给服务器**A**和服务器**B**。

但是原本存储在服务器**A**或**B**上的kv对是否需要迁移才能重新被正确检索呢？**答案是：原封不动就可以检索到对应的值**。缺少**Cx**标签不会影响到**Ax**或**Bx**标签上的值，也就不会影响到服务器A、B。如下图所示：

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122758/toptal-blog-image-1492519596181-b11847a4171cb6a399fff1b6d251c06a.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th><th>LABEL</th><th>SERVER</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td><td>"B2"</td><td>B</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td><td>"A5"</td><td>A</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td><td>"B1"</td><td>B</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td><td>"A4"</td><td>A</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td><td>"A1"</td><td>A</td>
    </tr>
</table>


### 4.2 情况二：增加服务器数量

同样地，当增加服务器数量时，为了能够正确检索出原来分布在服务器**A**、**B**、**C**上的kv对，是否需要大量迁移数据呢？另外，新增机器后，所有机器的负载是否均衡？现在假设在删除了机器**C**的基础上，我们新增机器**D**，添加标签**D0 ... D9**。大概有3分之1的数据需要重新分配到**D**，而剩余的数据保持不变。如下图所示：

<div align="center">
<img src="https://uploads.toptal.io/blog/image/122759/toptal-blog-image-1492519628143-6699eb1c37c92cbd5ccf76eaa27dbd29.jpg">	
</div>

<table>
    <tr>
        <th>KEY</th><th>HASH</th><th>ANGLE(DEG)</th><th>LABEL</th><th>SERVER</th>
    </tr>
    <tr>
        <td>"john"</td><td>1633428562</td><td>58.8</td><td>"B2"</td><td>B</td>
    </tr>
    <tr>
        <td>"kate"</td><td>3421657995</td><td>123.2</td><td>"A5"</td><td>A</td>
    </tr>
    <tr>
        <td>"jane"</td><td>5000799124</td><td>180</td><td>"B1"</td><td>B</td>
    </tr>
    <tr>
        <td>"bill"</td><td>7594634739</td><td>273.4</td><td>"A4"</td><td>A</td>
    </tr>
    <tr>
        <td>"steve"</td><td>9787173343</td><td>352.3</td><td>"D2"</td><td>D</td>
    </tr>
</table>

所以在服务器数量发生变化时，相对于module机器数量算法，一致性哈希算法只需要对小部分数据进行迁移，就能够正常检索和保持新集群的数据负载均衡。这就是一致性哈希算法解决再哈希问题的方法。

通常，当**k**为数据总量，**N**为服务器数量时，当服务器数量发生变化时，大约只有**k/N**份数据需要重新映射，也就是说只有**N分之k**的数据需要迁移。

## 5. 实现

接下来，我们通过分析开源的一致性哈希算法库[lafikl/consistent](https://github.com/lafikl/consistent)了解下算法细节。

**Consistent**为一致性哈希算法的核心数据结构，如下：

```
type Consistent struct {
	hosts     map[uint64]string   // 存储表格散列值-表格名kv对或主机散列值-主机名kv对等，
	sortedSet []uint64            // 表的哈希值列表，已经有序，当给一定key的哈希值，能直接用二分查找法快速检索出这个key所属表、主机的哈希值
	loadMap   map[string]*Host    // 存储表名-表的kv对、主机ip地址-Host的kv对等等
	totalLoad int64               // 记录所有机器的负载

	sync.RWMutex                 // 避免有多个goroutine同时修改Consistent导致冲突
}
```

下面是往集群添加一台机器的代码：

```
func (c *Consistent) Add(host string) { // host可以表示任意对象，如主机ip地址127.0.0.1:8000或者表名1024
	c.Lock()                 // 加锁，避免并发修改导致的冲突
	defer c.Unlock()

	if _, ok := c.loadMap[host]; ok {    // 如果主机或表已经存在，直接返回
		return
	}

	c.loadMap[host] = &Host{Name: host, Load: 0}   // 初始化host主机或表的负载
	for i := 0; i < replicationFactor; i++ {       // 这里replicationFactor默认为10，也就是说1个表对应10个虚表、1台机器对应10台虚拟机器，提高机器负载的均衡性
		h := c.hash(fmt.Sprintf("%s%d", host, i))  // 求出每台虚拟机的地址或虚拟表的hash值，为uint64类型
		c.hosts[h] = host                          // uint64的哈希值-主机ip地址
		c.sortedSet = append(c.sortedSet, h)       // 把计算的哈希值添加到列表

	}
	// sort hashes ascendingly
	sort.Slice(c.sortedSet, func(i int, j int) bool {   // 对更新后的主机hash值列表进行排序
		if c.sortedSet[i] < c.sortedSet[j] {
			return true
		}
		return false
	})
}

```

下面是检索某个key对应的主机ip地址代码：

```
// Returns the host that owns `key`.
//
// As described in https://en.wikipedia.org/wiki/Consistent_hashing
//
// It returns ErrNoHosts if the ring has no hosts in it.
func (c *Consistent) Get(key string) (string, error) { // 计算某个key对应的表名或主机ip地址
	c.RLock()
	defer c.RUnlock()

	if len(c.hosts) == 0 {
		return "", ErrNoHosts
	}

	h := c.hash(key)       // 先算出key的哈希值
	idx := c.search(h)     // 通过二分查找出第一个等于或比key大的主机ip地址在列表的索引值，如果找不到，就用列表第1个主机，因为有序列表可以看作是一个环，列表尾部与头部是相接的
	return c.hosts[c.sortedSet[idx]], nil
}

func (c *Consistent) search(key uint64) int {
	idx := sort.Search(len(c.sortedSet), func(i int) bool {   // 使用sort包查找出第一个大于或等于key值的表或主机ip
		return c.sortedSet[i] >= key
	})

	if idx >= len(c.sortedSet) {  // 如果找不到，那么就用列表第1个元素
		idx = 0  
	}
	return idx
}
```

最后是删除指定主机的代码：

```
// Deletes host from the ring
func (c *Consistent) Remove(host string) bool {
	c.Lock()
	defer c.Unlock()

	for i := 0; i < replicationFactor; i++ {           // 把表对应的10张虚拟表或主机对应的10台虚拟机器从列表中删除
		h := c.hash(fmt.Sprintf("%s%d", host, i))
		delete(c.hosts, h)
		c.delSlice(h)
	}
	delete(c.loadMap, host)                           // 同时把主机的负载信息删除
	return true
}
```

以上为一致性哈希算法的核心代码，原文实现还考虑到了机器的负载，比如能够基于把key散列到负载最小的机器，尽可能保持集群中所有机器的负载在同一水平，感兴趣的读者可参考[google的一篇关于一致性哈希算法负载研究paper](https://research.googleblog.com/2017/04/consistent-hashing-with-bounded-loads.html)。

## 6. 总结

以前在实习的时候，为了提高数据库的查询性能，对数据库进行了分库分表，分库分表算法就是用的最简单朴素module算法。比如1个库有64张表存储广告信息，用广告的id mod 64算出广告对应的表。但后期发现，当每个表的数据太多的时候，需要继续增加表来提高性能时。发现原来的数据需要大量迁移才能保证数据的正常检索，从而导致迁移需要停止服务的时间太长。后来才知道一致性哈希算法，希望通过这篇博客来学习一致性哈希算法，以后再遇到类似的情况时，能够知道怎么去使用并解决遇到的问题：）



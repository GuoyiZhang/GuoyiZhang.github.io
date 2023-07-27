---
layout: post
title: 数据结构之Log Structured Merge Trees
date: 2019-01-09 14:51:00.000000000 +09:00
tags: 数据结构 LSM 数据存储 BloomFilter
---


# Log Structured Merge Trees介绍

**Note. 本文主要翻译自[Log Structured Merge Trees](https://kunigami.blog/2018/07/19/log-structured-merge-trees/)一文，在那篇文章里作者详细介绍了LSM这种写密集型高效存储数据结构，本文在此基础上补充了一些性能分析如时间复杂度、空间复杂度等内容和提出一些疑惑。希望能给各位读者抛砖引玉，本人水平有限请多指教。**

- [1. B+树和Append Logs](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#1-b%E6%A0%91%E5%92%8Cappend-logs)

- [2. LSM树](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#2-lsm%E6%A0%91)

- [3. 具有层级压缩的LSM](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#3-%E5%85%B7%E6%9C%89%E5%B1%82%E7%BA%A7%E5%8E%8B%E7%BC%A9%E7%9A%84lsm)
    
- [4. 一些实现细节](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#4-%E4%B8%80%E4%BA%9B%E5%AE%9E%E7%8E%B0%E7%BB%86%E8%8A%82)

- [5. 存储性能分析](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#5-%E5%AD%98%E5%82%A8%E6%80%A7%E8%83%BD%E5%88%86%E6%9E%90)

- [6. 读性能简析](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#6-%E8%AF%BB%E6%80%A7%E8%83%BD%E7%AE%80%E6%9E%90)

- [7. 参考资料](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-01-08-Log%20Structured%20Merge%20Trees.md#7-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)


在这篇博客中我们讲讨论一种名为Log Structured Merge树(LSM树)。在写密集情境下，是一种有效替代类似B+树应用存储的数据结构。

在这篇[1]文章里，作者指出硬件性能提高会给读性能带来更大的提升，而给写性能所带来的提升要小很多。因此选择一种能提升写性能的数据结构很有意义。

## 1. B+树和Append Logs

B+树是一种读操作性能很高的数据结构，它把数据组织成树状结构，并且在不断存储数据过程中动态调整树的高度，使得树的整体高度尽可能。因此当查找一条数据的时候，不需要太多的树层次查找次数就能找到数据，这大大提高了读性能，因为每查找一层树节点，就是一次耗时的外存随机访问，外存的访问时间远远超于内存。

Adam Jacobs[3]做过一个实验，顺序访问旋转磁盘的吞吐率能达到50M/s，而相同条件下的随机访问吞吐率只有300/S（大概慢了100,000倍）。而使用固态硬盘的话，则差距能够缩小，但两种方式的吞吐率仍然相差很大，分别是40M/s和2000/s。

还有另外极端点的方式去避免进行随机访问从而进行耗时的磁盘寻道就是顺序往磁盘写入内容。我们只用把数据的每一行添加到日志文件的末尾就能做到这点。但这种方式会有问题，只是按顺序把数据存储到文件末尾，没有形成一个有效的数据结构，那么查找一条数据最坏的情况下需要遍历整个文件。当文件越来越大的时候，将会非常耗时。

LSM树这种数据结构就是为了不牺牲太多的读性能情况下，去获得更好的写性能。LSM树的思路是把数据写到日志文件，但是当文件变得太大的小时，会重新组织数据从而优化读性能。我们可以把它看作一种会批量更新的数据结构。

首先，我们会先描述原始版本的LSM树，然后再描述一种优化版本的LSM树，优化之后的LSM在生产环境中有更好的性能，并且已经应用于LevelDB[4]这样的kv型数据库。

## 2. LSM树

我们先了解下如何基于LSM树实现一个key-value型数据库。在刚开始写入一个key-value对的时候，会把数据写入到一个叫做<b>memtable</b>的内存结构里。而key-value对根据key的顺序存储到memtable（随机访问内容的耗时是很少的，并且可以使用二分法之类的搜索很快就能检索到一个key-value对）。当memtable达到存储容量上限的时候，就会将数据持久化到硬盘里（**注：如果在达到容量上限并且持久化到硬盘前，数据库崩溃了，memtable的数据会不会对丢失，从而导致整个数据库丢失部分数据？定期维护快照是否能够解决问题？**）。写入数据过程大致如图1所述：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/lsm/lsm-insert-mem2.png?raw=true" height="200" width="600">	
</div>

<p align="center">
  <b>图 1 lsm树插入key-value对的大致过程</b><br>
</p>

在这种结构下，查找一个key-value对，需要遍历文件然后在每个文件中用二分查找去搜索具体的key。为key创建索引的话，就可以很快检索到数据。要注意的是：一个key可能会出现在多个文件中，这表示该key被多次写入。我们可以只扫描包含这个key-value对的最近更新的文件，因为最近更新的文件包含着这个key的最新值。检索过程中大部分开销在于线性扫描文件。当数据库数的存储量很大的时候，要线性遍历大量文件将非常耗时，导致读取key-value对也非常耗时。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/lsm/lsm-write-disk2.png?raw=true" height="150" width="600">	
</div>

<p align="center">
  <b>图 2 将memtable持久化到磁盘过程</b><br>
</p>

为了避免这种情况，当数据库的文件数量增长超过指定数量后，LSM树会以外部归并排序方式，两两合并文件，并继续保持数据是以key排序。所以再次查找key-value对的时候，虽然文件的大小翻倍了，但是需要线性检索的文件数量减少了一半，从而搜索速度快了近一倍。（**注：假设原来文件数量为N，每个文件包含的key-value对数目为M，那么原来的检查平均时间复杂度为O(N/2 * lgM)，而合并后的时间复杂度为O(N/4 * lg(M/2))，为前者的一半。**）这种方式就是所谓的<b>tiered compaction</b>[2]，如图3所示。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/lsm/lsm-compaction1.png?raw=true" height="150" width="600">	
</div>

<p align="center">
  <b>图 3 文件压缩合并过程</b><br>
</p>

这种方式的主要缺点是当单个文件大小超过一定数量后，合并操作会变得非常耗时。给定m个文件大小为S的排好序的文件，合并操作的时间复杂度为O(m\*S\*lgS)。尽管合并操作发生的次数不频繁（通常是数据库大小翻倍的时候），但是每次合并会耗费很长时间。

作者的另外一篇博客[amortized analysis](https://kunigami.blog/2017/07/08/eliminating-amortization/)[5]包含相关数据结构的时间复杂度分析。我们可以看到尽管这种操作能产生一个平均性能更好的数据结构，但合并过程在最坏的情况下耗时太长的话，仍然不能应用于生产环境中。因为这就像木桶理论一样，一只水桶能装多少水取决于它最短的那块木板。同样地，LSM树要作为一种可用于生产环境的数据结构，首先要解决最坏情况下合并操作耗时太长的问题，就算合并发生的概率很少。

## 3. 具有层级压缩的LSM

一种能够有效减少LSM最坏情况下的读写时间方式是把每个存储的文件大小控制在一定范围内（小于2MB），并把这些文件按层划分。除了第一层比较特殊外，**同一层的任意两个文件都不会包含key相同的key-value对**。每一层会包含多个文件，但是整层的文件大小总和会限定在某个值内。每一层文件总大小是前一层的k倍。在LevelDB[4]中，第```L```层文件大小总和为不超过(10^L)MB。（就是说，第一层大小上限是10MB，第二层大小上限就是100MB，如此类推）

**提升.**无论哪一层，只要文件大小总和达到上限，就需要从这一层中选出一个文件与下一层的文件进行合并，或者提升到下一层。为了继续保持同一层不能包含相同的key这个属性，我们首先需要层下一层里面找出包含与需要合并的文件的key-value对的文件，并把它们合并成一个文件。但这种方式不是简单的生成一个合并完毕的文件，而是会生成多个大小上限为2MB的文件。在合并过程中，如果我们找到冲突（key相同的key-value对），我们只用从高层中丢弃key相同的key-value对即可，因为低层的文件包含的数据越新，只要丢掉老数据就可以了。图4描述了第0层提升到第1层的过程：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/lsm/lsm-level-promotion.png?raw=true" height="300" width="900">	
</div>

<p align="center">
  <b>图 4 第0层提升到第1层的过程</b><br>
</p>

## 4. 一些实现细节

当合并文件的时候，我们可以使用另外一种数据结构[Bloom filters](https://kunigami.blog/2015/01/29/bloom-filters/)对每个文件检测，找出某个key所在的文件。Bloom filter能够通过占用比较少的内存空间检查一个key是否存在于某个set。如果检测结果是这个key不在set里，那么就可以肯定key不在set里。但是如果检测结果是这个key在set里，那么这个结果有可能是错的（**注.这种情况下，只需要在文件里进行二分搜索即可判定**）。所以我们能够使用少量内存来快速检测某个key是否在指定文件里。

第3节提到过，多层LSM树的第一层比较特殊，第一层不需要保证相同的key的key-value对只出现一次。但是当合并的时候，就有可能需要合并多个文件，因为某个key可能在第一层出现多次，合并的时候需要合并所有包含key的文件。这种方式我们能够保证最新的key出现在最低层。

为了选出下一层中需要合并的文件，可以使用[round-robin](https://en.wikipedia.org/wiki/Round-robin_scheduling)策略。我们会维护文件的合并历史过程，当某个文件最近发生过合并的话，就会选择这个文件的下一个文件进行合并。这种方式能够保证每个文件最终都能得到合并。

当生成合并过后的文件时，我们可能会输出多个小于2MB的文件，以防检测到当前文件与下一层太多文件的key-value对有重合（LeveLDB是在10MB内）。这是为了避免某个文件在将来提升层级的时候会合并太多文件。

## 5. 存储性能分析

由于文件大小限定在2MB内，合并文件将是一个计算、资源成本相对低的操作。如上所述，我们限制了每两层之间不能包含太多重复的key，所以对于大小为11MB的数据，我们只需要合并大概11个文件，我们很容易通过内存的归并排序来完成。

在文件提升过程中，很容易产生级联情况。比如某个文件在从第**t**层提升到第**t+1**层时，会导致**t+1**层产生溢出，这就会导致另一次提升。幸运的是，总层数**L**是以O(lgn)的趋势增长，其中n是key-value数量。对于LevelDB来说，第一层大小限定为100MB，即使要存储100TB容量的数据，也最多只合并8层文件（10^8约等于100M，100M\*MB约等于10^5GB,也就是100TB）。

## 6. 读性能简析

事实上，每个key在每一层最多只存在于一个文件，这个属性能够让我们对每一层的文件建立索引（比如，一个持久化到硬盘的hash table，key就是key value里的key，而value就是某一层的文件index）。（**注.这当然不会对一层建索引，因为第一层里不同文件的key有可能重复，但是第一层的文件数量很少，所以缺少索引情况下线性查找也不会太耗时**）。

多层LSM树一个有趣的属性是每一层都可以起着写缓存作用，无论什么时候，当某个key被更新时，这个key将被插入到低层文件里。它将会经历多次提升才能被存储到更高层的文件里。这就意味着，查找一个刚被更新的key时，只需要扫描很少层数的文件或者读取少量索引就能检索到对应的数据。因为低层文件数量很少，索引量也很少。

## 7. 参考资料

[[1]](http://www.benstopford.com/2015/02/14/log-structured-merge-trees/) ben stopford – Log Structured Merge Trees

[[2]](https://www.datastax.com/dev/blog/leveled-compaction-in-apache-cassandra) Datastax – Leveled Compaction in Apache Cassandra

[[3]](https://queue.acm.org/detail.cfm?id=1563874) ACM Queue – The Pathologies of Big Data

[[4]](https://github.com/google/leveldb/blob/master/doc/impl.md) LevelDB – Wiki

[[5]](https://kunigami.blog/2017/07/08/eliminating-amortization/) NP-Incompleteness – Eliminating Amortization

---
layout: post
title: MapReduce:大数据并行计算框架
date: 2018-05-13 22:57:00.000000000 +09:00
tags: mapreduce 并行计算 大数据 6.824
---

# MapReduce介绍

**Note. 本文参考至[官方MapReduce论文](https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf),结合MIT开设的[6.824](https://pdos.csail.mit.edu/6.824/index.html)这门与分布式系统相关的课程实验，对MapReduce框架的原理和细节作介绍。希望能帮助读者了解MapReduce框架以及相关细节，以便编写基于MapReuce框架的代码。**

-  [0. 摘要](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#0-%E6%91%98%E8%A6%81)

-  [1. MapReduce介绍](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#1-mapreduce%E4%BB%8B%E7%BB%8D)

-  [2. MapReduce编程模型](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#2-mapreduce%E7%BC%96%E7%A8%8B%E6%A8%A1%E5%9E%8B)

    - [2.1 例子](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#21-%E4%BE%8B%E5%AD%90)
    
    - [2.2 类型](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#22-%E7%B1%BB%E5%9E%8B)
    
    - [2.3 更多MapReduce例子](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#23-%E6%9B%B4%E5%A4%9Amapreduce%E4%BE%8B%E5%AD%90)
    
- [3. MapReduce实现](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#3-mapreduce%E5%AE%9E%E7%8E%B0)

    - [3.1 MapReduce整体执行过程](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#31-mapreduce%E6%95%B4%E4%BD%93%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B)
    
    - [3.2 Master数据结构](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#32-master%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84)
    
    - [3.3 MapReduce容错机制](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#33-mapreduce%E5%AE%B9%E9%94%99%E6%9C%BA%E5%88%B6)
    
    - [3.4 MapReduce输入的局部性特点](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#34-mapreduce%E8%BE%93%E5%85%A5%E7%9A%84%E5%B1%80%E9%83%A8%E6%80%A7%E7%89%B9%E7%82%B9)
    
    - [3.5 Map任务和Reduce任务粒度](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#35-map%E4%BB%BB%E5%8A%A1%E5%92%8Creduce%E4%BB%BB%E5%8A%A1%E7%B2%92%E5%BA%A6)
    
    - [3.6 备份任务](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#36-%E5%A4%87%E4%BB%BD%E4%BB%BB%E5%8A%A1)
    
- [4. 相关改进](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#4-%E7%9B%B8%E5%85%B3%E6%94%B9%E8%BF%9B)

    - [4.1 Partitioning函数](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#41-partitioning%E5%87%BD%E6%95%B0)
    
    - [4.2 中间结果有序性](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#42-%E4%B8%AD%E9%97%B4%E7%BB%93%E6%9E%9C%E6%9C%89%E5%BA%8F%E6%80%A7)
    
    - [4.3 Combiner函数](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#43-combiner%E5%87%BD%E6%95%B0)
    
    - [4.4 MapReduce的输入输出类型](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#44-mapreduce%E7%9A%84%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA%E7%B1%BB%E5%9E%8B)
    
    - [4.5 副作用](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#45-%E5%89%AF%E4%BD%9C%E7%94%A8)
    
    - [4.6 忽略错误结果](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#46-%E5%BF%BD%E7%95%A5%E9%94%99%E8%AF%AF%E7%BB%93%E6%9E%9C)
    
    - [4.7 调试模式：本地执行](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#47-%E8%B0%83%E8%AF%95%E6%A8%A1%E5%BC%8F%E6%9C%AC%E5%9C%B0%E6%89%A7%E8%A1%8C)
    
    - [4.8 状态信息](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#48-%E7%8A%B6%E6%80%81%E4%BF%A1%E6%81%AF)
    
    - [4.9 数据统计](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-15-MapReduce:%E5%A4%A7%E6%95%B0%E6%8D%AE%E5%B9%B6%E8%A1%8C%E8%AE%A1%E7%AE%97%E6%A1%86%E6%9E%B6.md#49-%E6%95%B0%E6%8D%AE%E7%BB%9F%E8%AE%A1)
    
## 0. 摘要

[MapReduce论文](https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf)是google引爆大数据时代的3篇论文中的一篇，另外2篇是GFS、BigTable。这篇论文介绍了一个通用的并行计算框架，充分利用分布式系统下机器的并行计算能力。在这框架下，用户定义一个*map*函数，这个函数能够处理一个key/value对，并输出若干中间格式key/value对；用户再定义一个*reduce*函数，这个函数能够把相同的中间键对应的值合并起来。很多现实环境的任务都可以使用这个框架进行处理，后面将详细介绍这个模型。

这个框架的原理就是把一个大任务划分成若干小任务，然后在多台机器上同时执行这些若干小任务，再通过汇总若干小任务的中间输出结果，生成最终结果。下面图1是框架原理图：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/mapreduce%E6%A8%A1%E5%9E%8B.png?raw=true">	
</div>

<p align="center">
  <b>图 1 MapReduce框架原理图</b><br>
</p>

这个框架看起来好像很简单，相信很多程序员也能写出来，但是MapReduce有很多细节需要处理。比方说，怎么把任务的输入分成若干份，在每台机器上调度运行若干小任务去处理输入呢？每台机器的中间输出又怎么汇总呢？要是整个集群有部分机器运行中宕机了怎么保证最终结果的正确性？Map任务和Reduce任务的数量怎么选择才能使得整个执行过程效率高？带着这些问题，下面将开始介绍MapReduce的一些细节。

## 1. MapReduce介绍

在Google发表MapReduce论文前，内部已经有很多大规模数据处理框架，比如网页爬虫、web请求日志等任务需要计算倒排索引、网页的图拓扑结构、每个主页包含的网页数量、某天最常出现的搜索词等。这些任务大部分都很直观。但是这些任务的输入通常非常大，需要把这些任务分配到数百或数千台机器上分别执行，以便能够在一个合理时间内完成任务。

MapReduce主要的贡献是提出了一个简单但是很强大的接口，支持自动并行化和大规模计算。第2节描述基本编程模型和提供几个示例。第3节描述了一个实现MapReduce接口的量身定制我们的基于集群的计算环境。第4节介绍编程模型的几个改进。

## 2. MapReduce编程模型

MapReduce输入是一组key/value对，并产生一组key/value对。MapReduce库把任务表示成两个函数：*Map*和*Reduce*。

*Map*函数由用户提供，该函数输入为一个key/value对并产生一组*中间格式key/value对*。MapReduce库会把具有相同中间格式的key *I*对应的值合并起来，并把合并的结果传递给*Reduce*函数。

*Reduce*函数也是由用户提供，该函数输入为某个中间格式key *I*以及这个*I*对应的一组value。这个函数会把对应的一组value合并成一个更小集合的value。通常情况下每个*Reduce*函数会输出0个或1个value。中间格式的value会通过一个迭代器iterator提供给用户的reduce函数来处理。这使我们能够处理value集合太大以致无法放到内存里面去的场景。

### 2.1 例子

下面我们将通过介绍一些能使用MapReduce的例子进一步说明这个框架的实用性。现在考虑下有这么一种场景，需要计算出成千上万个文档里面每个单词所出现的次数。如果使用MapReduce框架，用户只需要编写以下类似的伪代码就能够并行计算：

```
map(String key, String value):
    // key: document name
    // value: document contents
    for each word w in value:
        EmitIntermediate(w, "1");
        
reduce(String key, Iterator values):
   // key: a word
   // values: a list of counts
   int result = 0;
   for each v in values:
      result += ParseInt(v);
   Emit(AsString(result));
```

**map**函数输出每个单词以及单词出现的次数（在这个例子中次数为‘1’）。**reduce**函数把特定单词的所有计算结果汇总在一起。

另外，用户编写代码来填充mapreduce规范对象与输入和输出的名称文件和可选的调整参数，用户然后调用MapReduce函数。

### 2.2 类型

尽管前面的伪代码的输入和输出都是string类型，但是概念上来看用户提供的map和reduce函数具有以下关系：

```
    map    (k1,v1)         -> list(k2,v2)
    reduce (k2,list(v2))   -> list(v2)
```

map函数接收的输入的key类型为k1，value类型为v1，输出一组key类型为k2，value类型为v2的值。而reduce函数接收的输入为map函数输出的key类型为k2，value为k2所关联的一组类型为v2的值，而输出一组类型为v2的值。

虽然接口提供的输入、输出都是字符串类型，但是用户可以在代码中把字符串转换为自定义类型。

### 2.3 更多MapReduce例子

以下是一些有趣的程序，这些程序能够很容易地使用MapReduce进行并行计算，如果我们生产环境的程序也跟下面的例子相似的话，可以考虑使用MapReduce减少程序运行时间。

-  **分布式Grep：** linux的grep命令大家都不陌生，如果输入文件的某一行匹配到一个pattern串的时候，就输出整行。map函数在处理输入过程中，每行匹配到pattern串时，会输出一行。reduce函数仅仅把中间格式数据拷贝到最终输出。

-  **统计所有URL的访问频率：** map函数处理网页请求日志并输出<URL,1>格式的值。reduce函数对相同的URL对应的所有值进行相加，输出<URL,total count>对。

-  **反向网页链接图：** map函数输出<target,source>对，其中每个网页称作source，而每个source页面里的链接称作target。reduce函数对一个特定target链接计算出对应的所有source链接并输出<target,list(source)>对。

-  **倒排索引：** map函数解析每个页面，并输出一系列<word,document ID>对。reduce函数接收一个特定单词的所有document ID值，并根据document ID值进行排序后输出<word,list(document ID)>对。所有输出就组成了一个简单的倒排索引，用户通过这个倒排索引就能直接找出所有出现过特定单词的网页。也很容易对map函数进行增强，能够进一步记录单词在页面所出现的位置。

-  **分布式排序：** map函数从每条记录里提取key值，并输出<key,record>对。reduce函数原封不动地输出所有对。这个计算会依赖于4.1节所描述的partitioning函数，该函数会对map的输出做初步的合并；另外还依赖于4.2节描述的有序性属性。

## 3. MapReduce实现

MapReduce接口有很多种实现。该选择怎么的实现取决于执行环境。比如，一种实现可能适合与一台共享内存的小机器，而另外一种适合多核处理器的机器，还有另外一种适合相互连接的机器集群。本节将集合6.824课程的实验和google内广泛使用的集群环境来描述MapReduce的实现。

### 3.1 MapReduce整体执行过程

**Map**函数会在不同机器上被调用，自动把输入的数据分成**M**份。而被分割后**M**份数据能够在不同的机器上并行处理。**Reduce**函数会=也会在不同机器上被调用，基于partitioning函数（如：*hash(key)* **mod** *R*）把中间格式的key/value对划分为**R**份。partitions(R)的数量和partitioning函数由用户指定。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/MRExecution.png?raw=true">	
</div>

<p align="center">
  <b>图 2 MapReduce的整体执行流程</b><br>
</p>

图2描述了MapReduce的整体执行流程。当用户程序调用MapReduce函数时，将会执行下面的一系列步骤（图2标记的数字与下面步骤的数字一致）。

  1  MapReduce库连同用户程序，首先把输入文件划分为*M*块，通常每块大小为16MB到64MB（具体大小可以由用户提供可选参数决定）。然后在一个集群内同时运行多份程序的副本。

  2  在多份程序副本里面其中有一份比较特别：master。其余都是worker，由master分配。一共有**M**个map任务和**R**个reduce任务需要进行分配。master会选择空闲的worker进行分配，每次分配一个map任务或者reduce任务。master和worker可以是在不同机器上运行的进程，刚开始的时候用户在不同机器上启动1个master和若干worker，而master把任务分配给worker可以使用rpc方式。6.824课程的示例代码如下(基于go语言实现)：

```
go func() {         // 通过启动一个gorotuine进行任务分配
		for {
			failedTask := <-failedTaskChan
			select {
			case newWorkerAdd := <-registerChan: // 当有的worker加入时，直接分配map或reduce任务
				go func(srv string, rpcname string,
					args interface{}) {
					succ := call(srv, rpcname, args, nil) // 通过rpc方式调用worker执行任务
					if succ {
						wg.Done() // 通过waitgroup来控制master是否返回，当所有worker结束时，master会返回，把控制权交回给用户程序
					} else { // failed
						failedTaskChan <- args  // 如果失败，把任务加入到错误channel，等待下一次调度
					}
					existWorkerChan <- newWorkerAdd
				}(newWorkerAdd, "Worker.DoTask", failedTask)
			case existWorkerAdd := <-existWorkerChan: // 当之前分配过任务的worker已经完成任务时，也可以继续分配新的map或reduce任务
				go func(srv string, rpcname string,
					args interface{}) {
					succ := call(srv, rpcname, args, nil)
					if succ {
						wg.Done()
					} else { // failed
						failedTaskChan <- args  // 如果失败，把任务加入到错误channel，等待下一次调度
					}
					existWorkerChan <- existWorkerAdd
				}(existWorkerAdd, "Worker.DoTask", failedTask)
			}
		}
	}()
```

  3  一个worker如果被分配了map任务，这个worker会从分割后的输入读取相关内容。接着把读取到的内容解析为key/value对，并把key/value对传入给用户所定义的*Map*函数。*Map*函数产生的中间格式key/value对会缓存在内存里。同样，示例代码如下：

```
func doMap(
	jobName string, // the name of the MapReduce job
	mapTask int,    // which map task this is
	inFile string,
	nReduce int, // the number of reduce task that will be run ("R" in the paper)
	mapF func(filename string, contents string) []KeyValue,
) {
	bytes, err := ioutil.ReadFile(inFile) // 从分割后的输入读取相关内容
	if err != nil {
		panic(err)
	}

	kvs := mapF(inFile, string(bytes)) // 把内容传入到用户自定义map函数，生成key/value对

	nameFilesMap := make(map[string]*os.File)
	for _, kv := range kvs {
		r := ihash(kv.Key) % nReduce
		reduceFileName := reduceName(jobName, mapTask, r)
		if _, ok := nameFilesMap[reduceFileName]; !ok {
			file, err := os.OpenFile(reduceFileName, os.O_CREATE|os.O_WRONLY, 0755)
			if err != nil {
				panic(err)
			}
			nameFilesMap[reduceFileName] = file
		}
		enc := json.NewEncoder(nameFilesMap[reduceFileName])
		err := enc.Encode(&kv) // 把中间格式的key/value对序列化到本地磁盘
		if err != nil {
			panic(err)
		}
	}

	for _, file := range nameFilesMap {
		file.Close()
	}
}
```

  4  缓存在内存里的key/value对会定期写入到本地磁盘中，如上面示例代码所示。可选地，这些key/value会使用用户提供的paritioning函数先分成R份，上述使用默认的函数（`hash(key)% R`）。这些对在本地的位置信息会返回给master，master再把位置信息推送给reduce worker。

  5  当master把中间格式的key/value对推送给reduce worker时，reduce worker会使用rpc方式从map worker本地磁盘读取数据。当reduce worker读取完本地数据时，会把中间格式的key/value对按key大小进行排序，以便对同一个key的value进行分组一起。排序是必要的，因为通常情况下不同的key会映射到相同的reduce任务。如果中间格式的数据太大无法在内存中进行排序（如快排、堆排等），通常需要外部排序（外部归并）。示例代码如下：

```
func doReduce(
	jobName string, // the name of the whole MapReduce job
	reduceTask int, // which reduce task this is
	outFile string, // write the output here
	nMap int,       // the number of map tasks that were run ("M" in the paper)
	reduceF func(key string, values []string) string,
) {
	keyValuesMap := make(map[string][]string)
	keys := make([]string, 0)
	for m := 0; m < nMap; m++ {
		reduceFileName := reduceName(jobName, m, reduceTask)
		file, err := os.Open(reduceFileName) // 课程实验是从本地读取中间格式数据，一般实现时会通过rpc方式读取
		if err != nil {
			panic(err)
		}

		dec := json.NewDecoder(file)
		for {
			var kv KeyValue
			if err := dec.Decode(&kv); err != nil { // 从文件解析出中间格式key/value对
				break
			}
			if _, ok := keyValuesMap[kv.Key]; !ok {
				keyValuesMap[kv.Key] = make([]string, 0)
				keys = append(keys, kv.Key)
			}
			keyValuesMap[kv.Key] = append(keyValuesMap[kv.Key], kv.Value) // 按key对value进行分组，同一个key的value会组合在一起
		}

		file.Close()
	}

	sort.Sort(StringSlice(keys)) // 按中间格式的key进行排序，如果内存放不下，会使用外部排序

	reduceFile, err := os.OpenFile(outFile, os.O_CREATE|os.O_WRONLY, 0755)
	defer reduceFile.Close()
	if err != nil {
		panic(err)
	}
	enc := json.NewEncoder(reduceFile)

	for _, key := range keys {
		enc.Encode(KeyValue{key, reduceF(key, keyValuesMap[key])}) // 把reduce结果写入到本地
	}
}
```

  6  reduce worker会遍历有序的中间格式数据，把每个唯一的key和key关联的所有中间格式value传入到用户定义的*reduce*函数。*Reduce*函数的输出最后会输出到指定的位置（上述代码的outFile）。
  
  7  当所有map和reduce任务执行完毕，master会唤醒用户程序，整个程序会返回到用户程序那继续执行。本实验使用waitgroup的方式来控制整个程序返回到用户程序的时机。
  
MapReduce程序成功执行完毕后，最终的输出会分布在*R*份文件里（每个reduce任务对应一个文件，文件名由用户指定）。通常，用户不需要把这R份文件合并为一个文件-他们通常会将这些文件作为输入传递给另一个MapReduce调用，或从另一个分布式中使用它们能够处理输入的应用程序分成多个文件。

### 3.2 Master数据结构

master会维护多份数据结构。对每个map和reduce任务，master会存储这些任务的状态（idle、in-progress、completed），还会存储这些worker机器（非idle状态）的id。

### 3.3 MapReduce容错机制

由于MapReduce库旨在使用成百上千的机器并行处理大数据，而大规模的机器出现故障的概率会非常高，所以库必须能够优雅地处理机器故障。

#### Worker错误

master会定期向worker发起ping。如果一段时间内master没有收到某个worker对自己ping请求的响应时，就会把worker标记为错误状态。所有被这个worker处理的map任务将重置为*idle*状态，因此这些失败任务能够被重新调度，被其他正常的worker再次执行。同样，在错误状态的worker上执行的任务map任务或者reduce任务将重置为*idle*状态，并被重新调度。

在故障机器上运行完毕的map任务会被重新执行，因为它们的输出存储在故障机器的本地磁盘上，因此其他正常运作的机器无法访问。同样在故障机器上已经运行完毕的reduce则不需要重新执行，因为reduce的输出存储在全局文件系统中。

当一个map任务刚开始由worker A执行，在A发生故障后由worker B执行，这种情况下所有执行reduce任务的worker都会收到map重新执行的通知。所有还没从worker A读取数据的reduce任务将会从worker B读取。

#### Master错误

很容易把上述的master的数据结构的定期检查点存储起来。如果master任务运行出错，可以从最新的定期检查点恢复构建出新的master任务副本。然而，考虑到只有一份master任务，运行过程中几乎很少几率出现错误，因此比较简单的实现方式是当有master失败时，直接放弃本次MapReduce操作。客户端可以检查这种情况，如果愿意的话，可以重试MapReduce操作。

### 3.4 MapReduce输入的局部性特点

网络带宽是相对稀缺的资源计算环境。通过利用输入数据（由GFS管理）存储在集群机器的本地磁盘这个特点，能够节省整个集群的网络带宽。GFS把每个文件分割成若干64MB大小的块，每一块都会在不同机器上存储几份副本（通常3份）以此来容错。MapReduce的master任务会考虑到输入数据的位置信息，尝试把map任务运行在包含这些输入数据副本的机器上。如果调度失败，再尝试把map任务运行在离包含这些输入较近的机器。

### 3.5 Map任务和Reduce任务粒度

如上所述，我们把map阶段分成*M*个部分，把reduce阶段分成*R*个部分。理想情况下，*M*和*R*应该比worker数量大得多。让每个worker运行不同类型的任务能够提高动态负载均衡，同时能够加速故障恢复速度：很多map任务可以分散在不同的机器上去完成。

*M*和*R*在实现过程中有实际的界限，由于master必须做出`O(M + R)`次调度决策并在内存存储上述`O(M * R)`个状态。`O(M * R)`个状态存储空间很小，大概每个map、reduce任务的状态只需要一个字节的存储空间。

另外，*R*通常收到用户的限制，因为每个reduce任务的输出结束语一个单独的输出文件。实际中，我们在选择*M*的大小时，倾向于让每个任务大概能够处理16MB到64MB的数据。同时让*R*大致为worker机器数量的若干倍。比如，有2000台worker机器时，一般让`M = 200,000`，而`R = 5,000`。

### 3.6 备份任务

导致MapReduce任务执行时间较长的常见原因之一是出现“straggler”机器：某些机器会花费异常长的时间去完成整个过程中最后的几个map或者reduce任务。出现Straggler的原因有很多种。例如，一台机器的磁盘有问题，可能会频繁出现可纠正的错误，降低其读取性能，从30MB/s到1MB/s。

## 4. 相关改进

虽然简单提供的基本功能，编写*Map*和*Reduce*函数对于大多数人来说已经足够满足需求，但是下面会介绍一些有用的扩展。

### 4.1 Partitioning函数

MapReduce用户可以指定reduce任务/输出文件的数量为R。用户的数据会基于中间格式的key然后散布于这些R个任务中。框架会提供一个默认的partitioning函数（比如，`hash(key) mode R`）。这往往导致相当均衡的分布。但是，在某些情况下，使用其他的partitioning函数进行分区可能更有用。比如，有时候输出key是URL，我们希望所有同属于一个host的URL输出到相同文件中。为了支持这类情况，MapReduce库的用户可以提供一个特定的partitioning函数。比方说，使用`"hash(Hostname(urlkey)) mod R"`作为partitioning函数，能够把所有同属于一个host的URL输出到相同文件中。

### 4.2 中间结果有序性

实现过程中保证在特定的分区内，中间格式的key/value对按升序方式进行处理。这种有序性保证可以很容易地为每个分区生成一个有序的输出文件。每个分区的输出如果是有序的话，那么根据key来进行随机访问的效率会很高（比如使用二分查找，很快就能找出key对应的值）。

### 4.3 Combiner函数

在某些情况下，每个map任务可能产生大量的重复中间格式key，并且用户定义的*Reduce*函数是可交换和关联的。第2.1节描述的统计单词出现次数的例子就很好地说明了这种情况。由于单词频率服从Zipf分布，每个map任务会产生成千上万条格式为<the, 1>的数据。这些数据会跨越整个网络最后被发送给某个reduce任务，再由*Reduce*函数把次数累加起来并输出最终结果。框架允许用户定义一个可选的*Combiner*函数，使得数据在发送给reduce任务前先做部分合并。

*Combiner*函数由执行map任务的机器执行。通常情况下，combiner和reduce函数的代码是一样的。reduce函数和combiner函数的唯一区别是MapReduce库怎么处理函数的输出。reduce函数的输出会写入到最终文件，而combiner函数的输出会写入到中间文件，然后再通过网络传输给reduce任务。

部分合并能够显著加快整个MapReduce过程。

### 4.4 MapReduce的输入输出类型

MapReduce库支持以不同的格式读取输入文件。比如，"文本"模式的输入把每一行当作一个key/value对。key是某一行在文件的offset，而value是某一行的内容。另一种常用的支持格式存储按key排序的key/value对。在使用MapReduce框架时，每种输入类型都需要知道如何将自身分解为有意义的处理范围作为单独的map任务（比如，文本模式的范围拆分可确保发生范围拆分只在行边界上）。用户只要实现一个*reader*接口，告诉框架如何从输入文件进行分割，就可以添加新的输入类型。不过大多数用户都会使用一小部分预定义的输入类型中的某一种。

*reader*不一定需要从文件获取输入数据。比如，很容易指定*reader*从数据库里或者从内存里读取数据。

类似地，MapReduce框架支持以不同格式产生输出，用户也很容易添加新的输出类型。

### 4.5 副作用

在某些情况下，MapReduce的用户发现从map或者reduce任务产生的额外输出作为辅助文件会非常便利。但要注意map和reduce任务在并发读写文件时，需要保证读写操作的原子性。

### 4.6 忽略错误结果

MapReduce框架支持忽略某些数据的执行模型。

### 4.7 调试模式：本地执行

在分布式系统下调试*Map*或*Reduce*函数可能很棘手，因为master会在数千台机器上动态调度任务，每个任务所运行的机器是不固定的。为了方便调试、分析和小规模测试，框架提供了一种所有worker在本地按顺序执行的模式。用户可以指定某些map任务使用本地执行，这样用户就可以在本地使用调试和测试工具了。（比如：gdb）

### 4.8 状态信息

master运行一个内部HTTP服务器并到处一组可读的状态页面。状态页面显示计算的进度和有多少任务已完成，输入数据、中间格式数据、处理速度等等。这些页面还包含链接到生成的标准错误和标准输出文件。用户可以使用这些数据进行预测计算需要多长时间，以及在计算中是否要增加更多的资源。另外，顶级状态页面显示哪些worker失败了，哪些map任务和reduce任务失败。这些信息在诊断用户代码的bug的时候非常有用。

### 4.9 数据统计

MapReduce库提供了一个统计工具用于统计各种事件发生的次数。例如，用户想统计已处理单词的总数或是索引的德文文件的数量等等。

为了能够使用统计功能，需要在用户代码中创建一个counter对象，并在*Map*或*Reduce*函数中相应地增加counter。示例代码如下：

```
Counter* uppercase;  // 创建counter对象，用于统计大写开头的单词数量
uppercase = GetCounter("uppercase");
	map(String name, String contents): 
		for each word w in contents: 
		if (IsCapitalized(w)): // 在map函数里对每个单词进行判定，如果如大写，则Counter自增一
			uppercase->Increment();
	EmitIntermediate(w, "1");
```

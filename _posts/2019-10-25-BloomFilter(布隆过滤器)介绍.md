---
layout: post
title: BloomFilter(布隆过滤器)介绍
date: 2019-10-25 17:37:00.000000000 +09:00
tags: BloomFilter bitmap 布隆过滤器 位图 数据结构
---


# BloomFilter(布隆过滤器)介绍

**Note. 本篇介绍一种存储结构-布隆过滤器。这种存储结构类似于散列表，能够存储和查询元素是否存在，并且存储效率一般要比散列表高很多。应用场景有，比如爬虫应用会应用它来进行URL去重，避免重复爬取相同网页。在区块链领域方面，以太坊把Bloom Filter应用到数量庞大的交易receipt日志里，能够快速查找log。在介绍布隆过滤器之前，再介绍一种数据结构-位图。本质上，布隆过滤器是一种改进后的位图，存储效率更高。**

- [1.有1000万个整数，范围在1～1亿之间，如何快速判断某个整数是否存在？](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-10-25-BloomFilter(%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8)%E4%BB%8B%E7%BB%8D.md#1-%E6%9C%891000%E4%B8%87%E4%B8%AA%E6%95%B4%E6%95%B0%E8%8C%83%E5%9B%B4%E5%9C%A811%E4%BA%BF%E4%B9%8B%E9%97%B4%E5%A6%82%E4%BD%95%E5%BF%AB%E9%80%9F%E5%88%A4%E6%96%AD%E6%9F%90%E4%B8%AA%E6%95%B4%E6%95%B0%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8)

- [2. 位图](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-10-25-BloomFilter(%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8)%E4%BB%8B%E7%BB%8D.md#2-%E4%BD%8D%E5%9B%BE)

- [3. 布隆过滤器](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-10-25-BloomFilter(%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8)%E4%BB%8B%E7%BB%8D.md#3-%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8)

- [4. 参考资料](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-10-25-BloomFilter(%E5%B8%83%E9%9A%86%E8%BF%87%E6%BB%A4%E5%99%A8)%E4%BB%8B%E7%BB%8D.md#4-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)


## 1. 有1000万个整数，范围在1～1亿之间，如何快速判断某个整数是否存在？

在介绍位图之前，我们先看一个问题：**假设有1千万个整数，整数范围在1到1亿之间。如何快速查找某个整数是否在这1千万个整数中呢？**

我们可能马上想到用散列表来解决，并且散列表是可以动态扩容的。散列表的每个元素所占用的空间至少为27bit，取整后为4个字节（100,000,000至少需要27位，2^26为6700万，存放不下1亿），那么1千万个整数，就需要4000万字节，大概38MB多。如果整数数量不止1千万个，假设是8千万个，那么需要的内存空间又增长至8倍，304MB多，所占用的内存空间与整数数量呈正比例增长。

当然我们可以采用分治的思想，用多台机器来分开存储这1千万个整数。比如用8台机器，每台机器分别存储某个区间段的整数。

## 2. 位图

其实位图是一种特殊的“特殊”的散列表，对于上面那个问题，我们申请一个大小为1亿bit大小的“数组”，只要整数存在，就将这个数组对应下标的比特位置为1。但是一般的编程语言中，是没有比特这种类型的。**那如何在程序中，表示一个二进制位呢？**

利用位运算，我们可以借助编程语言中提供的数据类型如byte、int、long、char等类型，用其中某个位表示某个数字。对应的代码如下：

```
type BitMap struct {
	bytes []byte
	nbits int
}

func MakeBitMap(n int) *BitMap {
	res := new(BitMap)
	res.nbits = n
	res.bytes = make([]byte, n/8+1)
	return res
}

func (b *BitMap) Set(k int) {
	if k > b.nbits {
		return
	}
	bytesIdx := k / 8
	bitIdx := uint8(k % 8)
	b.bytes[bytesIdx] |= 1 << bitIdx
}

func (b *BitMap) Get(k int) bool {
	if k > b.nbits {
		return false
	}
	bytesIdx := k / 8
	bitIdx := uint8(k % 8)
	return (b.bytes[bytesIdx] & (1 << bitIdx)) != 0
}
```

结合代码来看，可以发现位图通过数组下标来定位数据，因为查询效率很高。每个数字只用一个二进制位来表示，在数字范围不大的情况下，所需要的内存空间很小。

回到刚才那个例子，1千万的数据，用散列表需要至少38MB。用位图的话，只需要1亿个bit，即11.92MB左右。另外，如果数据量从1千万提升到8千万，还是11.92MB。同样情况下，散列表需要304MB，大大减少了耗费的内存空间。

目前看起来位图存储效率很高，但是有一个前提条件，整数的范围不是很大，如上面的例子是1～1亿。如果是1到10亿，那位图的大小就是10亿个二进制位，就需要119.2MB的内存空间，相比散列表的38MB，反而占用更多的内存空间。这时候就要引入布隆过滤器，一种可以看作是进阶版位图的数据结构。

## 3. 布隆过滤器

还是有1千万个整数，数据范围为1～10亿。布隆过滤器的处理方式是，仍然使用一个1亿个bit大小的位图，再通过哈希函数，对整数进行处理，让它落在1～1亿内。比如可以使用f(x)=x%n，其中x表示整数，n表示位图的大小（1亿），即取模求余。或者使用Fnv、Murmur这两种计算速度很快并且随机性比较好的散列函数。关于散列函数个数，该选择什么类型的散列函数，这篇博客**Bloom Filters by Example**[1]里有更详细的介绍。

但是，我们肯定会反驳，散列函数会有冲突，比如1和一亿零一，通过f(x)=x%n之后都是1，位图存储的都是1，那怎么知道是1还是一亿零一。

为了减少冲突概率，布隆过滤器的做法是选择多个散列函数一起来定位一个数据。因为多个独立的散列函数同时发生冲突的概率，是相当低的。

使用K个散列函数，对同一个整数求散列值，得到K个不同的散列值，分别记作X<sub>1</sub>,X<sub>2</sub>,X<sub>3</sub>,...,X<sub>K</sub>。将这K个数字作为位图的下标，将对应的BitMap[X<sub>1</sub>],BitMap[X<sub>2</sub>],BitMap[X<sub>3</sub>],...,BitMap[X<sub>K</sub>]都设置为1。即用K个bit来表示一个整数是否存在。

当要查询某个整数是否存在的时候，用同样的K个散列函数，对该数字求散列值，分别得到Y[<sub>1</sub>],Y[<sub>2</sub>],Y[<sub>3</sub>],...,Y[<sub>K</sub>]。如果这个K个散列值，对应位图中的值都为1，则表示这个数字**可能存在**；如果有任意一个不为1，那就说明数字**真的不存在**，布隆过滤器对存在的情况可能存在误报，下面举例说明。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/bloom_filter/true_negative.jpg?raw=true" height="60%" width="60%">	
</div>

<p align="center">
  <b>图 1 整数不存在的情况，布隆过滤器保证肯定不存在</b><br>
</p>

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/bloom_filter/false_positive.jpg?raw=true" height="60%" width="60%">	
</div>

<p align="center">
  <b>图 2 布隆过滤器误判整数存在的情况</b><br>
</p>

Bloom Filter的大致结构如下：一个位图+多个散列函数，主要是要根据数据量和数据的范围来选取适当的散列函数数量以及散列函数、位图的大小。

```
type BloomFilter struct {
	hashFuncs []func(n int) uint
	bytes     []byte
	nbits     int
}

func MakeBloomFilter(funcs []func(n int) uint, nbits int) *BloomFilter {
	res := new(BloomFilter)
	res.hashFuncs = funcs
	res.nbits = nbits
	res.bytes = make([]byte, nbits/8+1)
	return res
}

func (b *BloomFilter) Set(k int) {
	for _, fun := range b.hashFuncs {
		idx := fun(k)
		bytesIdx := idx / 8
		bitIdx := uint8(idx % 8)
		b.bytes[bytesIdx] |= 1 << bitIdx
	}
}

func (b *BloomFilter) Get(k int) bool {
	res := true

	for _, fun := range b.hashFuncs {
		idx := fun(k)
		bytesIdx := idx / 8
		bitIdx := uint8(idx % 8)
		if (b.bytes[bytesIdx] & (1 << bitIdx)) == 0 {
			return false
		}
	}

	return res
}

func main() {
	nbits := 1000
	b := MakeBloomFilter([]func(n int) uint{
		func(n int) uint {
			return uint(n % nbits)
		},
		func(n int) uint {
			return uint(n%nbits) + 1
		},
	}, nbits)
	fmt.Printf("%+v\n", b.Get(10))
	b.Set(10)
	fmt.Printf("%+v\n", b.Get(10))
}
```

到现在为止，布隆过滤器还是一种存储效率很高的数据结构，可能会存在误判，但这不影响到它应用的广泛性。非常适合那种不需要100%精确的，允许存在小概率误判的大规模判重场合，比如说统计某个网站的用户日活跃量，对重复访问的用户去重，就算是误判某些用户存在，误判率不高的情况下（1000万和1001万这样的区别）还是能比较准确地统计出用户数量的。

最后，希望这篇文章能给大家带来一些启发，在日常开发中如果遇到大规模判重场景，可以考虑是否能应用布隆过滤器来满足需求。

## 4. 参考资料

[[1]](https://llimllib.github.io/bloomfilter-tutorial/) Bloom Filters by Example

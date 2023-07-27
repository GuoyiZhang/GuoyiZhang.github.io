---
layout: post
title: LEB128(Little-Endian Base 128)格式介绍
date: 2019-09-01 23:11:00.000000000 +09:00
tags: LEB128 变长编码 大小端
---


# LEB128(Little-Endian Base 128)格式介绍

**Note. 本篇介绍Andorid系统在Dex文件采用LEB128变长编码格式，相对固定长度的编码格式，leb128编码存储利用率比较高，能让Dex文件尽可能的小。对于存储空间比较紧缺的移动设备，这非常有用。其中LEB128可以分为无符号(ULEB128)、有符号整数编码(SLEB128)，其中还包括一种特殊的无符号整数编码(ULEB128p1 unsigned LEB128 plus 1)。下面将分别具体介绍，并给出相关的编码、解码代码，在区块链领域内的应用场景有智能合约编码，希望能带来启发。**

- [1. 大小端表示法](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-09-01-LEB128(Little-Endian%20Base%20128)%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.md#1-%E5%A4%A7%E5%B0%8F%E7%AB%AF%E8%A1%A8%E7%A4%BA%E6%B3%95)

- [2. ULEB128(unsigned LEB128，无符号整数编码)](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-09-01-LEB128(Little-Endian%20Base%20128)%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.md#2-uleb128unsigned-leb128%E6%97%A0%E7%AC%A6%E5%8F%B7%E6%95%B4%E6%95%B0%E7%BC%96%E7%A0%81)

- [3. SLEB128(signed LEB128，有符号整数编码)](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-09-01-LEB128(Little-Endian%20Base%20128)%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.md#3-sleb128signed-leb128%E6%9C%89%E7%AC%A6%E5%8F%B7%E6%95%B4%E6%95%B0%E7%BC%96%E7%A0%81)

- [4. ULEB128p1(unsigned LEB128 plus 1，特殊无符号整数编码)](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-09-01-LEB128(Little-Endian%20Base%20128)%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.md#4-uleb128p1unsigned-leb128-plus-1%E7%89%B9%E6%AE%8A%E6%97%A0%E7%AC%A6%E5%8F%B7%E6%95%B4%E6%95%B0%E7%BC%96%E7%A0%81)

- [5. 参考资料](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2019-09-01-LEB128(Little-Endian%20Base%20128)%E6%A0%BC%E5%BC%8F%E4%BB%8B%E7%BB%8D.md#5-%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)




## 1. 大小端表示法

在介绍LEB128编码前，先回忆下小端表示法。在计算机里，数据一般以字节为单位存储，如果任何数据都能用一个字节来表示的话，就没有大小端什么事了。但是现实中很多数据需要多个字节来表示（一个字节能表示最大的整数也就是127，像128至少要2个字节），这就会涉及到字节的存放先后顺序问题。比如说4个字节长度的一个十六进制的无符号整数：```0x12 34 56 78```，使用大、小端两种表示方法的内存布局示意图如图1所示：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-09-01/%E5%A4%A7%E7%AB%AF%E5%B0%8F%E7%AB%AF%E6%B3%95%E5%86%85%E5%AD%98%E5%B8%83%E5%B1%80.png?raw=true" >	
</div>


<p align="center">
  <b>图 1 0x12345678 大小端表示法内存布局示意图</b><br>
</p>

0x12 34 56 78这4个字节里，34相对于56来说，是更高位的字节，而56相对于78来说又是更高位的字节，字节的高低只是相对的。

如果使用大端法存储，那么最高位字节12就会存放在低地址0x100，然后依次是34存放在0x101，56存放在0x102，78存放在0x103,也就是以0x12 34 56 78的顺序存放。这种表示法的优点是与人类思维一致，但缺点是计算机处理起来不是很方便。

而小端法与大端法相反，最高位字节12存放在高地址0x103，而最低位地址存放在低地址0x100，也就是以0x78 56 34 12的方式顺序存放。可以看到，字节的顺序有点不符合人类的直觉，看起来有点别扭，但是计算机处理起来很方便，只要由低到高放置对应的字节即可。

那么这两种方式，在实际编程中会有什么不一样呢？

如果使用小端表示法来存储0x12345678，一个长度位4的字节数组```byte[] bytes```来表示的话，那么bytes[0]表示的是0x78，而bytes[1]表示0x56,bytes[2]表示0x34，而bytes[3]表示0x12。

至于如何判定自己的机器是使用小端还是大端，可通过类似下面的小程序[1]实现：

```

BOOL IsBigEndian()
{
	int a = 0x1234;
	char b =  *(char *)&a;  //通过将int强制类型转换成char单字节，通过判断起始存储位置。即等于 取b等于a的低地址部分
	if( b == 0x12)
	{
		return TRUE;
	}
	return FALSE;
}
```

有时候我们会忘记和容易搞混这两种表示方法，但只要记住一句话：```小端表示法：低位字节存放在低地址。```就可以了。因为大端表示法是跟小端相反的，也就是低位字节存放在高地址，这样是不是就变得很好记忆呢？

## 2. ULEB128(unsigned LEB128，无符号整数编码)

Little-Endian Base 128很显然是使用小端表示法，因为计算机处理小端表示法比较方便，uleb128用于无符号整数的编码、解码。uleb128中每个字节只有7位为有效位，如果第一个字节的最高位为1，表示LEB128需要使用第二个字节，如果第二个字节的最高位为1，表示会使用到第三个字节，以此类推，直到最后的字节最高位为0，当然LEB128最多使用到5个字节，如果读取5个字节后下一个字节最高位仍为1，则表示该Dex文件无效，Dalvik虚拟机遇到这种情况是直接报错。

其中无符号整数12726的解码过程，如图2所示：

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-09-01/uleb128_sample.jpg?raw=true" height="60%" width="60%">	
</div>

<p align="center">
  <b>图 2 uleb128解码示例</b><br>
</p>

例如，leb128编码格式的值"**b6 63**",表示成二进制形式为"**1011 0110,0110 0011**"。因为第一个字节的最高为1，所以这个值会用到第二个字节，第二个字节的最高位为0，所以这个值只有两个字节。又因为leb128是小端法表示，所以最终的结果表示成二进制"**11 0001,1011 0110**"，表示成十进制为12726。而编码的过程与解码过程相反，每次先取出无符号整数的低7位，然后逻辑右移7位。右移7位后，如果不为0，则表示还需要一个字节存储数据，则低7位数据位或0x8F（第8位为1）。循环这个过程直到某次逻辑右移7位后变为0为止。

go的实现代码如下：

```
func uleb128encode(num uint64) []byte {
	res := []byte{}

	if num == 0 {
		res = append(res, 0)
	} else {
		for num != 0 {
			b := (byte)(num & 0x7F)
			num >>= 7
			if num != 0 { /* more bytes to come */
				b |= 0x80
			}
			res = append(res, b)
		}
	}

	return res
}

func uleb128decode(bytes []byte) uint64 {
	if len(bytes) == 0 {
		panic("illegal input")
	}
	var res uint64 = 0
	var i uint8 = 0
	for {
		flag := bytes[i] & 0x80
		low7bit := bytes[i] & 0x7F
		res |= uint64(low7bit) << (7 * i)
		if flag != 0 {
			i++
		} else {
			break
		}
	}

	return res
}
```

## 3. SLEB128(signed LEB128，有符号整数编码)

对于有符号的sleb128来说，计算方式与uleb128是一样的。只是对uleb128的最后一个字节的最高有效位进行了符号扩展。将上面的例子中的"**b6 63**"按照sleb128进行解读。"**b6 63**"的二进制形式不变，还是"**1011 0110,0110 0011**"，这个值的最后一个字节的最高有效位为1，所以这个值是个负数。所以这个值的最终结果为"**-1 0001,1011 0110**"。另外计算机中的数都是用补码表示的，所以需要求**1 0001，1011 0110**的相反数补码。由于**1 0001,1011 0110**实际占了14个比特（连续右移2次，每次7位，即**01 0001,1011 0110**），解码时最高位需要填充1，即**11 0001,1011 0110**,即-3658。

<div align="center">
<img src="https://github.com/berryjam/berryjam.github.io/blob/master/image/2019-09-01/sleb128_sample.jpg?raw=true" height="60%" width="60%">	
</div>

<p align="center">
  <b>图 2 sleb128解码示例</b><br>
</p>

go的示例代码如下：

```
func sleb128encode(value int64) []byte {
	res := []byte{}

	more := 1

	for more != 0 {
		b := (byte)(value & 0x7F)
		signFlag := (byte)(value & 0x40)
		value >>= 7
		if (value == 0 && signFlag == 0) ||  // 正数
			(value == -1 && signFlag != 0) { // 负数
			more = 0
		} else {
			b |= 0x80
		}
		res = append(res, b)
	}

	return res
}

func sleb128decode(bytes []byte) int64 {
	if len(bytes) == 0 {
		panic("illegal input")
	}
	var res uint64 = 0
	var i uint8 = 0
	isNegative := false
	var shift uint64 = 0
	for {
		flag := bytes[i] & 0x80
		low7bit := bytes[i] & 0x7F
		res |= uint64(low7bit) << (shift)
		shift+=7
		if flag != 0 {
			i++
		} else {
			signFlag := bytes[i] & 0x40
			if signFlag != 0 {
				isNegative = true
			}
			break
		}
	}
	if !isNegative {
		return int64(res)
	} else {
		tmp := int64(res)
		tmp |= -(1 << shift)
		return tmp
	}
}
```

[3]LEB128的理解难点是在有符号数上，编码结束条件不像无符号数那么明显（value等于0），分两种情况：
1. 若为正数，7bits中的最高位为0 并且 value == 0结束，value ==0 表示高字节没有数据，而7bits最高位为0用于表示是正数，用于解码；
2. 若为负数，7bits中的最高位为1 并且 value == -1结束， value == -1表示高字节都是符号扩展出来的1， 7bits最高位为1用于表示是负数，在解码时高位填充1。

## 4. ULEB128p1(unsigned LEB128 plus 1，特殊无符号整数编码)

LEB128编码格式中还有一种特殊的编码格式uleb128p1(unsigned LEB128 plus 1)，这种编码格式的值为uleb128的值加上1。所以计算uleb128p1格式的值时，通常将这个值转换为uleb128格式，然后这个值的基础上减去一，得到的值就是uleb128p1格式的值。引入uleb128p1编码格式的目的是为了表示“-1”这个值，“-1”这个值也是最小的，例如LEB128编码“00”，使用uleb128格式进行解析，获得的值是0，所以uleb128p1格式的值为0减去1，等于-1。这也是uleb128p1表示的最小的值。

## 5. 参考资料

[[1]](https://blog.csdn.net/ce123_zhouwei/article/details/6971544) 详解大端模式和小端模式

[[2]](http://dwarfstd.org/doc/dwarf-2.0.0.pdf) DWARF Debugging Information Format 附录4第97页. uleb128、sleb128算法伪代码

[[3]](https://www.cnblogs.com/liwugang/p/7594093.html) LEB128相关知识

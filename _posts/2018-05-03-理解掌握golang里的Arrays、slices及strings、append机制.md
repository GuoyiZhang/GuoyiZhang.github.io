---
layout: post
title: 理解掌握golang里的Arrays、slices及strings、append机制
date: 2018-05-03 8:52:00.000000000 +09:00
tags: golang arrays slices strings append
---

# golang里的slice的正确打开方式

**Note. 本文参考至[官方介绍slice博客](https://blog.golang.org/slices),希望能帮助读者理解golang里的数组、slice、字符串原理以及掌握使用方法。**

-  [0. 介绍](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#0-%E4%BB%8B%E7%BB%8D)

-  [1. Arrays数组](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#1-arrays%E6%95%B0%E7%BB%84)

-  [2. Slices：slice header（切片与切片头部）](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#2-slicesslice-header%E5%88%87%E7%89%87%E4%B8%8E%E5%88%87%E7%89%87%E5%A4%B4%E9%83%A8)

-  [3. 函数传递中的slice](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#3-%E5%87%BD%E6%95%B0%E4%BC%A0%E9%80%92%E4%B8%AD%E7%9A%84slice)

-  [4. slice指针：方法接收者](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#4-slice%E6%8C%87%E9%92%88)

-  [5. slice capacity（切片容量）](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#5-slice-capacity%E5%88%87%E7%89%87%E5%AE%B9%E9%87%8F)

-  [6. Make函数](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#6-make%E5%87%BD%E6%95%B0)

-  [7. Copy函数](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#7-copy%E5%87%BD%E6%95%B0)

-  [8. Append函数：看一个例子](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#8-append%E5%87%BD%E6%95%B0%E7%9C%8B%E4%B8%80%E4%B8%AA%E4%BE%8B%E5%AD%90)

-  [9. Append函数：golang内置函数](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#9-append%E5%87%BD%E6%95%B0golang%E5%86%85%E7%BD%AE%E5%87%BD%E6%95%B0)

-  [10. Nil空值](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#10-nil%E7%A9%BA%E5%80%BC)

-  [11. Strings字符串](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#11-strings%E5%AD%97%E7%AC%A6%E4%B8%B2)

-  [12. 总结](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#12-%E6%80%BB%E7%BB%93)

-  [13. 扩展阅读](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-05-03-%E7%90%86%E8%A7%A3%E6%8E%8C%E6%8F%A1golang%E9%87%8C%E7%9A%84Arrays%E3%80%81slices%E5%8F%8Astrings%E3%80%81append%E6%9C%BA%E5%88%B6.md#13-%E6%89%A9%E5%B1%95%E9%98%85%E8%AF%BB)

## 0. 介绍

过程式编程语言最常见的特征之一就是数组的概念。数组看起来很简单，但是在编程语言中要实现数组需要解决很多问题，例如：

- 数组长度是固定还是可变？

- 数组长度是否属于数组类型的一部分，比如作为一个字段存在。还是通过计算得到？

- 多维数组又是怎么样的？

- 空数组是否有意义？

这些问题的答案会决定数组是否仅仅是语言的一个特征或者是语言设计中的一个核心部分。

在Go的早期开发中，Go的核心开发团队花了大约一年的时间来解决这些问题，才使得设计看起来相对完备。设计中关键一步是引入`slice`，这些slice基于固定大小的数组构建，以提供灵活的，可扩展的数据结构。然而，到目前为止，刚接触Go的程序员经常会在slice使用上犯错，也许是因为来自其他语言的经验固化了他们的思维方式。

在这篇文章中，我们试图捋清数组、slice之间关系。并通过一些代码片段来解释`append`内置函数是怎样的，以及它为什么这么设计。

<div align="center">
<img src="https://blog.golang.org/go-slices-usage-and-internals_slice-struct.png">
</div>

<div align="center">
<img src="https://blog.golang.org/go-slices-usage-and-internals_slice-1.png">	
</div>

## 1. Arrays数组

数组是Go的重要组成部分，但作为语言的基础设计一样，它们通常为其他特性服务。我们先简单介绍数组，但是数组功能有限，随后介绍j基于数组并且更强大和更有用的slice。

下面是一个数组声明

```
var buffer [256]byte
```

声明一个名为`buffer`的变量，它包含256个字节。`buffer`的类型包括了大小，[256]byte。要注意一个512字节的数组的类型与256字节的数组是不一样的，尽管数组元素都是字节。

与数组关联的数据就是：数组元素。`buffer`在内存中看起来就像下面那样：

```
buffer: byte byte byte ... 256次 ... byte byte byte
```

也就是说，变量保存256个字节的数据，没有别的。我们可以用熟悉的索引语法`buffer[0]`、`buffer[1]`等等直到`buffer[255]`来访问数组元素。（索引范围从0到255覆盖了256个元素。）试图用超出此范围的值索引`buffer`将导致程序崩溃。（数组越界错误）

Golang内置一个名为`len`的函数，它返回一个数组或切片的元素数量，还有一些其他数据类型（如map、channel）的元素数量。在我们的例子中，`len(buffer)`返回固定值256。

数组有各种用途，比如它们能够很好地表示转换矩阵-但它们在Go中的最常见用途是作为slice的实现服务的。

## 2. Slices：slice header（切片与切片头部）

slice在Go中使用频率很高，但要充分利用它们，必须明确地知道它们是什么以及它们做了什么。

slice是描述与slice变量本身分开存储的数组的连续部分的数据结构。**slice不是数组。**一个slice描述了一个数组的一部分。

根据前一节声明的变量`buffer`数组，我们可以通过对数组进行切片来创建一个描述元素100到150（准确地说，从100到149（包括））的slice：

```
var slice []byte = buffer[100:150]
```

在这段代码中，我们使用了完整的变量声明。变量`slice`类型为[]byte，即"包含字节的切片"，并且通过切分元素100（含）至150（不含）来从数组`buffer`初始化元素。更通用的方式是省略slice的类型，由golang的类型推断计算实际类型：

```
var slice = buffer[100:150]
```

```Note.slice类型具有左闭右开的性质，即buffer[a:b]包含了buffer数组下标为[a,b)区间的元素。```

如果是在函数里，我们可以使用更简约的声明方式：

```
slice := buffer[100:150]
```

这个切片变量究竟是什么？现在可以将切片想象成具有两个元素的小数据结构：长度和指向数组元素的指针。你可以把它看成下面的数据结构：

```
type sliceHeader struct {
    Length        int
    ZerothElement *byte
}

slice := sliceHeader{
    Length:        50,
    ZerothElement: &buffer[100],
}
```

当然，这只是一个示例，确切类型请参考$GOROOT/src/reflect/value.go源码，包含Cap、Len字段已经指向某个数组的指针unsafe.Pointer。尽管这段代码提到sliceHeader结构对程序员来说是不可见的（小写），并且元素指针的类型取决于元素的类型，但是这给出了机制的一般概念。实际代码

到目前为止，我们已经对数组使用了切片操作，但我们对切片进行切片，如下所示：

```
slice2 := slice[5:10]
```

与之前一样，此操作将创建一个新切片，再次情况下，原始切片的原书为5至9（含），表示原始数组的元素105至109。slice2变量的底层sliceHeader结构如下所示：

```
slice2 := sliceHeader{
    Length:        5,
    ZerothElement: &buffer[105],
}
```

请注意，此标头仍指向存储在变量`buffer`的数组中。

我们也可以reslice，也就是说切片并将结果存辉原始的切片结构中。

```
slice = slice[5:10]
```

slice变量的sliceHeader结构看起来就像它为slice2变量所做的一样。你会经常看到重复使用，例如截断一个切片。下面声明将删除我们slice的第一个和最后一个元素：

```
slice = slice[1:len(slice)-1]
```

可能会经常听到有经验的Go程序员谈论"slice header"，因为这确实是存储在slice变量中的内容。例如，当你调用一个把slice作为参数的函数时，比如[bytes.IndexRune](https://golang.org/pkg/bytes/#IndexRune),传进函数里的就是slice header。看下面的函数调用：

```
$GOROOT/src/bytes/bytes.go
// ...
// IndexRune函数的大致定义如下
func IndexRune(s []byte, r rune) int {
    ...
    return -1
}

slashPos := bytes.IndexRune(slice, '/')
```

传递给`IndexRune`函数的`slice`参数表面看来是一个[]byte类型的变量，但Golang在编译执行时，参数实际上是一个"slice header"。

在slice header中其实还有一个数据项Cap，我们在下面讨论，但首先让我们看看在编程中使用的slices究竟是什么。

## 3. 函数传递中的slice

理解尽管slice包含一个指针也很重要，**它本身就是一个值，但不是指针**。从数据结构上看，slice就是一个包含指针和长度的结构体。它不是一个指向结构体的指针。

这一点很重要。

当我们在前面的例子中调用`IndexRune`，它传递了slice header的一个`副本`。这种行为有重要的影响，直接会影响到程序员能否正确使用slice。

考虑下面函数：

```
func AddOneToEachElement(slice []byte) {
    for i := range slice {
        slice[i]++
    }
}
```

从这个函数名字就可以知道，这个函数作用是遍历slice，并对每个元素自增1。

下面来调用这个函数，看下调用方式以及输出结果：

```
func main() {
    slice := buffer[10:20]
    for i := 0; i < len(slice); i++ {
        slice[i] = byte(i)
    }
    fmt.Println("before", slice)
    AddOneToEachElement(slice)
    fmt.Println("after", slice)
}

// 输出结果
before [0 1 2 3 4 5 6 7 8 9]
after [1 2 3 4 5 6 7 8 9 10]
```

尽管slice header是按值传递的，但是slice header包含一个指向数组元素的指针，所以原slice header以及函数参数的slice header副本都有指针指向同一个数组。因此，在函数里slice header副本修改指向的数组元素，函数外原slice header所指向的数组元素也就同步被修改，函数返回后，原slice header所指向的数组元素就已经发生了变化。

**注意：Golang里只有值传递，没有引用传递。建议读者阅读[Go语言参数传递是传值还是传引用](http://www.flysnow.org/2018/02/24/golang-function-parameters-passed-by-value.html)，有助于理解slice在函数传递时都发生了什么。**

下面再看一个例子，通过这个例子可以看出函数是值传递，slice作为函数参数只是传递了slice header的副本，如下例所示：

```
func SubtractOneFromLength(slice []byte) []byte {
    slice = slice[0 : len(slice)-1]
    return slice
}

func main() {
    fmt.Println("Before: len(slice) =", len(slice))
    newSlice := SubtractOneFromLength(slice)
    fmt.Println("After:  len(slice) =", len(slice))
    fmt.Println("After:  len(newSlice) =", len(newSlice))
}

// 结果如下
Before: len(slice) = 50
After:  len(slice) = 50
After:  len(newSlice) = 49
```

可以从上面输出看出slice参数的`内容`可以被函数修改，但是它的slice header值不会被修改。存储在变量`slice`的长度不会因为调用函数而被修改，因为函数传递的只是slice header的副本，所以改变副本自身不会影响到原始的slice，但是副本和原slice都具有指向相同数组的指针，通过这个指针改变的值对双方都是可见的。**这里再一次强调golang只有值传递，没有引用传递。**因此，如果我们想编写一个修改slice header的函数，我们必须将它作为结果参数返回，就像上面的`SubtractOneFromLength`函数那样。变量`slice`保持不变，但是返回的值具有新长度，并且存储在变量`newSlice`中。

## 4. slice指针：方法接收者

另一种能够修改slice header的函数是定义指针，把slice header指针传递到函数参数副本。下面是前面例子的一个变体，如下：

```
func PtrSubtractOneFromLength(slicePtr *[]byte) {
    slice := *slicePtr
    *slicePtr = slice[0 : len(slice)-1]
}

func main() {
    fmt.Println("Before: len(slice) =", len(slice))
    PtrSubtractOneFromLength(&slice)
    fmt.Println("After:  len(slice) =", len(slice))
}

// 结果如下
Before: len(slice) = 50
After:  len(slice) = 49
```

这个例子看起来很笨拙，特别是处理额外的间接级别（使用一个临时变量），但是有一种常见的情况是指向slice的指针。使用指针接收器来修改slice是比较常用的方法。

假设我们希望在slice上有一个方法，在最后一个斜杠处截断它。我们可以这样写：

```
type path []byte

func (p *path) TruncateAtFinalSlash() {
    i := bytes.LastIndex(*p, []byte("/"))
    if i >= 0 {
        *p = (*p)[0:i]
    }
}

func main() {
    pathName := path("/usr/bin/tso") // Conversion from string to path.
    pathName.TruncateAtFinalSlash()
    fmt.Printf("%s\n", pathName)
}

// 结果如下
/usr/bin
```

可以从输出结果发现，如果方法接收者为指针类型，那么可以通过修改指针来达到修改slice的目的。如果把方法接收者改为值的方式，就不能改变slice，如下：

```
type path []byte

func (p path) TruncateAtFinalSlash() {
    i := bytes.LastIndex(p, []byte("/"))
    if i >= 0 {
        p = p[0:i]
    }
}

func main() {
    pathName := path("/usr/bin/tso") // Conversion from string to path.
    pathName.TruncateAtFinalSlash()
    fmt.Printf("%s\n", pathName)
}

// 结果如下
/usr/bin/tso
```

另一方面，如果我们想为`path`写一个方法，这个方法把`path`中的把ASCII字母变为大写（忽略非英文字符），这个方法接收者可以是值，因为值接收者仍将指向相同的数组。

```
type path []byte

func (p path) ToUpper() { // 使用path而不是*path作为方法接收者，仍然能改变path自身的值
    for i, b := range p {
        if 'a' <= b && b <= 'z' {
            p[i] = b + 'A' - 'a'
        }
    }
}

func main() {
    pathName := path("/usr/bin/tso")
    pathName.ToUpper()
    fmt.Printf("%s\n", pathName)
}

// 结果如下
/USR/BIN/TSO
```

## 5. slice capacity（切片容量）

下面的函数通过一个元素来扩展它的参数`slice []int`：

```
func Extend(slice []int, element int) []int {
    n := len(slice)
    slice = slice[0 : n+1]
    slice[n] = element
    return slice
}

func main() {
    var iBuffer [10]int
    slice := iBuffer[0:0]
    for i := 0; i < 20; i++ {
        slice = Extend(slice, i)
        fmt.Println(slice)
    }
}

//结果如下
[0]
[0 1]
[0 1 2]
[0 1 2 3]
[0 1 2 3 4]
[0 1 2 3 4 5]
[0 1 2 3 4 5 6]
[0 1 2 3 4 5 6 7]
[0 1 2 3 4 5 6 7 8]
[0 1 2 3 4 5 6 7 8 9]
panic: runtime error: slice bounds out of range

goroutine 1 [running]:
main.Extend(...)
	/tmp/sandbox148878173/main.go:16
main.main()
	/tmp/sandbox148878173/main.go:27 +0x140
```

上面当slice长度达到所指向数组的长度的时候，如果想继续reslice，`slice = slice[0: n+1]`就会导致程序出错。

所以接下来需要介绍slice header的第三个数据字段：`capacity`(容量)。除了数组指针和长度外，slice header还会存储容量：

```
type sliceHeader struct {
    Length        int
    Capacity      int
    ZerothElement *byte
}
```

`Capacity`字段记录底层数组实际具有的空间大小，它是`Length`可以达到的最大值。试图超越其容量来增长slice会超出slice所指向的数组范围，并导致程序出现panic。

在我们的示例slice被创建之后

```
slice := iBuffer[0:0]
```

它的slice header大致如下:

```
slice := sliceHeader{
    Length:        0,
    Capacity:      10,
    ZerothElement: &iBuffer[0],
}
```

`Capacity`字段等于所指向的数组的长度减去slice第一个元素（本例中为数组索引为0）的数组中的索引。如果想查询一个slice的容量，可以使用内置`cap`函数：

```
if cap(slice) == len(slice) {
    fmt.Println("slice is full!")
}
```

## 6. Make函数

如果我们想扩充slice的容量超越其所指向的数组长度，该怎么办？很遗憾的是没有办法！根据定义，容量限制了扩展上限。但是可以重新分配一个数组，复制数据并修改slice指向新数组来完成这样的扩充。

我们从分配开始。我们可以使用内置的`new`函数来分配一个更大的数组，然后对新数组进行slice，但是使用内置`make`函数会更简单。它分配一个新的数组并创建一个slice header，Golang源码如下：

```
// $GOROOT/src/runtime/slice.go
func makeslice(et *_type, len, cap int) slice {  
    // 根据切片的数据类型，获取切片的最大容量
    maxElements := maxSliceCap(et.size)
    // 比较切片的长度，长度值域应该在[0,maxElements]之间
    if len < 0 || uintptr(len) > maxElements {
        panic(errorString("makeslice: len out of range"))
    }
    // 比较切片的容量，容量值域应该在[len,maxElements]之间
    if cap < len || uintptr(cap) > maxElements {
        panic(errorString("makeslice: cap out of range"))
    }
    // 根据切片的容量申请内存
    p := mallocgc(et.size*uintptr(cap), et, true)
    // 返回申请好内存的切片的首地址
    return slice{p, len, cap}
}
```

`make`函数有三个参数：slice类型，它的初始长度和容量，这是分配保存slice数据的数据的长度。这个调用创建一个长度为10，可以再空出5个空间的slice：

```
slice := make([]int, 10, 15)
fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))
// 结果如下
len: 10, cap: 15
```

这段代码将`int`切片容量加倍，但保持其长度不变：

```
    slice := make([]int, 10, 15)
    fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))
    newSlice := make([]int, len(slice), 2*cap(slice))
    for i := range slice {
        newSlice[i] = slice[i]
    }
    slice = newSlice
    fmt.Printf("len: %d, cap: %d\n", len(slice), cap(slice))
    
    // 结果如下
    len: 10, cap: 15
    len: 10, cap: 30
```

运行此代码之后，slice在需要重新分配之前有更多的增长空间。

创建slices时，长度和容量通常是相同的。内置的`make`函数有一个这种常见情况的简写。长度参数默认为容量，所以可以将它们都设置为相同的值。

```
gophers := make([]Gopher, 10)
```

变量`gophers`切片的长度和容量都为10。

## 7. Copy函数

当我们在前一节中将slice的容量加倍时，我们编写累一个循环来将旧数据复制到新slice中。Go里有一份内置的函数`copy`，能更简单地进行扩容。它的参数是两个slice，把第右边的参数复制到左边参数里。这里我们的例子被改写为使用copy：

```
newSlice := make([]int, len(slice), 2*cap(slice))
copy(newSlice, slice)
// 结果如下
len: 10, cap: 15
len: 10, cap: 30
```

`copy`函数非常智能。它只会复制它能做的，注意两个参数的长度。换句话说，它复制的元素的的数量是两个slice长度的较小者。此外，`copy`函数会返回一个整数值，即它复制的元素的数量，尽管它并不总是会用上。下面是copy函数的实现，会先比较fromSlice和toSlice的长度，选择长度较小者作为函数返回值。然后计算出要移动元素的内存耗费空间，再使用memmove汇编函数对内存进行移动。

```
$GOROOT/src/runtime/slice.go
func slicecopy(to, fm slice, width uintptr) int {
	if fm.len == 0 || to.len == 0 {
		return 0
	}

	n := fm.len
	if to.len < n {
		n = to.len
	}

	if width == 0 {
		return n
	}

	if raceenabled {
		callerpc := getcallerpc(unsafe.Pointer(&to))
		pc := funcPC(slicecopy)
		racewriterangepc(to.array, uintptr(n*int(width)), callerpc, pc)
		racereadrangepc(fm.array, uintptr(n*int(width)), callerpc, pc)
	}
	if msanenabled {
		msanwrite(to.array, uintptr(n*int(width)))
		msanread(fm.array, uintptr(n*int(width)))
	}

	size := uintptr(n) * width
	if size == 1 { // common case worth about 2x to do here
		// TODO: is this still worth it with new memmove impl?
		*(*byte)(to.array) = *(*byte)(fm.array) // known to be a byte pointer
	} else {
		memmove(to.array, fm.array, size)
	}
	return n
}
```
以下是使用`copy`函数将值插入到slice指定位置的一个例子：

```
func Insert(slice []int, index, value int) []int {
    // 先把slice长度增加1，要注意slice的容量必须比长度大于等于1，否则下面会导致panic
    slice = slice[0 : len(slice)+1]
    // 使用copy函数把index位置及之后的元素都往后挪一位，这里也可以用循环来完成腾挪
    copy(slice[index+1:], slice[index:])
    // 在index位置插入value
    slice[index] = value
    // 返回最终的slice
    return slice
}

...

    slice := make([]int, 10, 20) // Note capacity > length: room to add element.
    for i := range slice {
        slice[i] = i
    }
    fmt.Println(slice)
    slice = Insert(slice, 5, 99)
    fmt.Println(slice)
    // 结果如下
    [0 1 2 3 4 5 6 7 8 9]
    [0 1 2 3 4 99 5 6 7 8 9]
```

## 8. Append函数：看一个例子

回过头来，我们编写了`Extend`函数，可以将slice扩展一个元素。不过很麻烦，因为如果slice的容量太小，这个函数会出现异常。（上面的`Insert`函数也有同样问题）现在我们已经有了解决这个问题的方法，下面是一个更健壮的函数，它能够实现interger slice的扩容。

```
func Extend(slice []int, element int) []int {
    n := len(slice)
    if n == cap(slice) {
        // slice满的时候，必须扩容
        // 创建一个容量为原来长度2倍+1的新的slice，这里要+1是为了防止原来长度为0的情况，2倍仍然为0
        newSlice := make([]int, len(slice), 2*len(slice)+1)
        copy(newSlice, slice)
        slice = newSlice
    }
    slice = slice[0 : n+1]
    slice[n] = element
    return slice
}
```

在这种情况下，返回slice特别重要，因为当它重新分配结果slice时会描述一个完全不哦难过的数组。下面是一个函数调用，会发现扩容之后slice所指向的数组已经不同：

```
    slice := make([]int, 0, 5)
    for i := 0; i < 10; i++ {
        slice = Extend(slice, i)
        fmt.Printf("len=%d cap=%d slice=%v\n", len(slice), cap(slice), slice)
        fmt.Println("address of 0th element:", &slice[0])
    }
    
// 结果如下（从第6个元素开始时，因为扩容后slice指向新的数组，导致第0个元素的地址与之前不一样）
len=1 cap=5 slice=[0]
address of 0th element: 0x10458000
len=2 cap=5 slice=[0 1]
address of 0th element: 0x10458000
len=3 cap=5 slice=[0 1 2]
address of 0th element: 0x10458000
len=4 cap=5 slice=[0 1 2 3]
address of 0th element: 0x10458000
len=5 cap=5 slice=[0 1 2 3 4]
address of 0th element: 0x10458000
len=6 cap=11 slice=[0 1 2 3 4 5]
address of 0th element: 0x10450030
len=7 cap=11 slice=[0 1 2 3 4 5 6]
address of 0th element: 0x10450030
len=8 cap=11 slice=[0 1 2 3 4 5 6 7]
address of 0th element: 0x10450030
len=9 cap=11 slice=[0 1 2 3 4 5 6 7 8]
address of 0th element: 0x10450030
len=10 cap=11 slice=[0 1 2 3 4 5 6 7 8 9]
address of 0th element: 0x10450030
```

下面将基于`Extend`函数来实现`Append`函数，并且利用Golang的可变长参数，把不固定数量的元素添加到slice里。`Append`的实现如下：

```
// Append appends the items to the slice.
// First version: just loop calling Extend.
func Append(slice []int, items ...int) []int { // items为可变长参数
    for _, item := range items {
        slice = Extend(slice, item)
    }
    return slice
}
```

## 9. Append函数：golang内置函数

Go提供了内置的`Append`函数，效率很高，并适用于任何类型的slice。Go的一个不足是任何通用类型的操作都必须由运行时提供。

```
    // Create a couple of starter slices.
    slice := []int{1, 2, 3}
    slice2 := []int{55, 66, 77}
    fmt.Println("Start slice: ", slice)
    fmt.Println("Start slice2:", slice2)

    // Add an item to a slice.
    slice = append(slice, 4)
    fmt.Println("Add one item:", slice)

    // Add one slice to another.
    slice = append(slice, slice2...)
    fmt.Println("Add one slice:", slice)

    // Make a copy of a slice (of int).
    slice3 := append([]int(nil), slice...)
    fmt.Println("Copy a slice:", slice3)

    // Copy a slice to the end of itself.
    fmt.Println("Before append to self:", slice)
    slice = append(slice, slice...)
    fmt.Println("After append to self:", slice)
```

## 10. Nil空值

顺便说一下，用我们新发现的知识，我们可以看到一个`nil`切片是什么。当然，它是slice header的零值：

```
sliceHeader{
    Length:        0,
    Capacity:      0,
    ZerothElement: nil,
}
```

或者是

```
sliceHeader{}
```

## 11. Strings字符串

字符串本质上是[]byte类型的slice，有关字符串的详细介绍参考我的[另外一篇博客](https://berryjam.github.io/2018/03/%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7/)。

## 12. 总结

要理解slice如何工作，有助于正确使用slice。有一个小的数据结构slice header，这是与slice变量相关的结构体，这个slice header里面包含指向某个数组的指针，并具有容量和长度字段。当我们在函数传递slice时，虽然传递的是一个slice header副本，但是副本所指向的数组与原slice是一样，所以改变副本的元素会导致原slice的元素也会被改变。

一旦了解到slice的原理，用起来就会很容易。而且功能强大并且具有很强的表达能力，特别是在内置的`copy`和`append`函数加持下。

## 13. 扩展阅读

还有很多扩展阅读介绍了与slice相关的内容。如前面所述，["Slice Tricks" Wiki page](https://golang.org/wiki/SliceTricks)有很多例子。[Go Slices](https://blog.golang.org/go-slices-usage-and-internals)博客用清晰的图标描述了内存布局细节。Russ Cox的[Go Data Structure](https://research.swtch.com/godata)文章包含了slice的讨论以及Go的其一他一些内部数据结构。

还有很多介绍slice的博客，但了解slice的最佳方式是使用它们。

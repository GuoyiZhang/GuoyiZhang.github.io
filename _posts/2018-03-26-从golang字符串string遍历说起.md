---
layout: post
title: 从golang字符串string遍历说起
date: 2018-03-26 10:39:00.000000000 +09:00
tags: golang 字符串 strings UTF-8 遍历
---

# 聊聊go语言的Strings、bytes、runes和字符

**Note. 本文主要参考[官方介绍字符串的博客](https://blog.golang.org/strings),希望能帮助读者在编码过程中能够正确处理字符串。**

-  [0. 为什么读取不到字符串下标n的值？](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#0-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%AF%BB%E5%8F%96%E4%B8%8D%E5%88%B0%E5%AD%97%E7%AC%A6%E4%B8%B2%E4%B8%8B%E6%A0%87n%E7%9A%84%E5%80%BC)

-  [1. 介绍](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#1-%E4%BB%8B%E7%BB%8D)

-  [2. 什么是string？](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#2-%E4%BB%80%E4%B9%88%E6%98%AFstring)

-  [3. 看一个例子：打印string](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#3-%E7%9C%8B%E4%B8%80%E4%B8%AA%E4%BE%8B%E5%AD%90%E6%89%93%E5%8D%B0string)

-  [4. UTF-8和字符串文字](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#4-utf-8%E5%92%8C%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%96%87%E5%AD%97)

-  [5. code point、字符和runes之间的关系](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#5-code-point%E5%AD%97%E7%AC%A6%E5%92%8Crunes%E4%B9%8B%E9%97%B4%E7%9A%84%E5%85%B3%E7%)

-  [6. 再看一个例子：range循环和下标循环的区别](
https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#6-range%E5%BE%AA%E7%8E%AF%E5%92%8C%E4%B8%8B%E6%A0%87%E5%BE%AA%E7%8E%AF%E7%9A%84%E5%8C%BA%E5%88%AB)

-  [7. 一些常用的字符串处理库](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#7-%E4%B8%80%E4%BA%9B%E5%B8%B8%E7%94%A8%E7%9A%84%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%A4%84%E7%90%86%E5%BA%93)

-  [8. 总结](https://github.com/berryjam/berryjam.github.io/blob/master/_posts/2018-03-26-%E4%BB%8Egolang%E5%AD%97%E7%AC%A6%E4%B8%B2string%E9%81%8D%E5%8E%86%E8%AF%B4%E8%B5%B7.md#8-%E6%80%BB%E7%BB%93)

## 0. 为什么读取不到字符串下标n的值？

我们先来看一个常见的读取字符串里面某个字符操作：

```
func testString() {
    s := "我是中国人"
    // 错误用法：读取“我”
    fmt.Println("%c",s[0]) // 输出：æ
}
```
这是一个很多从其他编程语言如java、c++的开发者刚转到golang来可能都会犯的错误：本来是想读取s的第一个字符“我”，但是输出的是一个拉丁符号“æ”。下面开始介绍golang的string类型，最后再回答为什么会输出号“æ”，如果想输出“我”代码该怎么写。

## 1. 介绍

在介绍string类型前，希望能先阅读这篇[blog](http://blog.golang.org/slices)了解下golang的slices。这篇blog用了大量示例来说明slice实现背后的机制。在此背景下，本文再来讨论golang的字符串。刚开始读者可能觉得字符串看起来太过简单，以至于不值得用一篇博客来介绍。但是要使用它们，不仅需要理解它们的原理，还需要理解字节，字符和rune之间的区别，Unicode和UTF-8，字符串和字符串文字的区别，以及其他更微妙的区别。

**Note. UTF-8是一种字符编码规范，一个字符的长度不固定，可以用1-4个字节来表示，更多详细请参考[UTF-8介绍](https://zh.wikipedia.org/wiki/UTF-8)链接。而Unicode是另外一种字符编码规范，用“U+”随后紧接着4个数字来表示一个字符。**

正如上面那个问题，有很多细节需要掌握才能正确使用golang的string类型。

## 2. 什么是string？

我们先从一些基础开始。

在Go中，一个字符串实际上是一个**只读的字节slice**。如果你完全不确定字节slice是什么或者它的用法，请先阅读[这篇博客](http://blog.golang.org/slices);下面假设你已经了解过slice。

值得重要声明的是：**一个字符串能够存储任意字节**。它没有约定一定要存储Unicode文本、UTF-8文本或者任何其他预定义的格式。就字符串的内容而言，它完全等价于一个字节slice。

下面是一个字符串文字，它使用\xNN符号来定义一个包含一些特殊字节值的字符串常量。（当然，每个字节范围是从十六进制的00到FF）

```
const sample = "\xbd\xb2\x3d\xbc\x20\xe2\x8c\x98"
```

## 3. 看一个例子：打印string

由于上面的示例字符串里面包含一些特殊的字节值，这些值既不是有效的ASCII码，也不是有效的UTF-8码，直接打印这个字符串会输出一些奇怪的符号。打印string：

```
fmt.Println(sample)
```

会输出一堆乱码（具体的输出跟运行环境有关）

```
��=� ⌘
```

为了弄清楚这个字符串究竟是什么，我们需要拆开这个字符串看看里面都是些什么。有好几种方式可以做到这点，但最明显的就是循环遍历这个字符串内容并单独分析每个里面每个字节，就像在for循环中一样：

```
for i := 0; i < len(sample); i++ {
        fmt.Printf("%x ", sample[i])
}
```

如前面的例子所暗示，**索引字符串可以访问单个字节，而不是字符**。我们将在下面回过头来详细讨论这个话题，以下是按字节循环输出的结果：

```
bd b2 3d bc 20 e2 8c 98
```

这个字符串使用了转移的十六进制符来表示每个字节。

为杂乱的字符串生成美观的可呈现的输出的更简单的方法是使用fmt.Printf的%x(十六进制)格式化输出字符串。它会把字符串的连续字节转换为十六进制数字，每个字节两个数字。

```
fmt.Printf("%x\n", sample)
```

这种输出的结果如下“

```
bdb23dbc20e28c98
```

一个小窍门是使用该格式的“空格”标志，在%和x之间放置一个空格，这样能够使得输出看起来更美观。

```
fmt.Printf("% x\n", sample)
```

从下面的输出结果可以看出每个字节间有一个空格符，从而使得整个字符串看起来不那么费劲：

```
bd b2 3d bc 20 e2 8c 98
```

还有一种打印方式，使用fmt.Printf的%q（引用）占位符，这个占位符对任何无法打印的字节序列转义，对可以打印的字节序列直接输出相应的字符，所以在不同环境下输出结果也不会是一些无棱两可的乱码。

```
fmt.Printf("%q\n", sample)
```

输出结果如下：

```
"\xbd\xb2=\xbc ⌘"
```

如果我们仔细看输出，我们可以看到除了几个无法打印被转移的十六进制符号外，还有一个ASCII等号和一个空格符和一个瑞典符号"Place of Interest"。这个符号的Unicode值为U+2318，在空格（十六进制20）后面，它的UTF-8值为e2 8c 98。

如果我们对字符串中的奇怪值不熟悉或感到困惑的时候，可以似乎用%+q占位符来查看这些奇怪值对应的UTF-8值是什么，然后通过[UTF-8](http://www.utf8-chartable.de/unicode-utf8-table.pl)表查询就可以知道是哪个国家的什么符号。这种占位符不仅可以转义不可打印的序列，而且还可以转义任何非ASCII字节，同时能够解析UTF-8字节。结果是它会显示正确格式的UTF-8字节序列的Unicode值，它表示字符串中的非ASCII数据：

```
fmt.Printf("%+q\n", sample)
```

用这种格式，瑞典符号"Place of Interest"将显示为转义的Unicode值\uxxxx:

```
"\xbd\xb2=\xbc \u2318"
```

这些打印技术在调试字符串的内容时很有用，并且在下面的讨论中也很方便。值得指出的是，所有这些方法对字节slices和字符串的作用都是一样的。**这也印证了golang的字符串本质上是字节slices。**

## 4. UTF-8和字符串文字

正如我们所看到的，索引一个字符串会产生一个字节而不是一个字符：一个字符串只是一堆字节。这意味着当我们将一个字符存到一个字符串中时，我们只是把字符对应的字节存起来。我们通过下面的例子来说明这个结论。

这是一个简单的程序，它用三种不同的方式打印一个字符串常量，一个是纯字符串，一个是ASCII引用字符串，一个是十六进制单个字节。为了避免混淆，我们通过后引号来声明一个“原始字符串”，因此它只包含文本。（用双引号括起来的一般字符串可以包含以上所示的转义序列）

```
func main() {
    const placeOfInterest = `⌘`

    fmt.Printf("plain string: ")
    fmt.Printf("%s", placeOfInterest)
    fmt.Printf("\n")

    fmt.Printf("quoted string: ")
    fmt.Printf("%+q", placeOfInterest)
    fmt.Printf("\n")

    fmt.Printf("hex bytes: ")
    for i := 0; i < len(placeOfInterest); i++ {
        fmt.Printf("%x ", placeOfInterest[i])
    }
    fmt.Printf("\n")
}
```

输出结果是：

```
plain string: ⌘
quoted string: "\u2318"
hex bytes: e2 8c 98
```

这也告诉我们Unicode字符值U+2318对应的是一个"Place of Interest"符号⌘，由字节序列e2 8c 98表示，并且这些字节是十六进制值2318的UTF-8编码。

可能有些读者通过观察输出结果很快就得出这个结论，也有读者可能感到很困惑，这取决于你对UTF-8的熟悉程度，但值得花一点时间来解释如何创建字符串的UTF-8表示。简单的事实是：它是在编写源码时创建的。

golang的源代码被定义为UTF-8文本，并且不允许其他的表示形式。这意味着当我们在源代码编写文本`⌘`时，用于创建程序的文本编辑器将符号⌘的UTF-8编码放入源文本。当我们打印出十六进制字节时，我们只是将编辑器放置在文件中的数据转储出来。

简而言之，go的源代码使用UTF-8编码，所以*字符串文字的源代码也是UTF-8文本。*如果该字符串文本不包含转义序列，而原始字符串不能，则构造的字符串将精确地保存引号之间的源文本。因此，通过定义和构造，原始字符串将始终包含其内容的有效UTF-8表示。同样，除非它包含像前一节那样的UTF-8-breaking转义符，否则常规字符串文字将始终包含有效的UTF-8。

有些人认为go字符串总是UTF-8文本，但它们不是：只有字符串文字才是UTF-8文本。正如我们在前一节中所展示的，字符串值可以包含任意字节；正如我们在这里所展示的那样，只要字符串文字没有字节级转义，字符串*文本*总是包含UTF-8文本。

总而言之，字符串可以包含任意字节，但是当从字符串文字构造时，这些字节（几乎总是）UTF-8文本。

## 5. code point、字符和runes之间的关系

迄今为止，我们一直非常小心地使用“字节”和“字符”这两个字。其中一部分原因是字符串包含字节，另外一部分原因是“字符”的概念有点难以定义。Unicode标准使用术语“code point”来表示某个字符。code point U+2318（十六进制值2318）表示符号⌘。（想查看更多关于code point的信息，请参阅其[Unicode页面](http://unicode.org/cldr/utility/character.jsp?a=2318)）

更一个更普通的例子，Unicode代码点U+0061是小写拉丁字母'A':a。

但是小写字母重音字母'A':à呢？这是一个字符，他也是一个code point(U+00E0),但它有其他表示。例如，我们可以使用“组合”重音code point U+0300，并将其附加到小写字符a，U+0061，以创建相同的字符à。一般来说，一个字符可以用许多不同的code point序列表示美因茨可以用不同的UTF-8字节序列表示。

因此，计算中的字符概念是模棱两可的，或者至少是令人困惑的，所以我们谨慎。为了使概念更可靠，有一些规范化技术可以保证给定的字符总是由相同的code point序列表示，但是这个主题现在离我们太远了。后面将解释Go库如何解决规范化问题。

“code point”有点儿含糊不清，所以GO为这个概念引入了一个较短的术语：*rune*。该术语出现在Go库和源代码中，其含义与"code point"完全相同，只是一个有趣的增加。

Go语言将rune这个词定义为int32类型的别名。因此当整数值表示一个code pint时，程序看起来会清楚些。另外，你可以把Go里的一个字符串常量称作*rune常量*。符号'⌘'的类型和值分别为**rune**和**0x2318**。

## 6. 再看一个例子：range循环和下标循环的区别

除了Go源代码是UTF-8这个事实外，Go只有一种以UTF-8编码方式处理字符串，那就会在字符串上使用for range循环。

我们已经看过很多一般的for循环是怎样的。相比之下，for range循环，在每次迭代时解码一个UTF-8编码rune。每次循环时，循环的索引都是当前rune的起始位置，以字节为单位，code point是其值。

```
s := "我是中国人"
for index, runeValue := range s {
		fmt.Printf("%#U 起始于字位置%d\n", runeValue, index)
}

// 输出结果如下：
我 起始于字位置0
是 起始于字位置3
中 起始于字位置6
国 起始于字位置9
人 起始于字位置12
```
作为对比，我们下标循环来遍历字符串，看看打印的又是什么。

```

fmt.Printf("% x\n", s)
for i := 0; i < len(s); i++ {
		fmt.Printf("%c 起始于字位置%d\n", s[i], i)
}

// 输出结果如下：
e6 88 91 e6 98 af e4 b8 ad e5 9b bd e4 ba ba
æ 起始于字位置0
 起始于字位置1
 起始于字位置2
æ 起始于字位置3
 起始于字位置4
¯ 起始于字位置5
ä 起始于字位置6
¸ 起始于字位置7
­ 起始于字位置8
å 起始于字位置9
 起始于字位置10
½ 起始于字位置11
ä 起始于字位置12
º 起始于字位置13
º 起始于字位置14
```

前面已经说过，使用下标方式索引字符串s，得到的是s[i]的字节值。是字符串s对应的二进制序列为`e6 88 91 e6 98 af e4 b8 ad e5 9b bd e4 ba ba`，所以s[0]对应的字节为e6，通过查[UTF-8编码表](http://www.utf8-chartable.de/unicode-utf8-table.pl)发现，e6对应的UTF-8字符就是拉丁字符*æ*。所以也就回答了第0节所遇到的问题。为什么s[0]输出的不是“我”而是"æ"。如果确实想读取第一个字符“我”的话，可以先把字符串转为rune数组(`runeArray :=[]rune(s)`，再通过索引runeArray来读取，`runeArray[0]`。

## 7. 一些常用的字符串处理库

Go的标准库为解释UTF-8文本提供了强大支持。如果一个range循环不足以满足你的需求的话，那么可以考虑使用Go的标准库。

最重要的包是[unicode/utf8](http://golang.org/pkg/unicode/utf8/),它包含了用于验证，反汇编和重组UTF-8字符串的编码模板。这里有一个等同于上述range循环的例子，但是这个例子中使用了该包重的**DecodeRuneInString**函数来完成相同的功能。函数的返回值是UTF-8编码字节中的rune以及宽度。

```
for i, w := 0, 0; i < len(s); i += w {
		runeValue, width := utf8.DecodeRuneInString(s[i:])
		fmt.Printf("%#U starts at byte position %d\n", runeValue, i)
		w = width
}

// 输出结果如下
U+6211 '我' 起始于字位置 0
U+662F '是' 起始于字位置 3
U+4E2D '中' 起始于字位置 6
U+56FD '国' 起始于字位置 9
U+4EBA '人' 起始于字位置 12
```

## 8. 总结

最后，对字符串类型做一个总结，希望能够帮助大家能够了解golang的字符串，并且能够在开发过程中正确处理它。

- golang的string是以UTF-8编码的,而UTF-8是一种1-4字节的可变长字符集，每个字符可用1-4字节
来表示

- 使用下标方式s[i]访问字符串s，s[i]是UTF-8编码后的一个字节(uint8)，即按字节遍历

- 使用for i,v := range s 方式访问s，i是字符串下标编号，v是对应的字符值(int32=rune)，即按字符遍历

- 使用fmt.Printf打印时，%c占位符打印的是字符,%+v占位符打印的是这个类型自身，如fmt.Printf("%+v",s[i])打印的就是字节一个十进制的无符号整数s[i]

- 如果希望以随机方式访问字符串s的每个字符，可以先转为[]rune数组，再以下标访问

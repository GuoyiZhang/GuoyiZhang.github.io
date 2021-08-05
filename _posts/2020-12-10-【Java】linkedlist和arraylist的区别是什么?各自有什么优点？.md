# 【Java】linkedlist和arraylist的区别是什么?各自有什么优点？

LinkedeList和ArrayList是常用的两种存储结构，都可以实现了List接口，那么它们之间有什么区别？下面本篇文章就来带大家了解一下LinkedeList和ArrayList之间的区别，希望对大家有所帮助。

![](https://img-blog.csdnimg.cn/img_convert/4cb93023125fdc28c2138e5bd8e31cd5.png)
LinkedeList和ArrayList的区别
**1、数据结构不同**
ArrayList是Array(动态数组)的数据结构，LinkedList是Link(链表)的数据结构。
**2、效率不同**
当随机访问List（get和set操作）时，ArrayList比LinkedList的效率更高，因为LinkedList是线性的数据存储方式，所以需要移动指针从前往后依次查找。
当对数据进行增加和删除的操作(add和remove操作)时，LinkedList比ArrayList的效率更高，因为ArrayList是数组，所以在其中进行增删操作时，会对操作点之后所有数据的下标索引造成影响，需要进行数据的移动。
**3、自由性不同**
ArrayList自由性较低，因为它需要手动的设置固定大小的容量，但是它的使用比较方便，只需要创建，然后添加数据，通过调用下标进行使用；而LinkedList自由性较高，能够动态的随数据量的变化而变化，但是它不便于使用。
**4、主要控件开销不同**
ArrayList主要控件开销在于需要在lList列表预留一定空间；而LinkList主要控件开销在于需要存储结点信息以及结点指针信息。
 



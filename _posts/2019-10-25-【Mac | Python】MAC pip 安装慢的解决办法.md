# 【Mac | Python】MAC pip 安装慢的解决办法

$ mkdir ~/.pip $ vim ~/.pip/pip.conf
 
[global] index-url = https://mirrors.cloud.tencent.com/pypi/simple

----

----

----

----

----

----

## 换源

豆瓣：[http://pypi.douban.com/simple/](http://pypi.douban.com/simple/)
清华：[https://pypi.tuna.tsinghua.edu.cn/simple](https://pypi.tuna.tsinghua.edu.cn/simple)

## []()[]()临时使用

加参数 -i
例如：pip install -i [https://pypi.tuna.tsinghua.edu.cn/simple](https://pypi.tuna.tsinghua.edu.cn/simple) XXX

## []()[]()永久修改

### []()[]()linux

修改 ~/.pip/pip.conf (没有就创建一个)， 修改 index-url至tuna，内容如下：
[global] index-url = https://pypi.tuna.tsinghua.edu.cn/simple

### []()[]()windows

windows下，直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下：
[global] index-url = https://pypi.tuna.tsinghua.edu.cn/simple

# 【Java | Spring Boot】IntelliJ IDEA 轻松搭建Spring Boot项目（零基础入门，超详细！）


现在越来越多的大公司都要用到spring boot 

毕竟省去的很多不必要的麻烦，可以更快的进行开发

相信很多小伙伴都为项目中的配置文件而烦恼，如果学会了Spring boot做项目，相信你会感觉人生已经到达了巅峰 ！

 

首先打开IDEA，我们创建一个项目，选择Spring Initializr 点击 Next

![](https://img-blog.csdnimg.cn/20190213145548756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

点击Next进行下一步的操作，基本上也没有什么很多要改的地方，大家参考下图~

![](https://img-blog.csdnimg.cn/20190213150455921.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

填写完毕之后点击 Next，然后开始选择项目所需要的依赖，这里由于只是简单的入门演示，所以选择Web即可

当然还有很多的依赖都可以选择，这就省去了很多麻烦，节省了大家的时间~

![](https://img-blog.csdnimg.cn/20190213150707215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

选择好需要的依赖以后继续点 Next 

![](https://img-blog.csdnimg.cn/20190213151518735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

以上步骤基本可以直接默认，点击 Finish完成创建

创建完成后项目结构如下

![](https://img-blog.csdnimg.cn/20190213151843686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

我们可以清理下没用的文件，让项目看起来更加的整洁，将我用红色标记部分删除即可~

![](https://img-blog.csdnimg.cn/20190213151936484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

然后创建一个Controller

![](https://img-blog.csdnimg.cn/20190213164518454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01hTm9uZ1hm,size_16,color_FFFFFF,t_70)

重点：必须把Controller目录创建在  Application 文件同级目录下！不然访问会报错！

确定没问题后访问你的路径，比如：http://localhost:8080/test

![](https://img-blog.csdnimg.cn/2019021316482898.png)

到这里，我们的springBoot项目的搭建已经成功，接下来我们就可以写自己的业务逻辑啦~


# 【Vue】零基础学习Vue: 第10课 Vue中v-model指令 的input单选框、复选框、下拉框、文本框：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>Document</title> <script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 单选框 --> <input type="radio" v-model="a" value="女">女 <input type="radio" v-model="a" value="男">男 <br> <!-- 复选框 --> <input type="checkbox" v-model="arr" value="香蕉">香蕉 <input type="checkbox" v-model="arr" value="苹果">苹果 <input type="checkbox" v-model="arr" value="梨子">梨子 <input type="checkbox" v-model="arr" value="菠萝">菠萝 <br> <!-- 下拉框单选 --> <select v-model="b"> <option value="鸟">鸟</option> <option value="狗">狗</option> <option value="鱼">鱼</option> </select> <br> <!-- 下拉框多选 multiple:多选属性--> <select v-model="arr2" multiple> <option value="鸟">鸟</option> <option value="狗">狗</option> <option value="鱼">鱼</option> </select> <br> <!-- 文本框 --> <textarea v-model="c"></textarea> </div> <script> let vm = new Vue({ el:"/#app", data:{ a:"男", //单选框 用单属性存储 b:"狗", //下拉框 用单属性存储 c:"文本内容",//文本框 用字符串属性存储 arr:[], //复选框内容 用数组存储 arr2:[] //下拉框对选 用数组存储 } }) </script> </body> </html>

## 运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190414163325780.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNjE0OTI4,size_16,color_FFFFFF,t_70)


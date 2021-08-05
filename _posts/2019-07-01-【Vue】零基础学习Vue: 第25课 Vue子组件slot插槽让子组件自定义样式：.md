# 【Vue】零基础学习Vue: 第25课 Vue子组件slot插槽让子组件自定义样式：


### slot插槽的作用：

1. slot 可以让组件更加灵活
1. slot插槽 默认是没有名字的 可以给slot加上name属性
1. slot也可以设置默认值 一个槽可以被插入多个内容 ，多个内容可以全都放在template中，只要给template命名name即可

### slot插槽的使用如下：

<!DOCTYPE html> <html lang="en"> <head> <meta charset="UTF-8"> <title>插槽</title> <!-- 引入vue --> <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script> </head> <body> <div id="app"> <!-- 引入子组件son --> <son> <!-- 没写name默认插入匿名插槽中 --> <h1>首页</h1> <!-- 下面注释的都是插入header槽内的数据 为了方便我们可以统一放入template标签中 --> <!--<h2 slot="header">标题1</h2>--> <!--<h2 slot="header">标题2</h2>--> <!--<h2 slot="header">标题1</h2>--> <!--<h2 slot="header">标题2</h2>--> <!-- 我们将上面插入的标签统一放入template标签中 只写一次header即可--> <template slot="header"> <h2>标题1</h2> <h2>标题2</h2> <h2>标题1</h2> <h2>标题2</h2> </template> <!-- 这是插入 footer 槽中的数据--> <p slot="footer">页脚</p> </son> </div> <template id="son"> <div> <!-- 插槽的作用： 给子组件标签设置插槽 这样可以通过插入标签自定义子组件中的标签内容了 --> <!-- 定义匿名插槽： 插槽可以匿名 这样引入插槽就不需要写入name--> <slot>匿名插槽</slot> <!-- 定义header插槽 --> <slot name="header">默认标题</slot> 我是子组件的内容： <!-- 定义footer插槽 --> <slot name="footer"></slot> </div> </template> <script> //定义子组件 let son = { template:'/#son', } let vm = new Vue({ el:'/#app', //注册子组件 components:{ son } }) </script> </body> </html>

 

### 运行结果如下：

## []()[]()首页

## []()[]()标题1

## []()[]()标题2

## []()[]()标题1

## []()[]()标题2

我是子组件的内容：
页脚

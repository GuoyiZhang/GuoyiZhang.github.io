---
layout:     post
title:      【Vue】countUp.js 实现数字滚动 动态效果
subtitle:   【Vue】countUp.js 实现数字滚动 动态效果
date:       2019-04-10 11:46:45
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - VUE
---
>blog.getTitle() 

# 【Vue】countUp.js 实现数字滚动 动态效果

https://github.com/xlsdg/vue-countup-v2
 
Installation $ npm install --save countup.js vue-countup-v2 Usage <template> <div class="iCountUp"> <ICountUp :endVal="endVal" :options="options" @ready="onReady" /> </div> </template> <script type="text/babel"> import ICountUp from 'vue-countup-v2'; export default { name: 'demo', components: { ICountUp }, data() { return { endVal: 120500, options: { useEasing: true, useGrouping: true, separator: ',', decimal: '.', prefix: '', suffix: '' } }; }, methods: { onReady: function(instance, CountUp) { const that = this; instance.update(that.endVal + 100); } } }; </script> <style scoped> .iCountUp { font-size: 12em; margin: 0; color: /#4d63bc; } </style>

 



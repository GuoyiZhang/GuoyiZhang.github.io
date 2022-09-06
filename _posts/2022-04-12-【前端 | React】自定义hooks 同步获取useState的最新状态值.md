---
layout:     post
title:      【前端 | React】自定义hooks 同步获取useState的最新状态值
subtitle:   【前端 | React】自定义hooks 同步获取useState的最新状态值
date:       2022-04-12 17:30:50
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 前端
    - react
    - javascript
    - react.js
---

>【前端 | React】自定义hooks 同步获取useState的最新状态值

# 【前端 | React】自定义hooks 同步获取useState的最新状态值

## 背景

不使用hook时，我们可以在setState回调函数获取最新值

使用react hook时，最新的值只能在useEffect里面获取

但我们有时候的业务场景需要我们同步拿到变量的最新变化值，以便做下一步操作；

这时我们可以封装一个hook通过结合useRef通过回调函数来拿到最新状态值。

## 代码

import { useEffect, useRef, useState } from 'react'; //*/* /* 自定义 useState /* @param state /* @returns /*/ const useSyncState: any = (state: any) => { const cbRef: { current: any } = useRef(); const [data, setData] = useState(state); useEffect(() => { cbRef.current && cbRef.current(data); }, [data]); return [ data, (val: any, callback: any) => { cbRef.current = callback; setData(val); }, ]; }; export default useSyncState;

## 使用

const [data,setData] = useSyncState(0); setData(1, function (data) { console.log("我是最新的值：", data); })



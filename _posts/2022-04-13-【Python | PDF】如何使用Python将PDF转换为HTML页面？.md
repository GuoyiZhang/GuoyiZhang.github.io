---
layout:     post
title:      【Python | PDF】如何使用Python将PDF转换为HTML页面？
subtitle:   【Python | PDF】如何使用Python将PDF转换为HTML页面？
date:       2022-04-13 14:41:13
author:     Sunny day
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - 后端
    - Python
    - python
    - pdf
    - html
---

>【Python | PDF】如何使用Python将PDF转换为HTML页面？

# 【Python | PDF】如何使用Python将PDF转换为HTML页面？

前言

最近想做一个小的功能，将PDF文字提取，并转换为HTML页面 ，但苦苦找寻没有合适好用简单的方法。Google一下，马上知道，接下来就是学习的结果，分享给大家，以免踩坑

含泪分享，希望大家喜欢，直接上代码

本文仅用于知识分享！

第一个版本，简单实现了HTML输出

import fitz from tqdm import tqdm def pdf2html(input_path,html_path): doc = fitz.open(input_path) for page in tqdm(doc): html_content = page.getText('html') print("开始输出html文件") with open(html_path, 'w', encoding='utf8', newline="") as fp: fp.write(html_content) input_path = r'G:\back\pyfile\翻译\pdf_translate-master\3.pdf' /# 如果报错 就用绝对路径 html_path = r'G:\back\pyfile\翻译\pdf_translate-master\input.html' pdf2html(input_path,html_path)

第二个版本，优化了HTML输出的样式（做了居中对齐）

import fitz from tqdm import tqdm def pdf2html(input_path, html_path): doc = fitz.open(input_path) print(doc) html_content = "<!DOCTYPE html><html lang=\"en\"><head><meta charset=\"UTF-8\"><title>Title</title></head><body style=\"display: flex;justify-content: center;flex-direction: column;background: /#0e0e0e;align-items: center;\">" for page in tqdm(doc): html_content += page.getText('html') print("开始输出html文件") html_content += "</body></html>" with open(html_path, 'w', encoding='utf8', newline="") as fp: fp.write(html_content) input_path = r'/Users/guoyi/Desktop/report123.pdf' /# 如果报错 就用绝对路径 html_path = r'/Users/guoyi/Desktop/report123.html' pdf2html(input_path, html_path)

## 安装

pip install PyMuPDF 或者pip3 install PyMuPDF pip install tqdm 或者pip3 install tqdm

有任何疑问评论咨询我~



---
title: "朝鲜日报搜索API爬虫 조선일보 Crawler use API by Python"
description: 通过对朝鲜日报网站的http请求包分析，构造文章搜索请求API，获取搜索文章列表。
toc: true
featuredImage: imgs/chosun.png
authors:
  - jmsu
tags:
  - Crawler
  - Python
  - requests
  - Statictic
categories:
series:
date: '2024-04-14'
lastmod: '2024-04-14'
draft: false
---

本文对朝鲜日报(chosun.com，조선일보)的文章搜索API进行分析，并使用Python构建请求爬虫。

## URL/ HTTP Request Analysis

首先，导航至朝鲜日报的搜索页面，其URL构造如下（以 페미니즘 为例）：

```
https://www.chosun.com/nsearch/?query=%ED%8E%98%EB%AF%B8%EB%8B%88%EC%A6%98&page=1&siteid=&sort=1&date_period=&date_start=&date_end=&writer=&field=&emd_word=&expt_word=&opt_chk=false&app_check=0&website=www,chosun&category=
```

基本URL为 https://www.chosun.com/nsearch/ ，可以看出get请求包含了许多参数，其中query值的URL Encode能看出其代表搜索关键词。

此时其实可以直接通过构造URL发送HTTP GET请求，但通过对网页的粗略判断，朝鲜日报使用js异步加载，即使直接获取了此URL返回的HTML内容，也很难对其进行提取。

![searchpage html code](image.png "搜索页面HTML Code")

上图为搜索页面的源码，可以看出由于js异步技术，其文本内容被js结构包含，获取到我们想要的信息过程过于繁杂，且将造成大量的内存占用。因此对此页面进行抓包，找出异步请求部分的API请求，并尝试用Python构造真正的API请求。

使用Chrome自带的检查工具可以轻松检视页面的http请求，筛选XHR类型，很轻松找到了API地址，其返回结果如下

![alt text](image-1.png "API抓包")

可以看出返回的json数据包含了此搜索页的10条新闻搜索结果，接下来使用Python构造请求并解析其返回的json及其元数据就可以了。


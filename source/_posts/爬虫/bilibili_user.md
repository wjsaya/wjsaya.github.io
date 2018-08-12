---
title: '[python爬虫]--爬取bilibili'
date: 2017-11-23
tags: [py爬虫]
categories: [编程,python,爬虫]
---


>最近在学习python爬虫，写出来的一些爬虫记录在个人博客里，同时备份一个放在了github上。
个人博客地址：https://www.wjsaya.top
github地址：https://github.com/wjsaya/python_spider_learn/
本次内容：从bilibili的开放api获取up主的公开信息
<!--more-->

----------

思路：
--

 1. 初始化数据库（db_init）。
 2. 从up_waiting库获取up主id(get_up_mid)。
 3. 传入up主id，从api获取信息，解析信息。
 4.  解析基本信息，入库(up_info)
    --    线程开始         --
    解析关注的其他up主，获取id，与up_info表核对之后入库(up_waiting)
    --    线程结束         --
 5.   >   回2



----------



代码：
--

``` python
#coding: utf-8
import time
import re
import os
import requests
from bs4 import BeautifulSoup


```



下载过程截图：
![下载过程截图](http://img.blog.csdn.net/20170614163946243?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2F5YV93ag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


恩。。。效果图：
![恩。。。效果图](http://img.blog.csdn.net/20170614164348318?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2F5YV93ag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

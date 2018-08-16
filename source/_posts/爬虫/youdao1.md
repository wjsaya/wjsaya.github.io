---
title: '[python爬虫]--调用有道词典进行翻译' 
date: 2017-06-14
tags: [py爬虫]
categories: [编程,python,爬虫]
---

>最近在学习python爬虫，写出来的一些爬虫记录在csdn博客里，同时备份一个放在了github上。
github地址：https://github.com/wjsaya/python_spider_learn/
本次内容:通过有道词典的接口写一个命令行的翻译工具。
<!--more-->

----------
思路：
--
1. 获取用户输入。
2. 通过值构造请求header。
3. 向有道翻译的对应接口发送headers，然后获取返回并取出结果并输出。

----------



代码：
--

``` python
#调用有道词典的web接口进行翻译
#coding: utf-8
import requests
import json

def translate(word=None):
    url = 'http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null'
    key={
    'type':"AUTO",
    'i':word,
    "doctype":"json",
    "version":"2.1",
    "keyfrom":"fanyi.web",
    "ue":"UTF-8",
    "action":"FY_BY_CLICKBUTTON",
    "typoResult":"true"
    }  
    #key这个字典为发送给有道词典服务器的内容，里面的i就是我们需要翻译的内容。此处直接调用word变量。
    response = requests.post(url,data=key)
    return result

def get_result(li=None):
    result = json.loads(li.text)
    print ("输入的词为：%s" % l
i['translateResult'][0][0]['src'])
    print ("翻译结果为：%s" % li['translateResult'][0][0]['tgt'])

def main:
	    print ("本程序调用有道词典的API进行翻译，可达到以下效果：")
	    print ("外文-->中文")
	    print ("中文-->英文")
	    word = input('请输入你想要翻译的词或句：')
	    list_trans = translate(word)
	    get=get_result(list_trans)
	
if __name__ == '__main__':
	main()

```

效果图：
--
![youdao](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/youdao.png)


---
title: '[python爬虫]--爬取豆瓣音乐topX'
date: 2017-06-15
tags: [python,爬虫]
---




> 最近在学习python爬虫，写出来的一些爬虫记录在csdn博客里，同时备份一个放在了github上。
github地址：https://github.com/wjsaya/python_spider_learn/
本次内容：从豆瓣的top250音乐界面爬取指定的topX专辑。
<!--more-->

----------
思路：
--

 1. 拼接出豆瓣topX页面URL。
 2. 用BS去访问和解析豆瓣topX页面URL，获取页面内的所有歌手名和专辑名并拼接，然后输出。

----------


代码：
--
``` python
#coding: utf-8
import re
import requests
from bs4 import BeautifulSoup

url = "https://music.douban.com/top250?start=25"
firefox={"User-Agent":"Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:53.0) Gecko/20100101 Firefox/53.0 FirePHP/0.7.4"}

def main():
#主函数，一切的开始，以及变量值获取
    maxm = input("想获取豆瓣排名前多少的音乐列表呢？(0-250之间）：")
    count_main = 0
    url_li = get_pages()
    for i in range(0,9):
        if count_main < int(maxm):
            count_main += get_details(url_li[i], count_main, maxm)


def get_soup(url):
#获取html并解析为BS
    html = requests.get(url, headers=firefox)
    soup = BeautifulSoup(html.content, 'lxml')
    return soup


def get_details(url, count_in_loop, maxm):
#获取豆瓣音乐专辑的名称与url链接
    content = get_soup(url)
    html = content.find_all('a', class_="nbg")
    for i in range(0,25):
        if count_in_loop < int(maxm):
            print ("歌手-名字："+html[i]["title"])
            print ("链接："+html[i]["href"])
        count_in_loop += 1
    return count_in_loop

def get_pages():
#从豆瓣网页解析出豆瓣下方的下一页标签中的地址列表，此函数可升级为解析下一页url。
#或者直接构造豆瓣url地址，?start=XXX即可。
    content = get_soup(url)
    l = content.find("div", class_="paginator")
    url_li = []
    for i in l.find_all('a'):
        url_li.append(i['href'])
    return url_li[0:9]

if __name__ == '__main__':
    main()

```

效果图：
--
![douban_music](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/douban_music.png)


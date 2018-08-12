---
title: "[hexo]--给站点做了个小升级"
date: 2018-08-12
tags: hexo
categories: [hexo,web]
---

> 
对博客做了些升级，原因是网站文章被爬取，以及CSDN图片无法外链问题
个人博客地址：https://www.wjsaya.top
CSDN博客地址：https://blog.csdn.net/saya_wj
>
<!--more-->

----------


问题列表
--
 1. 这个博客是个人写着玩儿的，主要是自己做一些总结。因为之前听说github Pages是不允许百度蜘蛛爬取的，好奇尝试百度了下部分文章，不百度没什么，百度之后发现不止百度爬取了，还有其他未授权网站爬了我的文章，爬了也就算了，还在图片上打上了大大的马赛克。。。不过我倒是感谢他们的蜘蛛，这样帮我推广的话，我觉得有必要在文章里写下我自己的博客地址了233
 2. 之前的文章都是CSDN发一份，博客发一份，因此前面的文章配图都是直接扣的CSDN的地址。。。一直以来相安无事，不知何时，CSDN做了防盗链，现在直接访问的话就会抛出403错误，看来是时候找一个图床了。说起来怪不好意思的，在CSDN推广了博客，还顺带借用了人家的图床那么久。
 3. 此外，我额外给hexo加了个博客总字数统计的插件。大致记录下。
 
 


----------


版权问题
--
 关于这个，以后的文章配图里或者文章里会出现我自己的博客地址。
 添加版权声明
 1. 到站点_config.yml找到post_copyright，将下面的enable改为true
 2. 默认版权放在正文里，看着不舒服，挪到文章底部。修改./themes/next/layout/_macro/post.swig，把post-copyright的div放进post-footer里，在post.tags之后。
 
![过程截图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_1.png?raw=true)

最终效果如下图，嘛。。。君子协定而已。

![效果图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_2.png?raw=true)


----------
图库替换
--
重新找图床，备选项如下：

服务商|结论
--|--
onedrive|5G空间，但是国内访问速度实在是。。。
微博相册|盗链，有人在用，但是能用多久呢？我用CSDN已经翻车了。。。
百度云|有8s前车之鉴，而且相当于盗链，不太想用。
坚果云|空间暂时无限？想分享？手机号实名。想给未注册坚果云用户分享？收费。
亿方云|100G空间，想共享？手持身份证实名。
七牛云|20G空间，40G流量，手持身份证实名。
网易云|50G空间，50G流量，手持身份证实名。
icloud|没有测试，但是估计很难用。
github|无限？反正目前来看还行。
码云|1G空间限制。之前准备放博客时恶心到我，不太想用。

&emsp;&emsp;最终结论：国内一票xx云要么需要实名，要么有坑；国外的google Drive，onedrive不可用，最终用github...而且github里图片是固定链接（仓库+文件名），这点很爽！唯一麻烦的可能就是需要在本地维护一个git的仓库了吧。。。没有图床来的方便。本篇博文图片已经迁移到github，以前的文章会再找时间进行迁移。

----------

其他优化
--
- 开启阅读进度，到站点_config.yml找到scrollpercent，改为true即可。
- 开启站点和文章字数统计。
	- 先用npm安装 hexo-wordcount：  
    ```shell
    npm install hexo-wordcount
    ```
	- 接下来对标题栏字数统计做一些自定义：
	- 打开站点_config.yml找到post_wordcount，把下面的item_text改为false，wordcount和min2read改为true。
	- 打开./themes/next/layout/_macro/post.swig，定位到title为post.wordcount的span和post.min2read的span，在后面加一点自定义的内容（我这里就是“ 字”和“ 钟”），如下图：
	- 修改前：
	```shell
	...忘记保存了，略过吧。。。
	```
	- 修改后（Line190-224）：
	```shell
	{% if theme.post_wordcount.wordcount or theme.post_wordcount.min2read %}
	...省略无关代码...
            {% if theme.post_wordcount.item_text %}
              <span class="post-meta-item-text">{{ __('post.wordcount') }}</span>
            {% endif %}
            <span title="{{ __('post.wordcount') }}">
              {{ wordcount(post.content) }} 字
            </span>
          {% endif %}
	...省略无关代码...
            {% if theme.post_wordcount.item_text %}
              <span class="post-meta-item-text">{{ __('post.min2read') }}</span>
            {% endif %}
            <span title="{{ __('post.min2read') }}">
              {{ min2read(post.content) }} 分钟
            </span>
          {% endif %}
        </div>
      {% endif %}
	```

	- 效果图：
  
    ![效果图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_5.png?raw=true)


- 对站点底部自定义

    ![原图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_6.png?raw=true)

	- 首先，去掉”由“、”强力驱动"、"主题 - "以及“本博客共计”，放在那里着实碍眼。
		- 直接修改./themes/next/layout/_partials/footer.swig，此处需要删减一些代码
		- 原代码：
		```shell
		{% if theme.copyright %}
		<div class="powered-by">
		  {{ __('footer.powered', '<a class="theme-link" href="https://hexo.io">Hexo</a>') }}
		</div>
		
		<div class="theme-info">
		  {{ __('footer.theme') }} -
		  <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
		    NexT.{{ theme.scheme }}
		  </a>
		</div>
		{% endif %}
		
		<div class="busuanzi-count">
		  <span class="site-pv">本博客共计{{ totalcount(site) }}字</span>
		</div>
		```	


		- 修改后：	
		```shell
		{% if theme.copyright %}
		<div class="powered-by">
		  {{'<a class="theme-link" href="https://hexo.io">Hexo</a>'}}
		</div>
		
		<div class="theme-info">
		  <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
		    NexT.{{ theme.scheme }}
		  </a>
		</div>
		{% endif %}
		
		<div class="busuanzi-count">
		  <span class="site-pv">
		    {{ totalcount(site) }}字
		  </span>
		</div>
		```
		- 效果图：
  
		![效果图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_7.png?raw=true)
	
	- 把字数统计和主题版权声明合并
		- 直接修改./themes/next/layout/_partials/footer.swig
        - 原代码（Line11-27）：
            
        ```shell
            
        {% if theme.copyright %}
        <div class="powered-by">
        {{'<a class="theme-link" href="https://hexo.io">Hexo</a>'}}
        </div>
        
        <div class="theme-info">
        <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
            NexT.{{ theme.scheme }}
        </a>
        </div>
        {% endif %}
        
        <div class="busuanzi-count">
        <span class="site-pv">
            {{ totalcount(site) }}字
        </span>
        </div>
        ```
        - 修改后(直接丢弃了字数统计和主题的class...)：
            
        ```shell
            {% if theme.copyright %}
        <div class="powered-by">
        {{'<a class="theme-link" href="https://hexo.io">Hexo</a>'}}
        </div>
        
        <div class="powered-by">
        <a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
            NexT.{{ theme.scheme }}
        </a>
        </div>
        共 {{ totalcount(site) }} 字
        {% endif %}

        ```

        - 效果图：
  
        ![效果图](https://github.com/wjsaya/BlogPictures/blob/master/some_upgrade_8.png?raw=true)

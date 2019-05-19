---
title: "[hexo]--hexo添加TravisCI自动发布"
date: 2019-05-20
tags: hexo
categories: [hexo,admin]
---

> 
每次写了文章手动编译上传两次（HEXO源码&HEXO站点文件）太麻烦，因此需要引入TravisCI，实现HEXO源码push后自动构建并发布HEXO站点静态文件。

个人博客地址：https://www.wjsaya.top

github地址：https://github.com
>
<!--more-->

----------


目标
--
 1. 实现github push后自动构建hexo项目并发布。
 
 
----------
思路：
--
 1. github开放仓库给TravisCI
 2. 配置TravisCI

----------
配置过程：
--
 1. github开放仓库给TravisCI
   首先需要到github创建一个Personal access tokens，给TravisCI开放访问源以及对源执行push操作的权限
	 创建位置在Settings->Developer settings-> Personal access tokens，也就是[此url](https://github.com/settings/tokens)
	 如下图所示，开放repo的权限即可

	 ![github_personal_access_token](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/github_personal_access_token.png)

 2. 配置TravisCI
	  访问https://travis-ci.com官网，然后Sign in with github或者Sign uo with github，通过github帐号登录或者注册TravisCI
		登录时会跳转到github进行授权，此处按照喜好来授权就好，由于我现在只需要TravisCI帮忙构建hexo，因此只开放了hexo源的权限，根据自己需求配置即可。

	 ![github_access_app_settings](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/github_access_app_settings.png)

 3. 配置TravisCI下Repositories

   -	打开TravisCI的dashboard，打开想配置的项目，打开More optionjs下的Settings，首先是General字段，目前网络上的教程和实际稍有出入，但是影响不大。主要是构建触发的时机有所改变，对于hexo，勾选Buid pushed branches即可，每次对项目执行push操作就会触发TravisCI的构建操作。  
	 -	此外，Environment Variables下可以设置环境变量，此处的变量可以在TravisCI构建时调用，但是此处的环境变量默认是不会显示在构建历史记录的。因此应该把github的access token放在此处，避免泄露。记得不要勾选Display value in build log选项即可（此项默认不勾选）。  

	 ![travisci_variables](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/travisci_variables.png)


 4. 配置hexo项目
   -	主要是添加_travis.yml文件。TravisCI检测到此文件后就会根据设置中的条件（Buid pushed branches）以及_travis.yml文件的配置进行构建。_travis.yml文件中，指定构建语言为node.js，并且设置监听的repo，我的hexo源文件在hexo分支，项目发布地址在master分支。因此监听hexo的改变即可。  
   -	网络上大多教程都是全自动模拟手动：1手动执行git pull拉取最新的源码 -> 在TravisCI环境里执行hexo generate命令生成网站源码 -> 切换到public目录手动执行push操作，这样是可行的，但是太过于笨重。我这里使用了更轻松的方式，那就是先修改项目的_config.yml文件，然后直接通过hexo d -g执行发布操作。这样流程更简单，更容易调试错误。    

		_travis.yml文件分为多个部分，涉及到发布的部分如下，可自行决定是否合并，我为了逻辑清晰做了拆分：  
*   before_install部分中指定安装hexo-cli的环境  
*   install段指定安装当前项目下的依赖模块  
*   scripts中执行shell命令hexo clean，清除现有的hexo缓存  
*   after_script，使用sed命令修改_config.yml中的发布地址，然后使用hexo d -g命令发布hexo  
		本站点的[_travis.yml](https://github.com/wjsaya/wjsaya.github.io/blob/hexo/.travis.yml)文件如下，修改CI_REPO即可，sed会替换_config.yml文件中的发布地址：

----------
_travis.yml
--
``` yml
language: node_js # 设置语言
node_js: stable # 设置相应版本

cache:
    directories:
        - node_modules # 缓存不经常更改的内容
    
env:
    global:
        # Travis CI系统/docker配置完成后，执行，配置部分环境变量，可以引用TravisCI网页上设置的变量
        - CI_REPO: https://${hexo_token}@github.com/wjsaya/wjsaya.github.io.git

branches:
    only:
        - hexo # 只监测hexo分支

before_install:
    # 安装前执行的脚本
    - npm install hexo-cli -g  # 安装hexo环境
    
install:
    # 安装脚本,before_install后执行
    - npm install # 安装hexo及插件

script:
    # 执行脚本，install后执行
    - hexo clean # 清除
    # - hexo generate # 生成

after_script:
    # script后执行
  - sed -i "s@repo:.*@repo:\ ${CI_REPO}@g" ./_config.yml
  - hexo deploy -g
  # 不手动执行，直接通过hexo deploy来调用_config.yml发布

  # 可同时发布到github和coding
    # - git clone https://${GITHUB_URL} .deploy_git
    # - # add commit timestamp
    # - git add .
    # - git commit -m "Travis CI Auto Build at `date +"%Y-%m-%d %H:%M"`"
    # - git push --force --quiet "https://${hexo_token}@${GITHUB_URL}" master:master
    # - git push --force --quiet "https://hadronw:${Travis_co_token}@${CODING_URL}" master:master
```

TravisCI构建状态截图：
--
Build History(构建历史)，可点击绿色或红色部分查看构建详情：
![travisci_status1](https://raw.githubusercontent.com/wjsaya/BlogPictures/master/travisci_status1.png)

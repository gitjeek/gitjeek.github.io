---
layout:       post
title:        "scrapy通过scrapyinghub实现24小时爬虫托管爬取"
subtitle:     "Continuous scrapy crawling by scrapyinghub"
date:         2017-06-04 22:39:00
author:       "Jagger"
header-img:   "img/title_bar/lianjia-chengdu."
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 爬虫
    - python
    - scrapy
---
#### 本文仅供学习参考作用,概不对任何事情进行负责!


**前言**  
当我们在对网上的数据进行爬取,但是需要爬取的数据量特别大,爬取的时间特别长的时候(比如需要爬一周),我们难免会思考一个问题:**我总不可能一直将电脑开着去抓取数据吧?**  


这个时候我们一般有两种解决方案:
1. 购买云服务器进行24小时的爬取.
2. 通过爬虫托管网站进行24小时的爬取.  


本文将介绍第二种方法,通过scrapy的scrapyinghub实现24小时的爬虫托管爬取

***

**目录**

1. 需求背景
2. scapyinghub介绍
3. 实现流程

***

#### 1.需求背景
最近学校有一门搜索引擎的课程,我们需要搭建一个自己的搜索引擎.搭建搜索引擎的过程中我们需要有数据进行填充.这个时候我们准备将中国可移动文物普查网中的所有文物数据爬取到我们手中,并对拿到的数据进行二次加工组装成属于我们自己的文物搜索引擎.由于需要爬取的文物数据很多,有23w左右,所以在本地爬取的技术路线我们放弃,并且想到了通过scrapyinghub进行爬虫托管.

#### 2.scrapyinghub介绍

scrapyinghub地址:
>https://scrapinghub.com/

Scrape Cloud是scrapy基于云的Web爬行平台，可以轻松部署抓取工具并按需扩展，无需担心服务器，监控，备份或cron作业。他帮助开发人员每月将超过20亿个网页转化为有价值的数据.
![](http://upload-images.jianshu.io/upload_images/5870138-eac5324bf0a707e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 3.实现流程
**3.1部署scrapy到cloud**
有梯子的同学可以看官网的教程
>https://helpdesk.scrapinghub.com/support/solutions/articles/22000201028-learn-scrapy-video-tutorials-


首先我们在scrapyinghub上注册一个账号并且登录到管理控制台,点击右上方的`create project`按钮新建一个项目


![](http://upload-images.jianshu.io/upload_images/5870138-22e0037c245722f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
当然了,我们只是将先有的scrapy爬虫部署到云上去,所以我们选择**bulid spider with scrapy**

![](http://upload-images.jianshu.io/upload_images/5870138-5241a02e39d05dbe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后我们将视线转移到我们的服务器上,首先安装`shub`

    pip install shub

![](http://upload-images.jianshu.io/upload_images/5870138-2273adad865cd48a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装成以后,在服务器上登录我们的scrapy cloud

    shub login

![](http://upload-images.jianshu.io/upload_images/5870138-9e3310efc9202b0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
提示输入API key ,我们复制命令行中出现的网址并且进入

![](http://upload-images.jianshu.io/upload_images/5870138-ea111aa75f8704d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
复制其中的API key到shell中


![](http://upload-images.jianshu.io/upload_images/5870138-5e47095256aafdd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

成功!
这个时候我们就需要使用`shub deploy`命令将本地的scrapy上传到cloud

    shub deploy

![](http://upload-images.jianshu.io/upload_images/5870138-4e08bcd47baf76e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由于我们之前操作过,所以自动将`projectID`填入了.当然了,如果我们是第一次使用的话只需要在我们cloud的控制台的URL中找到`projectID`并输入到shell中即可

![](http://upload-images.jianshu.io/upload_images/5870138-afd1cab581d5aae6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上传scrapy成功!
![](http://upload-images.jianshu.io/upload_images/5870138-11a749b950dba41b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个时候我们就可以在cloud上运行我们的spider了!在`Dashborad`选项卡中的右上方点击`run`,我们就可以愉快的选择我们的spider啦!

![](http://upload-images.jianshu.io/upload_images/5870138-f8d59d6bfbaa7690.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**3.2爬取优化方案**
对于23W的数据,我们在之前的初步爬取中按照当前cloud的爬取速度我们预估要24个整天才能爬完,这个速度我们显然是不能接受的.
于是我们优化爬取方案为**将当前爬虫分解成10个爬虫对网站进行爬取**.这种方法也有隐患**:过多的爬虫会导致网站服务器压力倍增,很可能导致其网站瘫痪**.
所以,考虑到这个情况,我们**将爬虫的爬取时间设置成了晚上11点到早上11点.**
优化过后的爬虫方案,将爬取的时间缩短为了4.8天,大大的增加了爬取的效率.

**小Tips:**
>scrapyinghub默认一个project只能同时run一个爬虫,所以如何让多个爬虫同时运行在scrapyinghub以提升爬取效率呢?我们只需要新建多个organization即可,每一个organization都可以run一个爬虫~


![](http://upload-images.jianshu.io/upload_images/5870138-7bae8d1d73fb9c00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/5870138-81cdb352e018578d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
现在我们就有2个正在run的爬虫啦~

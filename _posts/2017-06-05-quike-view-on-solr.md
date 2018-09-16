---
layout:       post
title:        "Solr6.5.1环境搭建+相关命令+索引查询"
subtitle:     "Quick view on Solr 6.5.1"
date:         2017-06-05 14:55:00
author:       "Jagger"
header-img:   "img/title_bar/lianjia-chengdu."
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 索引
    - Solr
---


>最近在研究搜索引擎,准备搭建一套属于自己的搜索APP,用于搜索的数据我已通过scrapy抓到本地了,现在需要一个搜索引擎来跑这些数据.于是选择了基于Lucene的solr来为我做搜索引擎的工作.时至今日最新版的solr为6.5.1,话不多说,开始干吧~

#### 目录
1. solr简介
2. solr环境搭建
3. solr相关命令
4. solr索引查询

***

#### 1.solr简介


![](http://upload-images.jianshu.io/upload_images/5870138-1eff67e382c88da3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**solr 名称来源**
Search On Lucene Replication
**solr 基本概况**
Apache Solr (读音: SOLer) 是一个开源的搜索服务器。Solr 使用 Java 语言开发，主要基于 HTTP 和 Apache [Lucene](http://www.oschina.net/p/lucene) 实现。Apache Solr 中存储的资源是以 Document 为对象进行存储的。每个文档由一系列的 Field 构成，每个 Field 表示资源的一个属性。Solr 中的每个 Document 需要有能唯一标识其自身的属性，默认情况下这个属性的名字是 id，在 Schema 配置文件中使用：id进行描述。
Solr是一个高性能，采用Java开发，基于[Lucene](http://www.oschina.net/p/lucene)的全文搜索服务器。文档通过Http利用XML加到一个搜索集合中。查询该集合也是通过 http收到一个XML/[JSON](http://www.oschina.net/project/search?q=JSON)响应来实现。它的主要特性包括：高效、灵活的缓存功能，垂直搜索功能，高亮显示搜索结果，通过索引复制来提高可用性，提 供一套强大Data Schema来定义字段，类型和设置文本分析，提供基于Web的管理界面等。

#### 2.solr环境搭建
环境准备:
1. Ubuntu 16.04 LTS  
http://releases.ubuntu.com/16.04/
2. Java JDK 1.8.0
http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
3. Solr 6.5.1
http://www.apache.org/dyn/closer.lua/lucene/solr/6.5.1

安装/配置solr:

>1、将下载下来的solr-6.5.1.tgz解压

>2、在*\下新建文件夹solr_home

>3、在solr_home中新建文件夹solr_core

>4、从solr-6.5.1\solr\server\solr下的三个文件copy到solr_home中

>5、将\solr-6.5.1\solr\server\solr\configsets\basic_configs下的conf文件夹copy到solr_home\solr_core中

启动solr


在solr解压目录运行:

    bin/solr start
Solr的默认web管理页面地址为
http://localhost:8983

![](http://upload-images.jianshu.io/upload_images/5870138-a8fa25e8b4d06d1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

***
#### 3.Solr相关命令
查看帮助

    bin/solr -help     

启动start

    bin/solr start  -help    查看start帮助
    bin/solr start           在默认端口启动
    bin/solr start -f        前台启动
    bin/solr start -p 8984   指定端口启动
    bin/solr start -cloud    启动分布式版本
    bin/solr start -e cloud -noprompt         -e表示要启动一个现有的例子，例子名称是cloud，cloud这个例子是以SolrCloud方式启动的
    bin/solr restart         重启项目

新建 core

     bin/solr create -c abc   创建一个名为<abc>的core

post提交数据生成索引

    bin/post -c abc docs/   向名为<abc>的core或collection提交数据，数据源在<docs/>目录中

删除

    bin/solr delete -c abc     删除一个名为<abc>的core或collection

删除索引

    bin/post -c abc  -d "<delete><query>*:*</query></delete>"     删除<abc>core的所有数据
    bin/post -c abc  -d "data to be deleted"       在<data to be deleted>处填写你想要删除的数据
停止solr

    bin/solr stop -all 停止所有端口
    bin/solr stop -p 8080 停止<8080>端口

状态

    bin/solr status 查看当前运行的所有solr端口

当然了,有一些命令在solr的web管理界面也能实现,不管是黑猫白猫,只要能抓耗子的猫就是好猫.

#### 4.solr索引查询
**一.基本查询**

		q     查询的关键字，此参数最为重要，例如，q=id:1，默认为q=*:*，
		fl    指定返回哪些字段，用逗号或空格分隔，注意：字段区分大小写，例如，fl= id,title,sort
		start 返回结果的第几条记录开始，一般分页用，默认0开始
		rows  指定返回结果最多有多少条记录，默认值为 10，配合start实现分页
		sort  排序方式，例如id  desc 表示按照 “id” 降序
		wt    (writer type)指定输出格式，有 xml, json, PHP等
		fq   （filter query）过虑查询，提供一个可选的筛选器查询。返回在q查询符合结果中同时符合的fq条件的查询结果，例如：q=id:1&fq=sort:[1 TO 5]，找关键字id为1 的，并且sort是1到5之间的。
		df    默认的查询字段，一般默认指定。
		qt   （query type）指定那个类型来处理查询请求，一般不用指定，默认是standard。
		indent    返回的结果是否缩进，默认关闭，用 indent=true|on 开启，一般调试json,php,phps,ruby输出才有必要用这个参数。
		version   查询语法的版本，建议不使用它，由服务器指定默认值。


**二. Solr的检索运算符**

		“:”      指定字段查指定值，如返回所有值*:*
		“?”      表示单个任意字符的通配
		“*”      表示多个任意字符的通配（不能在检索的项开始使用*或者?符号）
		“~”      表示模糊检索，如检索拼写类似于”roam”的项这样写：roam~将找到形如foam和roams的单词；roam~0.8，检索返回相似度在0.8以上的记录。
		AND、||  布尔操作符
		OR、&&   布尔操作符
		NOT、!、-（排除操作符不能单独与项使用构成查询）
		“+”      存在操作符，要求符号”+”后的项必须在文档相应的域中存在²
		( )      用于构成子查询
		[]      包含范围检索，如检索某时间段记录，包含头尾，date:[201507 TO 201510]
		{}      不包含范围检索，如检索某时间段记录，不包含头尾date:{201507 TO 201510}


 ***
相关推荐:  
[Windows10下浅识Luke索引](http://www.jianshu.com/p/4ce9919fba79)
[scrapy通过scrapyinghub实现24小时爬虫托管爬取](http://www.jianshu.com/p/90dcdcab5c4d)  
参考链接:  
http://lucene.apache.org/solr/quickstart.html
http://blog.csdn.net/matthewei6/article/details/50620600

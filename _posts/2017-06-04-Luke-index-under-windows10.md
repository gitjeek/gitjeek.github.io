---
layout:       post
title:        "Windows10下浅识Luke索引"
subtitle:     "Luke index under Windows 10"
date:         2017-06-04 19:39:00
author:       "Jagger"
header-img:   "img/title_bar/lianjia-chengdu."
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 索引
    - Solr
    - Luke
---

>最近学校课程在做搜索引擎相关研究,在数据构建索引方面老师推荐了Luke.于是利用周末的时间,快速品尝一下Luke.

***

**目录**  
1.Luke简介  
2.Luke下载/安装  
3.Luke使用  
--3.1 打开Luke/索引信息初始化  
--3.2体验Luke功能  
-----3.2.1索引文件查看  
-----3.2.2索引文件操作  
-----3.2.3索引插件管理  
4.总结  

***

#### 1.Luke简介(摘自Luke/GitHub)

![](http://upload-images.jianshu.io/upload_images/5870138-c3d5724b243c49dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Luke在GitHub的地址:
>https://github.com/DmitryKey/luke


**Luke is the GUI tool for introspecting your Lucene / Solr / Elasticsearch index. It allows:**

* Viewing your documents and analyzing their field contents (for stored fields)
* Searching in the index
* Performing index maintenance: index health checking, index optimization (take a backup before running this!)
* Reading index from hdfs
* Exporting the index or portion of it into an xml format
* Testing your custom Lucene analyzers
* Creating your own plugins!
Search engines luke can deal with


**Search engines luke can deal with:**
* Apache Lucene. Most certainly luke can open lucene index produced by pure Lucene. Do people make pure Lucene these days?
* Apache Solr. Solr and Lucene share the same code base, so it is natural that luke can open Lucene Index produced by Solr.
* Elasticsearch. Elasticsearch uses Lucene as its lowest-level search engine base. So luke can open its index too!

我们的搜索引擎项目是基于solr进行的,Luke可以配合solr进行更多更好更方便的开发工作,看来老师推荐Luke是有道理的!

#### 2.Luke下载/安装

下载和安装也是顺其自然的事

**下载**当然是在github的luke官网:
>https://github.com/DmitryKey/luke/releases

![](http://upload-images.jianshu.io/upload_images/5870138-2c40fc60003a655d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
不同的Lucene版本要对应其相对应的Luke版本,所以在下载Luke的时候要选择合适于自己所安装的Lucene的版本.这里我们选择最新版`6.5.0`

**安装**在指引文档中也有说明:
>1.Clone the repository.
2.Run `mvn install` from the project directory. (Make sure you have Java and Maven installed before doing this)
3.Use [luke.sh](https://github.com/DmitryKey/luke/blob/master/luke.sh) or [luke.bat](https://github.com/DmitryKey/luke/blob/master/luke.bat) for launching luke from the command line based on the OS you are in.

简单的来说,**下载与安装**只需要在GitHub上克隆Luke到本地,然后双击`luke.sh` 或 `luke.bat`启动即可.

#### 3.Luke使用

**3.1打开Luke/索引信息初始化**

在Windows系统下,我们通过双击 `luke.bat`打开Luke的可视化界面:


![](http://upload-images.jianshu.io/upload_images/5870138-ab650d5fb1601207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
系统提示我们写入我们索引的路径.我们可以直接写入之前索引存放的路径,也可以在luke目录下面新建一个文件夹,将之前的索引文件复制过来.由于我们的项目是跑在服务器上的,所以为了方便我们将服务器上生成的索引文件复制一份到我们写入的路径中去.


![拷贝索引文件到目标路径](http://upload-images.jianshu.io/upload_images/5870138-980b92ac8aa77acf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意**:如果路径中没有索引文件的话会报错:
![](http://upload-images.jianshu.io/upload_images/5870138-ee48b792c914c2bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

启动成功!
![启动成功](http://upload-images.jianshu.io/upload_images/5870138-ee56f2c87864e682.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**3.2体验Luke功能**

**索引文件查看**
在luke中,我们能够很方便的查看索引的数量,索引的fields等索引的总体信息,也能够细致入微的查看当前索引中某一个item的具体信息,包括某一个字段的属性/内容/大小等等.

![](http://upload-images.jianshu.io/upload_images/5870138-34a457fd6601b7ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在`overview`选项卡中,我们能够看到`index name`,`number of fields`,`number of terms`等总体信息
也能看到所有的`fields`信息,包括其`name,term count,decoder`等等信息
当然了,我们也能看到索引出来的信息,包括`rank,field`等等.


![](http://upload-images.jianshu.io/upload_images/5870138-2c0c705439a67f2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`Documents`选项卡中,我们也能在选项卡下方看到具体的每一个item所包含的所有fields信息,我们也能点开下方的`Show`按钮查看某一个field的具体信息

![](http://upload-images.jianshu.io/upload_images/5870138-b25edce2ab8d3a20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


***

**索引文件操作**


在Luke中我们也能对索引的items进行操作,包括`增/删/查/改`操作


![](http://upload-images.jianshu.io/upload_images/5870138-670ae3a7b1aa6e02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`add`为`增`操作,
`reconstruct & edit`为`改`操作
`delete`为`删`操作
`browse by term`为'查'操作,当然了在`search`选项卡中有更为具体的`查`操作

![](http://upload-images.jianshu.io/upload_images/5870138-b34c90d7c18eed01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


当然了,至于具体的按钮是怎么操作的我们在这里按住不表,有兴趣的同学自行去尝试一下~
***
**索引插件管理**

在`plugins`选项卡里面,我们能够看到若干个Luke自带的插件


![](http://upload-images.jianshu.io/upload_images/5870138-05a031ec6e402f0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
简单介绍一下这几款自带的插件:
* *Analyzer Tool* : 分词器工具,可以选择分词器并对分词器分词的效果进行展示
* *Hadoop Plugin*: 支持对Hadoop任何文件系统的索引
* *Scripting Luke*: 提供了一个JavaScript的交互式Shell
* *Custom Similarity*: 设计自定义相似性的插件
* *Vocabulary Analysisi Tool*: 一个表示索引词汇增长的工具
* *Zipf distribution*:显示术语流行度发行的工具

***

#### 4.总结
 luke对于solr来说毫无疑问是对其索引功能的丰富.虽然solr有一个自带的web管理页面,但是作为扩展其索引功能的工具Luke,搭配solr我们能够更方便的实现索引的相关操作.
通过luke,我们能够简单直接的查看索引的具体信息;我们也能够在luke上直接对其索引内容进行相关操作;当然了,luke提供了强大的可扩展性功能,能够让我们用户能够拥有自定义的个性化需求.最重要的是,我们能够通过luke对索引的语句进行检验,分析查询效率,对查询进行优化,这显然能够帮助我们构建更好更优秀的搜索引擎.


***
相关推荐
[Solr6.5.1环境搭建+相关命令+索引查询](http://bigjag.top/2017/06/05/quike-view-on-solr/)

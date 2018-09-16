---
layout:       post
title:        "mmseg4j/jieba中文分词包体验，以及词云分析"
subtitle:     "mmseg4j,jieba chinese word segmentation packages"
date:         2017-05-24 12:36:00
author:       "Jagger"
header-img:   "img/title_bar/lianjia-chengdu."
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 中文分词
    - python
    - Solr
---

## 目录
   1. mmseg4j部分（简介，下载，安装，使用，分词算法分析）
   2. jieba部分（简介，下载，安装，使用，分词算法分析）
   3. 词云

***

## 1.mmseg4j
#### 1.1mmseg4j简介
  >mmseg4j用Chih-Hao Tsai 的MMSeg算法实现的中文分词器，并实现lucene的analyzer和solr的TokenizerFactory以方便在Lucene和Solr中使用。 MMSeg 算法有两种分词方法：Simple和Complex，都是基于正向最大匹配。Complex加了四个规则过虑。官方说：词语的正确识别率达到了 98.41%。mmseg4j已经实现了这两种分词算法。

#### 1.2mmseg4j下载
>http://central.maven.org/maven2/com/chenlb/mmseg4j/mmseg4j-core/1.10.0/mmseg4j-core-1.10.0.jar
http://central.maven.org/maven2/com/chenlb/mmseg4j/mmseg4j-analysis/1.9.1/mmseg4j-analysis-1.9.1.jar

#### github地址
https://github.com/chenlb/mmseg4j-core

#### 1.3mmseg4j安装/使用
在这里为了简单的体验mmseg4j,我们采用最简单的本地cmd方法使用：
进入到下载到的mmseg4j-core盘符：

![](http://upload-images.jianshu.io/upload_images/5870138-ee8355ca72cc8506.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>java -cp mmseg4j-core-1.9.1.jar com.chenlb.mmseg4j.example.MaxWord 中华人名共和国


![](http://upload-images.jianshu.io/upload_images/5870138-aa6fcc3f3c1911cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分词成功！

#### 1.4mmseg4j分词算法分析
mmseg4j的匹配算法一共有三种：

- Simple方法
- Complex方法
- MaxWord方法

1、Simple(简单最大匹配)

在一串字符串中从开头匹配子串，找到所有可能的匹配。
>国
国际
国际化

2、MaxWord(复杂最大匹配)：

以三个词为一组
>研_究_生
研_究_生命
研究生_命_起源
研究_生命_起源

3、消除歧义的匹配算法
>简单的最大匹配:选择这个词的最大长度。 复杂最大匹配:选择第一个词块的最大长度。
最大平均单词长度
单词长度的最小方差
最大的语素和自由的单字单词

现在我们来对一段文本尝试用不同的分词算法进行分词：
>5月23日，人机大战第一局在浙江桐乡打响，经过猜先柯洁执黑先行。最终，由Deepmind团队研发的围棋人工智能AlphaGo执白1/4子战胜目前等级分排名世界第一的中国棋手柯洁九段，暂时以1比0领先。双方的第二局比赛将于25日10：30继续进行。DeepMind创始人哈萨比斯：无论结果如何 胜利都属于人类在赛前的演讲中，DeepMind创始人哈萨比斯就表示，并不同意这是人机大赛，而是人利用电脑发现新的知识。




##### MaxWord:

![](http://upload-images.jianshu.io/upload_images/5870138-9bd78617d07d5e48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

错词分析：

      柯洁  -> 柯 | 洁   (人名)
      哈萨比斯 -> 哈 | 萨 | 比 | 斯  (人名)
      对决  -> 对 | 决    (动词)
      等级分 -> 等级 | 分 (常用词)
      人工智能 -> 人工 | 智能   (常用词)
      世界第一 -> 界 | 第  (常用词)
      第二局 -> 第二 | 局  (常用词)

##### Simple:

![](http://upload-images.jianshu.io/upload_images/5870138-ab18d242eccf6e57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

错词分析:

      柯洁  -> 柯 | 洁   (人名)
      第二局 -> 第二 | 局  (常用词)
      哈萨比斯 -> 哈 | 萨 | 比 | 斯  (人名)
***
对比比较发现,在基于所给出文本的分析测试中:
  1.两种算法都不能对于特定的人名进行分词;
  2.基于Simple的算法能够匹配出一些简单的常用词(人工智能/世界第一/等级分),MaxWord算法则不能;
  3.MaxWord甚至会匹配出一些显而易见的错误(对 | 决, 界 | 第).

## 2.jieba
#### 2.1jieba简介
>支持三种分词模式：

>精确模式，试图将句子最精确地切开，适合文本分析；
全模式，把句子中所有的可以成词的词语都扫描出来, 速度非常快，但是不能解决歧义；
搜索引擎模式，在精确模式的基础上，对长词再次切分，提高召回率，适合用于搜索引擎分词。
支持繁体分词

>支持自定义词典

#### 2.2jieba下载/安装
在这里我是在pyhton环境下进行的jieba安装与测试

    pip install jieba


![](http://upload-images.jianshu.io/upload_images/5870138-e38b793854f7efda.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一键式傻瓜操作安装成功

#### 2.3结巴使用
我们通过一串代码来分析:


     import jieba
     import codecs

     jieba.add_word('柯洁')
     jieba.add_word('哈萨比斯')


     with open('word.txt', 'r') as f:
         for line in f:
             seg = jieba.cut(line.strip(), cut_all = False)
             s= '/'.join(seg)
             m=list(s)
             with open('word2.txt','a+')as f:
                 for word in m:
                     f.write(word.encode('utf-8'))
                     #print word

添加自定义词典:

     jieba.add_word('柯洁')
     jieba.add_word('哈萨比斯')

打开我们即将要分词的文件:

     with open('word.txt', 'r') as f:

逐行读取并且采用精确模式(cut_all = False)

    for line in f:
             seg = jieba.cut(line.strip(), cut_all = False)
             s= '/'.join(seg)
             m=list(s)

将分词后的文本保存到目标文本当中去,并且采用UTF-8格式储存:

    with open('word2.txt','a+')as f:
        for word in m:
            f.write(word.encode('utf-8'))
            #print word
测试:


![](http://upload-images.jianshu.io/upload_images/5870138-d019a667c803b918.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们发现,在文本中的'柯洁'和'哈萨比斯'的人名已经被正确的分词!


当然了,结巴提供当中提供的三种分词方式:

      # 全模式
    sl = jieba.cut(strt, cut_all=True)
    print "全模式分词结果:", ",".join(sl)
    print('\n')

    # 精确模式,默认hi精确模式，所以可以不指定cut_all=False
    sl = jieba.cut(strt, cut_all=False)
    print "精确模式分词结果:", ",".join(sl)
    print('\n')

    # 搜索引擎模式
    sl = jieba.cut_for_search(strt)
    print "搜索引擎模式分词结果:", ",".join(sl)

我们这里只采用了第二种精确模式.

## 3.词云分析

  我们采用图悦来进行词云分析:
  >http://www.picdata.cn/

##### 分析结果
![](http://upload-images.jianshu.io/upload_images/5870138-0adc2c9625224e21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 词云展示

![](http://upload-images.jianshu.io/upload_images/5870138-9776eb4c2535e0b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


***
相关推荐
[Solr6.5.1环境搭建+相关命令+索引查询](http://www.jianshu.com/p/b507c499d0b3)
[Windows10下浅识Luke索引](http://www.jianshu.com/p/4ce9919fba79)

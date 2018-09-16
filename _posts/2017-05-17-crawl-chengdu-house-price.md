---
layout:       post
title:        "scrapy抓取成都房价信息"
subtitle:     "Crawl Chengdu house price from lianjia.com"
date:         2017-05-17 11:22:00
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




>最近在学习scrapy爬虫，正好最近成都房价涨的厉害，于是想着去网上抓抓成都最近的房价情况，顺便了解一下，毕竟咱是成都人，得看看这成都的房子我以后买的起不~

***
话不多说，进入正题：
### 任务
抓取链家网（成都）的所有新房以及二手房价格、位置相关信息。

### 实验流程
1. 确定抓取目标.
2. 制定抓取规则.
3. '编写/调试'抓取规则.
4. 获得抓取数据

## 1.确定抓取目标
#### 1.1新房抓取目标

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5870138-a73c7d08cffed97d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们看到在成都楼盘列表页面，有楼盘名字、均价（每平方）和所属行政区，非常好，这正是我们想要抓取的内容！
于是确定目标：
抓取成都新房的所有**楼盘名称、均价信息、所属行政区和URL**信息。

#### 1.2二手房抓取规则



![](http://upload-images.jianshu.io/upload_images/5870138-e94ff38888e00dbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


我们来到二手房的列表页面，并且点击进入二手房详情页面，发现网站所展示的内容就比新房的内容的多得多了，这样我们也能抓取更多的数据了！在这里，我决定在二手房页面抓取**二手房地址、总价、每平米价、面积、所属行政区域和URL**信息。


## 2.制定抓取规则
#### 2.1新房抓取规则

由于新房的信息直接在**新房列表**网页上就能直接抓取，所以只需要在**新房列表**网页分析html结构找到我们想要的数据即可。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5870138-837173ce374f083d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
对于均价来说，我们所需要的信息在  `//div[@class='average']/span/text() `



![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5870138-6c39fdb98b514801.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
对于楼盘名称来说，我们所需要的信息在
`//div[@class='col-1']/h2/a/text()`
当然了，我们所需要的URL也在  `//div[@class='col-1']/h2/a/@href`


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5870138-3de077df819caa2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
对于行政区来说，所需要的信息在 `//div[@class='where']/span/text()`，当然了，我们还得使用正则将所属行政区匹配出来


如何抓取所有的新房信息呢？我们利用下一页来循环

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/5870138-e25d7607aca7b33d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2.2二手房抓取规则
抓取规则和新房抓取规则大同小异，不过这里有一点小小的区别是我们需要**利用二手房列表页面抓取到的URL，进入二手房详情页抓取信息**，不过这一点我们将在代码实现中讲到，在这儿先按住不表，还是继续分析html结构。


![](http://upload-images.jianshu.io/upload_images/5870138-d59f9dcf8953c11f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
小区名称在 `div[@class='communityName']/a[@class='info']/text()`


![](http://upload-images.jianshu.io/upload_images/5870138-727de3f9268c72fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

房总价在 `div[@class='price ']/span[@class='total']/text()`
每平方价在 `div[@class='price ']/div[@class='text']/div/span/text()` ,当然这里我们还是得用正则把两个引号给弄掉~


![](http://upload-images.jianshu.io/upload_images/5870138-44e6c490b666abcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
所属行政区在 `div[@class='areaName']/span[@class='info']/a/text()`的第一个text


![](http://upload-images.jianshu.io/upload_images/5870138-3ddb9dd78e112f77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
面积在 `div[@class='houseInfo']/div[@class='area']/div[@class='mainInfo']/text()`

![](http://upload-images.jianshu.io/upload_images/5870138-d59884a9fc3bca1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当然了，对于循环获得所有信息来说，我们还是用网站的“下一页”来做。



## 3.'编写/调试'抓取规则
为了让调试爬虫的粒度尽量的小,我将编写和调试模块糅合在一起进行.
***
#### 3.1'编写/调试'新房抓取规则
首先我们得定义一个item:
{% highlight python %}
	class NewHoseItem(scrapy.Item):
	    name = scrapy.Field()
	    average = scrapy.Field()
	    district = scrapy.Field()
	    URL = scrapy.Field()
{% endhighlight %}
然后我们尝试抓取一个**新房列表**页面下的所需数据吧~
{% highlight python %}
    import scrapy
    from fangjia.items import NewHouseItem

    class NewsHouseSpider(scrapy.Spider):
        name = "new"
        start_urls = [
            "http://cd.fang.lianjia.com/loupan/pg1/",
        ]

        def parse(self, response):
            for href in response.xpath("//div[@class='info-panel']"):
                item = NewHouseItem()
                item['name'] = href.xpath("div[@class='col-1']/h2/a/text()").extract_first()
                item['average'] = href.xpath("div[@class='col-2']/div[@class='price']/div[@class='average']/span/text()").extract_first()
                item['district'] = href.xpath("div[@class='col-1']/div[@class='where']/span/text()").extract_first().split('-')[0]
                item['URL'] = response.urljoin(href.xpath("div[@class='col-1']/h2/a/@href").extract_first())
                yield item
{% endhighlight %}

测试通过！
现在我们再编写一下循环的规则就ok了！

![](http://upload-images.jianshu.io/upload_images/5870138-97a31c8662563420.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于我们需要获取到totalpage的值和curpage的值，所以我们得对获取到的字符串稍微的处理一下，这里我们用到的是split（）

{% highlight python %}
        next_page = response.xpath("//div[@class='page-box house-lst-page-box']/@page-data").extract_first().split(',')[1].split(':')[1]
        next_page = int(next_page[0])+1
        total_page = int(response.xpath("//div[@class='page-box house-lst-page-box']/@page-data").extract_first().split(',')[0].split(':')[1])
        if next_page <= total_page:
            next_urls = 'pg%s/' % next_page
            next_urls = response.urljoin(next_urls)
            yield scrapy.Request(next_urls,callback = self.parse)
{% endhighlight %}

组装到我们的原函数里面

{% highlight python %}

        import scrapy
        from fangjia.items import NewHouseItem

        class NewsHouseSpider(scrapy.Spider):
            name = "new"
            start_urls = [
                "http://cd.fang.lianjia.com/loupan/",
            ]

            def parse(self, response):
                for href in response.xpath("//div[@class='info-panel']"):
                    item = NewHouseItem()
                    item['name'] = href.xpath("div[@class='col-1']/h2/a/text()").extract_first()
                    item['average'] = href.xpath("div[@class='col-2']/div[@class='price']/div[@class='average']/span/text()").extract_first()
                    item['district'] = href.xpath("div[@class='col-1']/div[@class='where']/span/text()").extract_first().split('-')[0]
                    item['URL'] = href.xpath("div[@class='col-1']/h2/a/@href").extract_first()
                    yield item

                next_page = response.xpath("//div[@class='page-box house-lst-page-box']/@page-data").extract_first().split(',')[1].split(':')[1]
                next_page = int(next_page[0])+1
                total_page = int(response.xpath("//div[@class='page-box house-lst-page-box']/@page-data").extract_first().split(',')[0].split(':')[1])
                if next_page <= total_page:
                    next_urls = 'pg%s/' % next_page
                    next_urls = response.urljoin(next_urls)
                    yield scrapy.Request(next_urls,callback = self.parse)
{% endhighlight %}
测试，成功！

#### 3.2'编写/调试'二手房抓取规则
二手房抓取规则和新房差不多，所以写简单一点，其中主要说明一点是**利用二手房列表页面抓取到的URL，进入二手房详情页抓取信息**。
先定义item：

{% highlight python %}
    class OldHouseItem(scrapy.Item):
        name = scrapy.Field()
        total = scrapy.Field()
        average = scrapy.Field()
        size = scrapy.Field()
        district = scrapy.Field()
        URL = scrapy.Field()
{% endhighlight %}
当然了，我们还是先写出一个**二手房列表**页面的抓取规则，其中，得利用抓取到的URL再进入**二手房详情页**进行抓取。

编写爬虫：

{% highlight python %}
    import scrapy
    from fangjia.items import OldHouseItem

    class OldsHouseSpider(scrapy.Spider):
        name = "old"
        start_urls = [
            "http://cd.lianjia.com/ershoufang/",
        ]

        def parse(self, response):
            for href in response.xpath("//ul[@class='sellListContent']/li"):
            URL = href.xpath("div[@class='info clear']/div[@class='title']/a/@href").extract()
            yield scrapy.Request(URL, callback=self.parse_dir_contents)

        def parse_dir_contents(self, response):
            item = OldHouseItem()
            item['name'] = response.xpath("div[@class='communityName']/a[@class='info']/text()").extract_first()
            item['total'] = response.xpath("div[@class='price ']/span[@class='total']/text()").extract_first()
            item['average'] = response.xpath("div[@class='price ']/div[@class='text']/div/span/text()").extract_first()
            item['size'] = response.xpath("div[@class='houseInfo']/div[@class='area']/div[@class='mainInfo']/text()").extract_first()
            item['district'] = response.xpath("div[@class='areaName']/span[@class='info']/a/text()").extract_first()
            item['URL'] = response
            yield item
{% endhighlight %}

发现有重定向：

![](http://upload-images.jianshu.io/upload_images/5870138-44d715e0bf6bd2b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一看，是robot的原因，于是我们进入 http://cd.lianjia.com/robots.txt 一探究竟
在robots.txt最下面看到
>User-agent:*
Disallow:/

原来我们的scrapy被屏蔽了！
当然，我们还有办法，不过笔者得去吃午饭了！晚上再回来研究研究~

***
相关推荐  
[scrapy通过scrapyinghub实现24小时爬虫托管爬取](http://bigjag.top/2017/06/05/Anchor-text-management-through-php/)  
[scrapy抓取学院新闻报告](http://bigjag.top/2017/05/16/crawl-news-from-sichuan-university/)

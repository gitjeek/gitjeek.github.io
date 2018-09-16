---
layout:       post
title:        "PHP实现自动锚文本管理"
subtitle:     "Anchor text management algorithm"
date:         2017-06-05 9:22:00
author:       "Jagger"
header-img:   "img/title_bar/lianjia-chengdu."
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - 锚文本管理
    - 算法
    - php
---


>前些日子公司有业务需求,需要为其做一个自动锚文本管理的功能.于是前去网上简单的看了看,好像没有现成的轮子可以使用,于是准备自己造一个轮子出来.

***

**目录**
1. 锚文本管理简介
2. 业务需求分析
3. 代码设计
4. 算法优化(未完成)

***

#### 1.锚文本管理简介

>锚文本又称锚文本链接，是链接的一种形式。和超链接类似，超链接的代码是锚文本，把关键词做一个链接，指向别的网页，这种形式的链接就叫作锚文本。
锚文本又称锚文本链接，锚文本实际上是建立了文本关键词与URL链接的关系。

从程序设计的角度来说,我们只需要**搭建一个用于后台管理锚文本的系统**,并且**对需要处理的内容进行锚文本替换**即可.本文我们只介绍**锚文本替换**.

#### 2.业务需求分析
彼时,公司产品部给出的锚文本的属性为:
`关键字(锚)`,`网址URL`,`匹配数量`,`优先级`
锚文本替换的规则为:

1. 文章匹配所有关键字，如果关键字间存在包含重复关系，只取**优先级**最高的关键字
2. **优先级**值大的优先，相同**优先级**值的优先取序号(锚ID)大的进行替换
3. **匹配数量**指每个关键字在内容中进行匹配上后，使用锚点的数量
4. 注意匹配正文只匹配文本内容，如：不用匹配图片的 alt 内容

我们这里给出一个例子来方便理解:

>如有关键字：瑞士（优先级 6，匹配 2 次）、瑞士时间（优先级 8，匹配 1 次）.正文里都出现了瑞士时间的字样，出现了 10 次。这时应用时效果为第一个瑞士时间为锚点，对应的为“瑞士时间”的关键字，第二次、第三次出现的瑞士时间匹配的关键字为“瑞士”，第四次开始出现的关键字均不再做无锚点匹配。

也就是说,规则是根据`优先级`和`匹配数量`匹配替换锚文本和数量,并且不匹配图片的alt内容.  
这里有一个问题出来了,正如上面的例子所说:**如果我首先匹配了'瑞士时间'字样,如何在下次匹配带有重复包含关系的'瑞士'时,避免再次匹配到已经做锚处理的'瑞士时间'呢??**  
这里我思考的策略是,在每一次锚替换时,屏蔽掉所有的`<a>`标签和`<alt>`标签,因为处理了的锚其实是一个超链接形式的`<a>`标签,只需要在锚替换时屏蔽掉即可.  
那么问题又来了:**如何屏蔽`<a>`标签和`<alt>`标签呢?**  
答案是正则:如果我能够写出**在文本中查找除了`<a>`,`<alt>`以外的<匹配数量>个<关键字>**的正则,再使用PHP的`preg_replace`函数即可.但是由于我水平有限,写不出这么复杂的正则,彼时公司产品部催的紧,我只能使用另外一个复杂且效率很低的算法:

1. 正则匹配到所有的`<alt>`标签并将其替换成一个`特殊的标识符`,并将匹配内容存到一个数组
2. 正则匹配到所有的非`<a>`标签的<关键字>,并替换<匹配数量>次
3. 将存有`<alt>`内容的数组根据`特殊的标识符`替换回去

#### 3.代码设计
按照我在上一节设计的规则,我只需要将几个关键正则写出来即可:

*1.正则匹配到所有的`<alt>`标签并将其替换成一个`特殊的标识符`,并将匹配内容存到一个数组:*

    $rule = "/<img.*>/";
    //先把img排除掉,并且将其存为一个数组
    preg_match_all($rule, $str, $matches);
    $str_without_alt = preg_replace($rule, 'Its_Just_A_Mark', $str);

*2.正则匹配到所有的非`<a>`标签的<关键字>,并替换<匹配数量>次*

     //锚处理
    foreach ($anchors as $anchor) {
    $rule = "/".$anchor['anchor_name']."(?!((?!<a\b)[\s\S])*<\/a>)/";
    $href = '<a href="'.$anchor['anchor_url'].'" class = "seo-anchor">'.$anchor['anchor_name'].'</a>';
    $str = preg_replace($rule, $href, $str,$anchor['anchor_num']);
    }

*3.将存有`<alt>`内容的数组根据`特殊的标识符`替换回去*

    //将img加上去
    foreach ($matches[0] as $alt_content) {
	    preg_replace('/Its_Just_A_Mark/',$alt_content,$str,1);
    }

至此,所有的关键点已写出,现在组装:

    /**
		 * Sets the anchors.(There's much space to optimize the efficiency)
		 *
		 * @param      <string>  $str   stirng to be setted.
		 * @param      <array>  $anchors  the anchors selected from db
		 *                      |_<anchor>
		 *                      |_<anchor>
		 *                      |_...
		 *                      |_...
		 *                      ...
		 * @param      <array> 	$anchor   the anchor selected from db
		 *                      |_@anchor_url	<string>	anchor's url
		 *                      |_@anchor_name   <string>	anchor's name
		 *                      |_@anchor_num	<int>	   number of which anchor to be setted
		 * @return     <string>  setted string
	*/
	public function setAnchors($str,$anchors)
	{
		$rule = "/<img.*>/";
		//先把img排除掉,并且将其存为一个数组
		preg_match_all($rule, $str, $matches);
		$str_without_alt = preg_replace($rule, 'Its_Just_A_Mark', $str);
		//锚处理
		foreach ($anchors as $anchor) {
			$rule = "/".$anchor['anchor_name']."(?!((?!<a\b)[\s\S])*<\/a>)/";
			$href = '<a href="'.$anchor['anchor_url'].'" class = "seo-anchor">'.$anchor['anchor_name'].'</a>';
			$str = preg_replace($rule, $href, $str,$anchor['anchor_num']);
		}
		//将img加上去
		foreach ($matches[0] as $alt_content) {
			preg_replace('/Its_Just_A_Mark/',$alt_content,$str,1);
		}
		return $str;
	}

大功告成!


#### 4.算法优化
(未完成,敬请持续关注)

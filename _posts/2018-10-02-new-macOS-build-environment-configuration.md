---
layout:       post
title:        "macOS新机配置工具清单"
subtitle:     "new macOS build environment configuartion"
date:         2018-10-02 10:52:00
author:       "Jagger"
header-img:   "img/title_bar/macOS.png"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - macOS
---

>研究生的生活逐渐稳定，为了迎接接下来的学习，也将手中的电脑更新至18款的MacBookPro 13。第一次接触macOS，难免有点生理不适。工欲善其事，必先利其器，经过一段时间的打磨之后，也逐渐顺手起来。  
>前人树下好乘凉，笔者的开发环境也是借鉴各位前辈的经验分享一步一步搭建起来的。所以本文充其量来说，只是做一个经验的整理。继承前人们分享的精神，我也将电脑上的工具，配置，以及一些小tips分享给大家（列表附有强烈个人倾向，欢迎提出意见）。

#### 目录
1. 日常软件
2. 开发工具
3. 奇技淫巧（系统增强）
4. Tips

***

## 日常

1. [网易邮箱大师](https://mail.163.com/dashi/)
2. [网易云音乐](https://music.163.com/#/download)，关于海外用户听不了的同学，请戳这里👉🏻
3. [chrome](https://www.google.com/chrome/)
4. [微信](https://weixin.qq.com/)
5. [印象笔记](https://www.yinxiang.com/download/)
6. [QQ](http://im.qq.com/macqq/)
7. [iPic](https://toolinbox.net/iPic/) ，图床神器，搭配个人站上传图片使用
8. [百度网盘](https://pan.baidu.com/download#pan)
9. [Microsoft 365](https://www.microsoft.com/en-us/microsoft-365)
10. [MacDown](https://macdown.uranusjr.com/)，Atom上有MD撰写的插件，不过感觉没这个好用
11. [网易有道词典](http://www.youdao.com/)
12. [Xmind](https://www.xmind.cn/)，思维导图
13. [OmniGraffle](https://www.omnigroup.com/omnigraffle/)，Visio替代品
14. [番茄土豆](https://pomotodo.com/#apps)，番茄工作法，让你知道其实你一天的工作效率其实低的可怜
15. [OneDrive](https://onedrive.live.com/about/zh-hk/download/)，微软云的桌面版，可以把仓库克隆到Finder里面实现本地无缝云同步

## 开发工具

1. [Dash](https://kapeli.com/dash)，离线api文档，搭配aflred食用体验更佳（后文有aflred）
2. [Postman](https://www.getpostman.com/)，接口测试工具
3. [iTerm2](https://www.iterm2.com/)，替换原生终端
4. [Oh My zsh](https://github.com/robbyrussell/oh-my-zsh)，zsh扩展，留坑待补充
5. [brew](https://brew.sh/)，mac的包管理工具，类似于apt-get一套，开发环境配置不用太简单
6. [tmux](https://github.com/tmux/tmux)，shell多窗口管理，终端断开延续，终端历史共享等（[Oh My Tmux!](https://github.com/gpakosz/.tmux)tmux配套插件，酌情选择使用）
4. [Xcode](https://developer.apple.com/xcode/)，特别说明，mac的一些系统扩展需要xcode支持，必须下载xcode才能使用
5. [Docker](https://www.docker.com/products/docker-desktop)，容器盒子
6. [Atom](https://atom.io/)，GitHub官方IDE，对git有很好的支持，感觉都不会使用sourceTree了
7. [SourceTree](https://www.sourcetreeapp.com/)，对，就是这个软件，Git的GUI管理软件
8. [Navicat](https://www.navicat.com/en/)，数据库管理GUI

## 奇技淫巧（系统增强）
1. [Aflred](https://www.alfredapp.com/)，WorkFlow神器，谁用谁知道
2. [TotalFinder](https://totalfinder.binaryage.com/)，Finder增强，多标签页管理Finder
3. [Dr.Cleaner](https://www.drcleaner.com/)，内存监控，系统优化
4. [Proxifier](https://www.proxifier.com/) ，网络代理软件，支持对单一软件的强制代理（网易云翻回墙听歌必备软件之一）
5. [ShadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG)，ss梯子（网易云翻回墙听歌必备软件之二）
6. [Bartender](https://www.macbartender.com/)，菜单栏管理软件，给你一个简洁优雅的菜单栏
7. [Moom](https://manytricks.com/moom/)，窗口大小管理，谁用谁知道
8. [iStat Menus](https://bjango.com/mac/istatmenus/)，菜单栏系统监控（内存，网速，磁盘，电池.....)
9. [PopClip](https://pilotmoon.com/popclip/)，划词增强工具，包括但不限于（上百个官方扩展）：复制，粘贴，翻译，定制搜索（淘宝，知乎，google...）
10. [ProxyeeDown](https://github.com/proxyee-down-org/proxyee-down)，底层 HTTP 高速下载器，支持百度云不限速下载
11. [Disk Drill](https://www.cleverfiles.com/cn/)，数据恢复，磁盘数据安全
12. [Mounty](https://mounty.app/)，NTFS格式U盘的完全读写
13. [Qbserve](https://qotoqot.com/qbserve/)，时间监控，记录你在电脑上花费的时间
14. [Scroll Reveser](https://pilotmoon.com/scrollreverser/)，鼠标（触摸板）控制滚动翻转，让mac上鼠标滚轮方向不再反人类
15. [cDock](https://sourceforge.net/projects/cdock/)，Dock栏增强，几乎你能想到的它都有，不过目前版本1.13部分功能在macOS 10.14中失效了
16. [AppCleaner](https://freemacsoft.net/appcleaner/)，超级小，超级好用的app删除工具

## Tips（效率提升）
1. 大写键和control键交换一下。contorl键绑定了很多的快捷键，交换了之后可以打开快捷键世界的大门。![](https://ws1.sinaimg.cn/large/006tNc79gy1fvtx9y5192j31100vytnh.jpg)
2. 屏幕触发角设置，提高鼠标党和单手党的工作效率。![](https://ws1.sinaimg.cn/large/006tNc79gy1fvtxb9c09lj317c0z6dud.jpg)
3. 微信唤醒可以实现两个键的全局快捷键，简单方便。![](https://ws1.sinaimg.cn/large/006tNc79gy1fvtxd46vfsj30u40mcdnd.jpg)
4. 全局应用唤醒可以使用‘自动操作’通过键盘的service绑定实现（貌似最少需要绑定三个按键）![](https://ws3.sinaimg.cn/large/006tNc79gy1fvtxl7czdfj31kw1a5h7j.jpg)![](https://ws1.sinaimg.cn/large/006tNc79gy1fvtxmbdfs2j317c12ik6f.jpg)

想到再补充～欢迎提供意见和建议～

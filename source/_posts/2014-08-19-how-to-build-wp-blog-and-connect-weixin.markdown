---
layout: post
title: "如何免费搭建WordPress博客并连接微信公众账号"
date: 2014-08-19 23:31:08 +0800
comments: true
categories: [WeChat]

keywords: WeChat, SAE, 新浪云计算, WordPress, 微信公众账号
description: 如何在SAE免费搭建WordPress博客，并连接微信公众号.
---
WordPress由于便捷的安装，丰富的插件，一直受到广大站长的喜爱。我的第一个独立博客也是用WordPress搭建，尽管后来改用Octopress平台了。

微信公众平台的影响力日益扩大，作为尝试，我也申请了一个账号(插播广告：欢迎扫描屏幕下方二维码关注本账号...只要998...)。

同时作为一个爱折腾的人，自然要开启微信的开发者模式。作为没有验证的订阅号，开发者暂时只能做一些信息接收和回复操作。

即使开发的权限不多，但是结合WordPress博客后也可以实现不少功能，比如发送"最新"，返回博客的最新文章等等。当然首先，你得有个博客。

<!--more-->

[SAE](http://sae.sina.com.cn/)是新浪提供的云计算服务，支持PHP和JAVA，而且资费相比阿里云什么的要便宜很多，用来搭建WordPress非常合适。

现在注册并且实名认证还送2500左右的云豆，对于数据库小、流量低的站点来说，可以免费用上个大半年。

下面讲讲大致流程。

__SAE WordPress 安装__

注册SAE之后，如果有建立WordPress站点的经验可以手动安装。或者和我一样选择easy模式，选择“导入应用”，将最新的[安装包](wp4cloud.sinaapp.com)链接填到基于URL安装，然后填写应用名称等进行安装。

接下来就是进入你设定的地址xxx.sinaapp.com进行管理员注册等。

嗯，有默认的markdown插件真幸福。

__安装主题或插件__

SAE不支持直接搜索安装主题和插件，只能通过SVN(我靠，这又是啥...)来进行上传。SVN的使用方法请看SAE[官方文档](http://sae.sina.com.cn/doc/tutorial/helloworld.html)... 

从WP官网或其他途径下载主题或插件，放在本地的wp-content/themes or plugins的文件夹里，然后svn add & commit提交到服务器。接着就可以进入WP后台安装了。

__博客优化__

主要是速度方面的。我暂时对以下两个方面进行了优化。

* gzip

对页面进行压缩。进入SAE应用管理，在AppConfig里选择页面压缩，把所有的文件类型都添加进去。使用站长工具进行检测，运用后大约压缩了50%.

* fonts

由于众所周知的原因，国内访问不了谷歌，然后包括WP在内的绝大多数平台或框架都采用了谷歌的公共库和字库，这会导致严重延时。好在360提供了[镜像](libs.useso.com)以方便国内开发者使用。

需要更新的文件有：

1. wp-includes/script-loader.php
2. wp-includes/js/tinymce/themes/advanced/skins/wp_theme/dialog.css
3. wp-content/themes/你的主题名/functions.php

使用编辑器将上述文件中的‘googleapis.com’全部替换成‘useso.com’。

__连接微信接口__

我最初采用的方式是创立一个新的应用，用来接收和返回微信信息，同时通过链接的方式引导到博客。但是因为我是只能勉强写"Hello World"的小白，PHP这货基本不懂，只能套用简单的SDK进行修改。

后来我选择放弃建造轮子，倚靠前人的肩膀，怒装[weixinpress](http://www.houqun.me/bbs/forum.php?gid=1)插件... 安装之后在WP后台可以轻松添加修改自定义回复。

然后微信的URL填写'xxx.sinaapp.com/?weixin'，设置token一致，就OK了。

接下来如有时间，我会考虑小打小闹，在此基础上添加些其他的功能。小黄鸡，神经猫什么的就算了吧...

欢迎关注下方微信公众账号进行测试或交流：

![img](http://cyztalk-wordpress.stor.sinaapp.com/uploads/2014/08/qrcode-150x150.jpg)

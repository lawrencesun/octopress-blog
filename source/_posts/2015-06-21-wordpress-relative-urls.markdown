---
layout: post
title: "外网如何访问内部WordPress网站"
date: 2015-06-21 10:08:42 +0800
comments: true
categories: WordPress
keywords: wordpress, relative urls, multiple domains, 外网访问内部, 相对路径
description: 
---
之前在内部服务器搭建了一个WordPress网站，测试时使用的时Localhost，在内网上线时，将主机地址改为服务器的ip地址，即10.xxx.xxx.xxx。显然这种情况下，外网是不能访问的。

WordPress默认使用的是绝对路径，也就是说文章和图片的地址都是10.xxx.xxx.xxx/...。除此之外，js和css的路径也是绝对的。这样即使外网通过外网ip访问到了网站，内容和样式都是无法加载的。

<!-- more -->

首先想到的一个方法就是在WP后台或者数据库中将SiteURL改为外网的ip地址。但是这种方法相对暴力，也不利于扩展多域名/ip的指向。

在网上搜到了一个方法，具体如下:

在`wp-config.php`文件中加入以下两行代码：

```
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
```

这样无论你是从内网还是外网访问，主机和链接地址都是随之动态改变的。

至于文章中的图片和链接，还需要改成相对路径，如/?p=xxx和/wp-uploads/...等。

最后一步，也是网上很多经验贴没有说明的，就是清除缓存。我按照上述步骤完成之后，多次测试都没有成功，外网访问时有些地址还是显示内网ip。删去了`wp-config.php`中的`define( 'WP_CACHE', true );`就好了。
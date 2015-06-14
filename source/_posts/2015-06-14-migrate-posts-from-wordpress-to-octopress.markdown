---
layout: post
title: "从Wordress迁移文章到Octopress"
date: 2015-06-14 17:17:43 +0800
comments: true
categories: [WordPress, Octopress]
keywords: wordpress, octopress
description: 
---
奸商_Godaddy_发邮件说不再提供免费主机(当年买域名时所赠)并要求尽早迁移，使得我不得不打开沉睡已久的老博客，将之前的博客也搬家到Octopress。

搬家自古以来就是件头疼的事情，当年把人人网上近百篇日志，以纯手工的方式，复制黏贴到WordPress上的悲惨经历仍历历在目。

好在从WordPress迁移到Octopress的工作已经有无数前辈进行了摸索，并制造了自动化工具。

<!-- more -->

首先进入WP后台对内容进行导出，下载生成的xml文件，命名为"wordpress.xml"并放在Octopress的根目录下面。

接下来下载[这个程式](https://gist.github.com/1394128)并放到新建的utils目录下。

先运行`gem install hpricot`，然后运行 `ruby -r "./utils/wordpressdotcom.rb" -e "Jekyll::WordpressDotCom.process"`。

命令窗口提示import了169篇文章，这些文章可以在source/_posts目录里看到。

如果你在WordPress里用的是和我一样的?P一类的路径，或者说别名和标题一样也是中文，那么很不幸，需要将别名改成英文。当然也有现成的转化工具可以用来实现，但我最终还是手动翻译了。

后续还有不少格式、图片链接的问题(懒得处理了...)，但是基本内容已经搬运成功了。

<sub>参考: [Octopress搬家记][1]，[从Wordpress迁移到Octopress][2]</sub>

[1]: http://blog.yorkxin.org/posts/2011/11/26/import-from-wpcom-to-octopress/

[2]: http://blog.dayanjia.com/2012/04/migration-to-octopress-from-wordpress/
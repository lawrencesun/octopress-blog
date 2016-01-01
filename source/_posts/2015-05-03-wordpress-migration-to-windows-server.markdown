---
layout: post
title: "WordPress迁移至Windows主机"
date: 2015-05-03 10:56:59 +0800
comments: true
categories: WordPress
keywords: WordPress, Windows Server, Web Platform
description: 
---
去年底接了一个建立网站的小项目，考虑到该网站偏向于新闻发布，对其他功能要求不高，所以使用WordPress搭建是一个比较便捷的方式。

本机测试是在_Ubuntu_的虚拟机环境下，但是网站最终需要搭建在_Windows Server 2012 R2_的虚拟主机上。

> Windows Server 环境配置

<!-- more -->

WordPress需要PHP/MySQL等环境，当然可以自己选择相应版本安装，但是很容易出错。我使用的是Web Platform Installer的傻瓜式安装。

打开Web PI，搜索WordPress并点击安装。在安装MySQL时需要设置root的密码，这里需要记一下，后面有用。Web PI随后会自行下载和安装所需要的环境。然后就是站点配置，包括物理地址、主机和ip地址等。

接下来就是常见的WP安装步骤。

> WordPress 数据迁移

通过phpMyAdmin导出旧的数据库, 将wp-content文件夹拷贝上传至新主机并在新主机上导入新的数据库。修改`wp-config.php`中DB_NAME, DB_USER, DB_PASSWORD, DB_HOST，更新为新主机的记录。


> 安全性

主要是定期数据备份，选择相对强的密码，以及ip限制。

在IIS的界面里有限制访问的功能，最好是对wp-admin文件夹设置一个仅限内网ip访问的权限控制。
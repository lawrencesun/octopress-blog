---
layout: post
title: "华为路由器开启Web登陆"
date: 2014-11-29 20:04:14 +0800
comments: true
categories: Devices
keywords: router, Huawei, web
description: 
---
对于一台新的路由器来说，默认ip地址一般式是（192.168.1.1)。可以用一台PC连接路由器的任意lan口，并将PC的本地ip地址设为(192.168.1.x)(x一般取0~15，不为1)，掩码(255.255.255.0)，网关(192.168.1.1)。

但是昨天试图通过Web端来配置华为AR1220-S路由器的时候，一直无法连接。

在华为技术支持和网络资源的帮助下，发现原因可能是原厂默认下无法实现Web登陆。

最终是通过Console接口初始了路由器的ip并打开了Web登陆的功能。

具体步骤如下:

<!-- more -->

1.Console线连接后，使用xp下的超级终端新建一个连接，进入console端界面。

```
<Huawei>system-view  //进入系统视图
[Huawei]interface gigabitethernet 0/0/0  //进入接口视图
[Huawei-GigabitEthernet0/0/0]ip address 192.168.1.1 24 //在该接口配置管理ip地址
[Huawei-GigabitEthernet0/0/0]display ip interface brief  //查看配置情况
[Huawei-GigabitEthernet0/0/0]aaa  //进入AAA视图
[Huawei-aaa]local-user _user-name_ password cipher _password_
	 					local-user _user-name_ privilege level _level_
	 					local-user _user-name_ service-type http
	 					// 配置用户名、密码、权限以及访问类型
```

2.执行quit, 返回系统视图。

3.此时PC端连接gigabitethernet0/0/0接口，确认可以ping通路由ip。

4.会发现还是不能登陆Web端，因此需要在Console端输入http server enable

通过以上步骤，就可以进入Web端界面并根据需求对路由器进行配置了。
---
layout: post
title: "Flask应用环境搭建"
date: 2016-02-13 12:52:51 +0800
comments: true
categories: Flask
keywords: flask, python
description: 
---
去年奶奶和我说，每天用手机自带的记事本来记录某一时刻的血压、心率，在排版
查找和分析时比较麻烦，问我能不能做一个专门的APP来记录。这事一直拖到过年，因为年夜饭、拜年、同学会这些事，时间打得比较散，比较适合做这个。

因为功能需求不多，所以考虑部署在几近免费的[新浪云](http://t.cn/R48mxld)。SAE支持PHP, Python和JAVA，考虑到以后可能会用Python，于是毅然放弃了世界上最好的语言。

Python的Web框架很多，最终选择了据说是比较轻的Flask。乍一看，也是和Rails类似的MVP结构，但是社区活跃度和支持度显然不是很高，遇到坑的时候往往需要花上不少时间去搜索。

本机环境选择了Ubuntu，自带python 2.7, 需要配置MySQL.

<!-- more -->

* 为了使用easy_install, 安装python setuptools, 一组Python的工具包

`$ wget https://bootstrap.pypa.io/ez_setup.py -O - | sudo python`

* 安装virtualenv

`$ sudo easy_install virtualenv`

* 创建一个项目并启用用virtualenv创建一个新的环境

```
$ mkdir myproject
$ cd myproject
~/myproject$ virtualenv venv
~/myproject$ . venv/bin/activate
```

* 安装Flask

`~/myproject$ pip install Flask`


* 在venv环境下安装flask-sqlalchemy

`pip install flask-sqlalchemy`

* 创建Flask应用

```
~/myproject$ mkdir app
~/myproject$ mkdir app/static
~/myproject$ mkdir app/templates
~/myproject$ cd app
```

* 在app创建`config.yaml`, `index.wsgi`, `myapp.py`等

```
~/myproject/app$ touch config.yaml
~/myproject/app$ subl config.yaml
```

* MySQL安装配置，可以参考[这篇](http://shmilyaw-hotmail-com.iteye.com/blog/1689160)

先确认是否安装MySQL

`$ mysql --version`

可能会遇到的问题：

`pin install mysql-python` 出现 `mysql_config not found`

solution: `sudo apt-get install libmysqlclient-dev`

出现`command 'x86_64-linux-gnu-gcc' failed with exit status 1`

solution: `sudo apt-get install python-dev`

* python在使用，经常会出现indentation的问题，如果使用Sublime Text的话可以参考[这篇](http://stackoverflow.com/questions/14773271/how-to-fix-convert-space-indentation-in-sublime-text)

* 测试

```
from flask import Flask, g, request

app = Flask(__name__)
app.debug = True

@app.route('/')
def hello():
    return "Hello, world! - Flask"

if __name__ == '__main__':
    app.run()
```

`~/myproject/app$ python myapp.py`

打开127.0.0.1:5000，出现"Hello, world! - Flask"






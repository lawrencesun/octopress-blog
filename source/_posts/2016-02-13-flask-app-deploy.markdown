---
layout: post
title: "Flask应用SAE部署"
date: 2016-02-13 12:53:04 +0800
comments: true
categories: Flask
keywords: flask, python, sae
description: 
---
如何Build一个Flask应用可以参考Flask[官方文档](flask.pocoo.org)，The Flask Mega-Turorial和Flask Web Development一书。值得一提的是，许多教程使用的第三方库都是比较早的版本，有一些用法会稍有不同，大多数问题都是因为版本不同造成的。

最终按照上述的几个资源，搭建了一个基本满足需求的[Web应用](meddata.applinzi.com)，因为时间不多，对验证和国际化用了简单粗暴的方法。

![img][img1]

<!-- more -->

SAE是支持扩展第三方库的，但同时预先也安装了不少，因此需要配置与本地测试相同的环境，包括安装扩展库和指定版本。SAE的python文档都有一些具体的说明，但是排版比较散乱。

__打包本地环境__

```
Venv/lib/python2.7/site-packages
zip -r site-packages.zip .
```

将压缩包拷贝到项目目录下

__配置第三方目标__

即`config.yaml`文件，具体可参考SAE官方文档或我应用中提供的GitHub链接。

__配置index.wsgi__

```
import os
import sys

root = os.path.dirname(__file__)

# sys.path.insert(0, os.path.join(root, 'site-packages'))
sys.path.insert(0, os.path.join(root, 'site-packages.zip'))

import sae
from myapp import app, db
db.create_all()
application = sae.create_wsgi_app(app)
```

__使用Git上传SAE__

```
~/myproject/app$ git inti
~/myproject/app$ git add .
~/myproject/app$ git commit -m "message"
~/myproject/app$ git remote add sae https://git.sinacloud.com/AppName
~/myproject/app$ git push sae master:1
```

__本地和SAE数据库切换__

```
try:
  import sae.const
  db = sae.const.MYSQL_DB
  user = sae.const.MYSQL_USER
  pw = sae.const.MYSQL_PASS
  host = sae.const.MYSQL_HOST
  port = int(sae.const.MYSQL_PORT)
except ImportError:
  pass
```

__可能出现的问题__

- “OperationalError: (1045, 'access deny')”

solution: 需要在SAE初始化MySQL数据库。

- “this is MySQLdb version (1, 2, 5, 'final', 1), but _mysql is version (1, 2, 3, 'final', 0)”

solution: SAE上的版本低，删除site-packages下的mysqldb和mysql-python

- “OperationalError: (OperationalError) (2006, 'MySQL server has gone away')”

solution: 因为sqlalchemy引起，在每次`session.commit()`之后加上`session.close()`，关闭当前session，将mysql连接还给连接池；并
在config.py里加上`SQLALCHEMY_POOL_RECYCLE = 10`

[img1]: http://7xj95q.com1.z0.glb.clouddn.com/meddata.png?imageView2/2/w/400/h/300/q/85|watermark/2/text/TGF3cmVuY2UgU3Vu/font/YXJpYWw=/fontsize/200/fill/I0VGRUZFRg==/dissolve/56/gravity/SouthEast/dx/10/dy/10
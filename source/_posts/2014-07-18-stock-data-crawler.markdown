---
layout: post
title: "Ruby程序:自动抓取股票财务数据"
date: 2014-07-18 21:03:55 +0800
comments: true
categories: Ruby
keywords: ruby, stock, crawler, 股票数据抓取
description: 自动抓取股票财务指标数据
---
无论是投资还是投机股市，股票的财务状况仍然是不可或缺的参考指标。传统金融网站上的数据往往以个股为单位呈现，导致数据的查询、对比和分析比较困难。如果要进行全局扫描和分析，可能就需要把这些数据一个一个下载后进行处理，这方面的时间成本略高。

前段时间在雪球偶然看到有人用Python做了一个自动获取数据的程序，觉得挺有趣，对大量数据的计算和分析可能有用，所以用了一天左右的时间用Ruby写了一个类似的代码。

<!-- more -->

数据来源于网易财经，理论上可以实现所有股票的任意财务数据的抓取，并导入csv文件。例如所有股票的”速动比率“（尽管不懂...），如下图所示。

![img](https://lh6.googleusercontent.com/-kE3w3S0vS_0/U8pK6OB_l0I/AAAAAAAAAsw/cAY1SvsE8mg/w400-h360-no/data.png)

核心代码如下:

```ruby
# 检测网页是否存在
def valid_url(url)
	url = URI.parse(url)
	req = Net::HTTP.new(url.host, url.port)
	res = req.request_head(url.path)
	return true if res.code == '200'
end

# 抓取数据extract_data
def extract_data(stock_num, index, url)
	# 数据来源为网易股票频道，按报告期的主要财务指标，默认显示为6个季度
	source = Nokogiri::HTML(open(url))
	# 获取股票名称
	name_table = source.search("//h1[@class='name']//a")
	name = name_table[0].text
	# 搜寻所有属于指定css class的值，返回的是一个Array
	data_table = source.search("//table[@class='table_bg001 border_box fund_analys']//td")
	# 查找Array中含有指定文字描述的元素，并返回元素的地址
	......

	# 上一步查找到的元素地址后面的六个元素含有所需数据，将其中的数字部分提取出来
	# 并加入到一个新建的Array中去
	......

	# 返回数据
	s
end 
```
目前的主要问题是运行速度较慢，以后有时间看看能否改进。不管如何，在此数据的基础上做一些计算、对比和分析应该容易不少。
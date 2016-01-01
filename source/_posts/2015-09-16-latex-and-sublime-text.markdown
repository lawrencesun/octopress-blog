---
layout: post
title: "LaTex环境配置: Sublime Text + TeXLive + Sumatra PDF"
date: 2015-09-16 19:51:48 +0800
comments: true
categories: LaTex
keywords: LaTex, Sublime Text, Sumatra PDF, TeX Live
description: 
---
前一阵子看到LaTex生成的PDF，感觉排版和公式特别酷炫。当然相较Word来说，无论是环境配置还是代码输入都比较复杂，但如果学会使用的话，绝对是写论文的利器。

在知乎大神的推荐下，选择了比较熟悉的Sublime Text 3，配以TeXLive和Sumatra PDF，作为我在Win10系统下的LaTex环境配置。在设置ST的LaTexTools时出现不少问题，几番搜索终于配置成功。

<!-- more -->

__TeXLive__

直接官网下载，安装。考虑到国内镜像文件下载速度不稳定，推荐下载iso。

__Sumatra PDF__

轻巧的PDF阅读器，也是官网下载，安装。

__LaTexTools__

是Sublime Text的一个插件。

1. 如果之前安装过Package Control可以跳到下一步。Ctrl+Shift+P打开命令输入行，输入install，选择Package Control: Install Package，回车安装。也可以去ST官网下载该文件直接安装。

2. 输入install package，选择LaTexTools进行安装。

__配置以及双向搜索__

输入Ctrl+Shift+P，运行Reconfigure and migrate settings。

打开Preferences > Browse Packages... 选择User文件夹中的LaTexTools.sublime-settings

```
"Windows": {
		//这里修改成texlive的路径
		"texpath": "C:\\texlive\\2015\\bin\\win32;$PATH",
		//这里修改成texlive
		"distro": "texlive",
		//这里修改成SumatraPDF.exe所在路径，留空的话需要将Sumatra所在路径添加到系统环境变量中去
		"sumatra": "C:\\...路径...\\SumatraPDF.exe"
},
```
这里就连接了TexLive和SumatraPDF，编译完成后会自动打开SumatraPDF。

打开SumatraPDF > 设置 > 设置反向搜索命令行

修改成Sublime Text的路径, "C:\ ...路径...\sublime_text.exe" "%f:%l"

新建一个tex文件，保存后Ctrl+B进行编译，如果没有错误，就会打开SumatraPDF，同时在PDF文档中双击某一处时，就会返回tex文件中相互对应的位置。

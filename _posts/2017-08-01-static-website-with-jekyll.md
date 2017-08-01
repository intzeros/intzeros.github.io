---
layout: post
title:  "从WordPress到静态网站"
date:   2017-08-01 12:00:00
categories: coding4fun
---

[TOC]

作为一个极简主义者，越来越受不了WordPress的臃肿。主要自己平时都是用Markdown来做记录，而WordPress支持的不是很好，另外有时写的md长文通过三方软件导出的html也不造放在wp的什么地方才合适，管理起来还不如几个静态页面来得方便。于是有了某天来个大整顿的想法。

对前端只停留在css的我，花了整三天时间，从Hexo到Jekyll再到Bootstrap，以及各种markdown2html解析器...最后基本算是从头写了一个基于Jekyll的website theme 😥

# Hexo

主流的静态页面生成器有俩，一个Jekyll，一个Hexo，都支持Markdown。 前者用的是Ruby，后者为Node.js。

Hexo会比Jekyll搭建起来更方便一些，而且各种配置项的设计也很便捷，网站themes还很丰富，所以我一开始弄的就是这个。

* [Hexo官方文档](https://hexo.io/zh-cn/docs/)
* [next](https://github.com/iissnan/hexo-theme-next) : 一款优雅并且功能强大的主题. [[doc]](http://theme-next.iissnan.com/getting-started.html) [[demo]]([http://notes.iissnan.com](http://notes.iissnan.com/))

搭建方法上面这两个链接写的很详细。

这里主要记录一下让Hexo支持LaTex的注意地方。Hexo用MathJax时会有一些[转义问题](http://2wildkids.com/2016/10/06/%E5%A6%82%E4%BD%95%E5%A4%84%E7%90%86Hexo%E5%92%8CMathJax%E7%9A%84%E5%85%BC%E5%AE%B9%E9%97%AE%E9%A2%98/#)，经我自己测试，最好的方式是用hexo-renderer-pandoc渲染器代替原有的hexo-renderer-marked。而网上说的用hexo-renderer-kramed，对矩阵的情况显示的不好。

```
$ npm uninstall hexo-renderer-marked --save
$ npm install hexo-renderer-kramed --save
```



# Jekyll

其实用Hexo/Jekyll+现成的主题，很方便就能搭出一个静态网站出来。只是除了代码高亮、LaTex这些功能之外，我还想能区分出code blocks和普通无需highlighting的<pre>部分，还有其他一些改动。因为markdown解析的原因，基本所有themes出的效果都是将两者统一处理了。强迫症（其实就是zuo），主题挑来挑去都没找到个合心意的，比如next这种，太复杂，让我一个前端白痴改，哈，那还是自己从头写一个比较符合程序员造轮子的习惯吧。。。（捂脸

（至于为啥从Hexo转到了Jekyll，原因未明）

## Get Started

[Jekyll Doc](https://jekyllrb.com/docs/home/) ([中文版](http://jekyll.com.cn/docs/home/)，内容比较旧)

## Create Theme From Scratch

用`jekyll new-theme`命令，就可以创建出一个theme需要的基本目录项。

```
jekyll new-theme jekyll-theme-awesome
```



`_config.yml`是配置文件。

默认配置参见：https://jekyllrb.com/docs/configuration/#default-configuration

下面对自己的`_config.yml`文件中的内容做一些说明：

```
markdown: kramdown
```

使用`kramdown`作为markdown的解析器。

```
plugins:
  - jekyll-paginate
paginate: 5
paginate_path: "/pages:num/"
```



### 分页显示

用于index.html里对文章的分页显示功能。参见https://jekyllrb.com/docs/pagination/。

在配置文件`_config.yml`中添加：

```
plugins:
  - jekyll-paginate
paginate: 5
paginate_path: "/pages:num/"
```

其中，paginate: 5 设置的是分页数；
paginate_path: "/pages:num/" 设置的是URL的显示格式，如http://localhost:4000/pages2/。

### LaTex支持

在配置文件`_config.yml`中添加：

```
mathjax: false
```

### 修改markdown解析器

在配置文件`_config.yml`中，默认使用的是`kramdown`解析器。

```
markdown: kramdown
```


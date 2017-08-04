---
layout: post
title:  "爬取微信公众号历史文章"
date:   2016-06-10 11:30:00
categories: Algorithm
---

想要获取微信公众号的历史文章，需要三个参数：`__biz`、`uin`、`key`

这里困难的是`key`参数不是固定不变的，存在有效期（大概几十分钟）。那么就有两个问题：

1）如何获取这三个参数？

2）如何实时更新文章列表？（更新key）

目前找到的方法都是手动获取的，不知道[传送门](http://chuansong.me/)是如何做到实时更新？

## 参数获取

1）间接爬 [搜狗微信搜索平台](http://weixin.sogou.com/)。

这个方式我没试过，据说不稳定，而且搜狗貌似做了隐藏。

2）通过手机微信客户端或mac微信客户端，进入某个公众号，点击“查看历史消息”，获取链接。这里又有两种方式：

(a) 利用微信客户端自带的分享功能，通过邮件或QQ分享出链接（直接复制链接不行）。

(b) 让手机和电脑处于同一局域网，通过手机抓包获取链接，工具如fiddler（可参见 [《Fiddler手机抓包》](http://lszero.com/884/fiddler-with-iphone/)）。

url一般为如下形式：<!-- more -->

```
https://mp.weixin.qq.com/mp/getmasssendmsg?__biz=MjM5ODIyMTE0MA==&uin=Mjg4MDM3NDY4MA%3D%3D&key=c50f8b988e61749ad277ef328b1e570b39373fc2621aa3cfb7fd610ada9ab880380773ab1aa6f06714e663bbcab7261926c1432ce0dbc8f2&devicetype=iPhone+OS10.0.2&version=16031b10&lang=zh_CN&nettype=WIFI&ascene=3&fontScale=100&pass_ticket=g%2BKftv0wh2qVWzGvLflZd6fUssx%2FounDqsAJyShoMYw5Bpzl2Nyfc8HXZzW0R8df&wx_header=1#wechat_webview_type=1
```

## 如何实时更新

由于key的时间限制，如何自动更新key值？

目前想到的也就写个手机脚本定时模拟点击，然后手机抓包。


### 参考

- [微信公众号抓取思路 – Alexis374/tech_post](https://github.com/Alexis374/tech_post/blob/master/crawl%20wechat%20article.md)
- [批量抓取微信公众号文章](http://www.songluyi.com/%E6%89%B9%E9%87%8F%E6%8A%93%E5%8F%96%E5%BE%AE%E4%BF%A1%E5%85%AC%E4%BC%97%E5%8F%B7%E6%96%87%E7%AB%A0/)
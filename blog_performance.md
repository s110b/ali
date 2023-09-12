---
title: "如何提高blog载入速度"
date: 2018-05-08T20:52:10+08:00
draft: true
category: ["web"]
tags: ["blog"]
---

> 如何让我们的网站在毫秒内打开是每个web开发者必须考虑的事情。人类的耐心有限。
> 如果毫秒内不能打开，意味着你的客户白白流失。

google提供了一个工具，只要输入网址，就可以分析页面存在的问题
首先：打开谷歌在线验证页，输入我的首页URL，点击验证

    http://developers.google.com/speed/pagespeed/insights/
google web tools 验证出好多问题，逐一解决：

# 找出性能瓶颈

把不需要每次都加载的资源缓存到客户端。
这个可以直接更改nginx的配置。 增加压缩gzip。减少带宽传输

    server {
    gzip  on;
    gzip_http_version 1.1;
    gzip_vary on;
    gzip_comp_level 6;
    gzip_proxied any;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
    gzip_buffers 16 8k;
    gzip_disable "MSIE [1-6]\.(?!.*SV1)";

## 增加缓存时间

```java
location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
        proxy_pass http://172.16.11.154:4000;
        proxy_cache_min_uses 1;
        proxy_cache_valid 200 301 302 120m;
        proxy_cache_valid 404 1m;
        expires max;
}
```

## 合并压缩css

为了减少http请求次数，需要把多个css 文件和 js文件合并起来，利用yahoo的 `YUI Compressor`可以把多个ccs文件或者js文件合并成一个单一文件，降低请求次数。

    http://refresh-sf.com/yui/

## 最后一步

异步加载css和js ，TODO
异步加载评论模块（disqus），此模块为国外组织开发，每次打开都耗时极长。
可以调整为异步加载，当有客户需要评论是，按需打开评论模块。
但这个很矛盾，如果客户想评论时，这个时候点击评论，打开还是很慢的。
如果客户看完文章在评论，这个时候评论已经是打开状态，反而体验要好一点。
这个目前还没有别的方案。暂时先这样吧。

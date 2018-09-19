---
layout: post
title: 在github主页中增加访问量和google分析
--- 

在上一篇文章中，已经为主页添加了评论的功能，那么我们再来增加显示访问量功能和google分析。

主要用到的工具：
1. **[busuanzi]**(http://busuanzi.ibruce.info/)，用来实现计数功能
2. **[googleAnalyze]**(https://analytics.google.com)，统计网站数据（供网站管理员参考如访问量、流量等运营的数据）

### 1.busuanzi

简单介绍busuanzi，静态网站建站现在有很多快速的技术和平台，但静态是优点也有缺点，由于是静态的，一些动态的内容如评论、计数等等模块就需要借助外来平台，busuanzi就是一个简单的、可直接访问次数显示在您在网页上（也可不显示）的插件。
![busuanzi主页](/images/2018-09-19-homepageStatistics/busuanziIndex.png)

demo使用方法非常简单，两行代码：
```javascript
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
```

以下介绍三个常用的统计维度，摘录自busuanzi**[官方文档]**(http://ibruce.info/2015/04/04/busuanzi/)：
#### 1.1 站点总访问量

要显示站点总访问量，复制以下代码添加到你需要显示的位置。有两种算法可选：

算法a：pv的方式，单个用户连续点击n篇文章，记录n次访问量。
```
<span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>
```

算法b：uv的方式，单个用户连续点击n篇文章，只记录1次访客数。
```
<span id="busuanzi_container_site_uv">本站访客数<span id="busuanzi_value_site_uv"></span>人次</span>
```

#### 1.2 单页面访问量

要显示每篇文章的访问量，复制以下代码添加到你需要显示的位置。

算法：pv的方式，单个用户点击1篇文章，本篇文章记录1次阅读量。
```
<span id="busuanzi_container_page_pv">本文总阅读量<span id="busuanzi_value_page_pv"></span>次</span>
```
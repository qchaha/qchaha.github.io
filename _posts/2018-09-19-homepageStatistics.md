---
layout: post
title: 在github主页中增加访问量统计和google分析
--- 

在上一篇文章中，已经为主页添加了评论的功能，那么我们再来增加显示访问量统计功能和google分析。

主要用到的工具：
1. **[busuanzi](http://busuanzi.ibruce.info/)**，用来实现计数功能
2. **[googleAnalyze](https://analytics.google.com)**，统计网站数据（供网站管理员参考如访问量、流量等运营的数据）
<br>
### 1.busuanzi

简单介绍busuanzi，静态网站建站现在有很多快速的技术和平台，但静态是优点也有缺点，由于是静态的，一些动态的内容如评论、计数等等模块就需要借助外来平台，busuanzi就是一个简单的、可直接访问次数显示在您在网页上（也可不显示）的插件。
![busuanzi主页](/images/2018-09-19-homepageStatistics/busuanziIndex.png)

demo使用方法非常简单，两行代码：
```javascript

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>

```

以下介绍我使用过的功能，摘录自busuanzi**[官方文档](http://ibruce.info/2015/04/04/busuanzi/)**。
#### 1.1 站点总访问量

要显示站点总访问量，复制以下代码添加到你需要显示的位置。如果你是像我一样的使用了模板，把js引入到通用模板处即可。

有两种算法可选：

算法a：pv的方式，单个用户连续点击n篇文章，记录n次访问量。
```html

<span id="busuanzi_container_site_pv">本站总访问量<span id="busuanzi_value_site_pv"></span>次</span>

```

算法b：uv的方式，单个用户连续点击n篇文章，只记录1次访客数。
```html

<span id="busuanzi_container_site_uv">本站访客数<span id="busuanzi_value_site_uv"></span>人次</span>

```

#### 1.2 单页面访问量

要显示每篇文章的访问量，复制以下代码添加到你需要显示的位置。

算法：pv的方式，单个用户点击1篇文章，本篇文章记录1次阅读量。
```html

<span id="busuanzi_container_page_pv">本文总阅读量<span id="busuanzi_value_page_pv"></span>次</span>

```

#### 1.3 在访问量数据未取回来之前，我不想让页面显示为诸如“本站总访问量 次”，显得太low，怎么办？

在这种情况下，我使用了未取到数据前，显示fontawesome的字体icon，fa-spin来做动态过渡，因此先导入fontawesome字体：
```html

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">
<span id="busuanzi_value_page_pv">本文总阅读量<i class="fa fa-spinner fa-spin"></i>次</span>

```
<br>
### 2.googleAnalyze

googleAnalyze的集成也是非常简单，首先我们需要创建googleAnalyze账户，访问**[这里](https://analytics.google.com)**进行操作。

#### 2.1 创建新账号  

![创建新账号](/images/2018-09-19-homepageStatistics/gaCreateAccount.png)  

需要填写的内容：
* 账号名称
* 网站名称
* 网站网址  

![创建新账号2](/images/2018-09-19-homepageStatistics/gaCreateAccount2.png)  

选项默认全勾，点击**获取跟踪ID**
创建成功后，会生成一个跟踪ID，我们需要记下两个信息：
1. 跟踪ID
2. 全局网站代码(gtag.js)  

![创建新账号3](/images/2018-09-19-homepageStatistics/gaCreateAccount3.png)

#### 2.2 集成到主页
在主页的_config.yml中，打开GoogleAnalyze选项，并填写上你的跟踪ID：
```

#Google Analytics
google_analytics: UA-126049566-1  # your google analytics

```
  
  然后把全局网站代码添加到jedyll模板的head处，保证访问主页中的所有子网页都包含此代码（例子中是我的代码，site.google_analytics是刚才在_config.yml定义的变量，每个人需要按照自己实际情况，例如跟踪ID是不一样的）：
```javascript

<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://www.googletagmanager.com/gtag/js?id={{ site.google_analytics }}"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());

  gtag('config', '{{ site.google_analytics }}');
</script>

```

完成以上，在google analytics中就可以看到实时概览效果：  

![ga效果图](/images/2018-09-19-homepageStatistics/gaExample.png)
<br>
### 3.成果

![效果图](/images/2018-09-19-homepageStatistics/result.png)

左边导航栏出现了站点访问总量和总人数，而文章标题下方显示了本文章的阅读次数。
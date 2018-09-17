---
layout: post
title: markdown常用语法
--- 

初次接触markdown，语法还是挺简洁的，现在把常用的语法记下，供日后参考。

### 1.标题
---

# 一级标题
```
# 一级标题
```
## 二级标题
```
## 二级标题
```
### 三级标题
```
### 三级标题
```
#### 四级标题
```
#### 四级标题
```
##### 五级标题
```
##### 五级标题
```
###### 六级标题
```
###### 六级标题
```
 

### 2.列表
---

#### 2.1有序列表
1. pagination
2. comments
3. google analytics
  
```
1. pagination
2. comments
3. google analytics
```
  
#### 2.2无序列表
* pagination
* comments
* google analytics
  
```
* pagination
* comments
* google analytics
```


### 3.超链接
---

hi,这是我的[主页](https://qchaha.github.io)哦
```
hi,这是我的[主页](https://qchaha.github.io)哦
```


### 4.代码块
---

#### 4.1普通代码
```
a = 1
print(a)
```
  
````
```
a = 1
print(a)
```
````
**小贴士：在代码块里显示反引号\`时，最外层的反引号需要比显示的反引号多一个，例如上例中代码块中需要显示三个反引号\`\`\`，最外层则需要四个反引号\`\`\`\`。**
  
#### 4.2高亮代码
```python
import subprocess
a = 1
print(a)
subprocess.check_output("hostname", shell=True)
```
  
````
```python
import subprocess
a = 1
print(a)
subprocess.check_output("hostname", shell=True)
```
````

#### 4.3代码块中转义双花括号
```javascript   
{% raw %}
{% if site.disqus %}
<div id="disqus_thread">
  <script>
    var disqus_config = function () {
      this.page.url = '{{ page.url | absolute_url }}';
      this.page.identifier = '{{ page.url | absolute_url }}';
    };
    (function() {
      var d = document, s = d.createElement('script');
      s.src = 'https://{{ site.disqus.shortname }}.disqus.com/embed.js';
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
    })();
  </script>
  <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by     Disqus.</a></noscript>
{% endif %}
</div>
{% endraw %}
```
  

  
````
```javascript   
{% assign openTag = '{%' %}  
{{ openTag }} raw %} 
{% raw %}
{% if site.disqus %}
<div id="disqus_thread">
  <script>
    var disqus_config = function () {
      this.page.url = '{{ page.url | absolute_url }}';
      this.page.identifier = '{{ page.url | absolute_url }}';
    };
    (function() {
      var d = document, s = d.createElement('script');
      s.src = 'https://{{ site.disqus.shortname }}.disqus.com/embed.js';
      s.setAttribute('data-timestamp', +new Date());
      (d.head || d.body).appendChild(s);
    })();
  </script>
  <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
{% endif %}
</div>
{% endraw %}
{{ openTag }} endraw %}
```
````


### 5.图片
---

#### 5.1一般用法
我的头像：![头像](/images/avatar.png)

```
我的头像：![头像](/images/avatar.png)
```

#### 5.2自定义图片大小
我的头像：<img src="/images/avatar.png" width="96px" height="96px">

```
我的头像：<img src="/images/avatar.png" width="96px" height="96px">
```


### 6.引用
---

> 引用内容：今天打台风，山竹来了哦

```
> 引用内容：今天打台风，山竹来了哦
```

### 7.参考文档
---

[最常用法，readme文件对照](https://github.com/KrisYu/Xian/blob/master/ReadMe.md)

[liquid语法，高级用法，模板显示特殊语言](https://huang-qing.github.io/github/2017/10/19/Jekyll-Liquid-API/)
---
title: 使用多说为next主题增加评论功能
categories: 博客管理
tags: [hexo,next,多说]

---

# 登录多说并获取通用代码
1. 登录[多说](http://duoshuo.com/)，可以使用百度、新浪等账号授权登录；
2. 点击我要安装，创建站点，站点名称和站点地址可填写自己的博客名称和地址，多说域名为需要注册的域名，可以和博客的域名保持一致，电子邮箱填写自己的邮箱即可，点击创建。
3. 点击后台管理->工具，即可获取到通用代码，格式如下：
```
<!-- 多说评论框 start -->
	<div class="ds-thread" data-thread-key="请将此处替换成文章在你的站点中的ID" data-title="请替换成文章的标题" data-url="请替换成文章的网址"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"xxxxxx"};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- 多说公共JS代码 end -->
```
# 配置next主题配置文件
打开themes\next目录下的_config.yml配置文件，找到duoshuo_shortname标签，设置为刚才创建站点时多说域名中的内容，请注意，没有前缀http://和后缀.duoshuo.com，只需要注册域名是填入的部分即可。

# 修改脚本
打开next\layout\_partials目录下的comment.swing文件，把在dushuo网站上获取的通用代码黏贴到{% if page.comments %}  和{% endif %}之间。同时需要再修改一点，把
```
<!-- 多说评论框 start -->  
    <div class="ds-thread" data-thread-key="请将此处替换成文章在你的站点中的ID" data-title="请替换成文章的标题" data-url="请替换成文章的网址"></div>
<!-- 多说评论框 end -->  
```
替换成
```
<!-- 多说评论框 start -->  
     <div class="ds-thread" data-thread-key="<%- page.path %>" data-title="<%- page.title %>" data-url="<%- page.permalink %>"></div>  
<!-- 多说评论框 end -->  
```
#使用
重新生成并上传即可：
```
hexo c
hexo g -d
```
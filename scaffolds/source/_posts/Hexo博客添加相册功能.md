---
title: Hexo博客添加相册功能
date: 2017-7-14 16:42:20
tags:  Hexo博客添加相册功能
reward: true
---

# Hexo博客添加相册功能

基本上所有的hexo主题默认都没有实现相册功能，一方面相册功能的需求较少，毕竟hexo主要是写博客用的；另一方面实现相册功能比较麻烦，比如说：

* 图片放哪里，放在source文件夹中，需要解析成静态文件，图片一旦多的话就会解析的非常慢

* 怎么批量获取图片url(文件名)，图片那么多，不可能一个一个的手动输入图片url

* 等等

所以需要找到一个好的解决方法。

注意：本博客使用的是spfk(也是yilia主题的另一种表现方式)主题，该主题作者[litten大牛](https://github.com/litten)有实现了相册功能，但他是同步`instagram`的图片，我会用不同的方法实现一个相册功能。

<!--more-->

### 新建一个页面

	hexo new page "photo"

执行上面命令，会在`source`文件夹中生成`photo`文件夹，打开`photo`文件夹中的`index.md`文件，修改内容如下：

	title: 相册 
	noDate: 'true'
	--- 

	<link type="text/css" href="/fancybox/jquery.fancybox.css" rel="stylesheet">
	 <div class="instagram">
	  <section class="archives album">
		<ul class="img-box-ul"></ul>
	  </section>
	 </div>


相册的样式使用该主题作者的相册样式，如果大家使用其他主题，样式可以自己实现。

### 图片位置

图片不能放在`source`文件中，因为需要编译成静态文件，非常慢，那放在哪好呢？一般我们会把博客备份到`github`，这样可以在不同电脑上写博客。那么我们也可以把相册一起上传到`github`，所以，在博客根目录下建一个放置图片的文件夹`photos`，这样你上传再多的图片都可以。

**博客备份(本地项目上传到GitHub):请查看本博客关于[上传GitHub的相关文章](https://shonlee.github.io/2017/04/06/github%E6%8F%90%E4%BA%A4%E6%9C%AC%E5%9C%B0%E9%A1%B9%E7%9B%AE/)**

### 获取图片

原生`js`不能很好的处理文件，所以用js并不能获取图片的文件名。虽然`js`不能处理文件，但是`nodejs`可以啊。在`source\photo`文件夹中新建一个`tool.js`文件，内容如下：

	"use strict";
	const fs = require("fs");
	const path = "../../photos";
	
	fs.readdir(path, function (err, files) {
	    if (err) {
	        return;
	    }
	    let arr = [];
	    (function iterator(index) {
	        if (index == files.length) {
	            fs.writeFile("output.json", JSON.stringify(arr, null, "\t"));
	            return;
	        }
	
	        fs.stat(path + "/" + files[index], function (err, stats) {
	            if (err) {
	                return;
	            }
	            if (stats.isFile()) {
	                arr.push(files[index]);
	            }
	            iterator(index + 1);
	        })
	    }(0));
	});


`tool.js`的作用是把所有的图片名称放在一个`json`文件中。运行`node tool.js`就会在`source\photo`下创建一个`output.json`文件。每次你要添加新图片时，都要运行`node tool.js`。

有了这个json文件，接下来就要用到js生成html代码了。

**运行`node tool.js的方法`**

	a. 打开cmd命令:
	b. CD 到tool.js的目录文件下
	c. 运行命令 `node tool.js`

![](http://i1.piimg.com/567571/ba2d4107e9ef9d4c.png)

运行结果如图:

![](http://i2.muimg.com/567571/71b3bc62eace5d56.png)

### 生成html代码

在`yourBlog\themes\spfk\source\js`(`yourBlog`为你的博客根目录，将`spfk`替换为你使用的主题)文件夹下新建一个`photo.js`的文件夹。为什么在这里建呢？因为编译之后会出现在`public\js`文件夹中，博客使用的js都在这。

`photo.js`代码如下：

	define([], function () {
	    return {
	        page: 1,
	        offset: 20,
	        init: function () {
	            var that = this;
	            $.getJSON("/photo/output.json", function (data) {
	                that.render(that.page, data);
	                that.scroll(data);
	            });
	        },
	
	        render: function (page, data) {
	            var begin = (page - 1) * this.offset;
	            var end = page * this.offset;
	            if (begin >= data.length) return;
	            var html, li = "";
	            for (var i = begin; i < end && i < data.length; i++) {
	                li += '<li><div class="img-box">' +
	                    '<a class="img-bg" rel="example_group" href="https://github.com/ShonLee/hexo_blog/blob/master/photos/' + data[i] + '?raw=true"></a>' +
	                    '<img lazy-src="https://github.com/ShonLee/hexo_blog/blob/master/photos/' + data[i] + '?raw=true" />' +
	                    '</li>';
	            }
	
	            $(".img-box-ul").append(li);
	            $(".img-box-ul").lazyload();
	            $("a[rel=example_group]").fancybox();
	        },
	
	        scroll: function (data) {
	            var that = this;
	            $(window).scroll(function() {
	                var windowPageYOffset = window.pageYOffset;
	                var windowPageYOffsetAddHeight = windowPageYOffset + window.innerHeight;
	                var sensitivity = 0;
	
	                var offsetTop = $(".instagram").offset().top + $(".instagram").height();
	
	                if (offsetTop >= windowPageYOffset && offsetTop < windowPageYOffsetAddHeight + sensitivity) {
	                    that.render(++that.page, data);
	                }
	            })
	        }
	    }
	})


其中`https://github.com/ShonLee/hexo_blog/blob/master/photos/' + data[i] + '?raw=true`是上传至github的图片链接。**`blob`就相当于地址中的`tree`**

![](http://i2.muimg.com/567571/68797527b5cb45de.png)

因为不可能一次性将所有的图片都插入到页面中，所以我做了分页功能，一次加载20张图片。

引入`photo.js`最直接的方法是在`E:\Hexo_Blog\source\photo`目录下的`index.md`文件中引入：

	title: 相册 
	noDate: 'true'
	--- 

	<link type="text/css" href="/fancybox/jquery.fancybox.css" rel="stylesheet">
	 <div class="instagram">
	  <section class="archives album">
		<ul class="img-box-ul"></ul>
	  </section>
	 </div>

	<script src="/js/photo.js"></script>


如果大家和我用的是同一主题，在`yourBlog\themes\spfk\source\js\main.js`文件末尾增加如下代码：

	if($(".instagram").length) {
	 require(['/js/photo.js', '/fancybox/jquery.fancybox.js', '/js/jquery.lazyload.js'], function(obj) {
	 	obj.init();
	 });
	 }

如果不是，看大家使用的主题有什么限制了。

### 生成静态文件

别忘了要在主题的配置文件`_config.yml`的`menu`下添加一个相册的菜单：

	menu: 
		首页: / 
		归档: /archives 
		随笔: /tags/随笔 
		相册: /photo
最后部署一遍,就可以在本地或上传GitHub产看相册了
	
	hexo clean
	hexo g
	hexo d

相册功能就完成了。


>引用[那时候的我](http://www.cnblogs.com/xljzlw/p/5137622.html)的链接<br>
>引用[那时候的我](http://zlwis.me/photo/)的个人博客

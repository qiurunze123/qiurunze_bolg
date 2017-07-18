---
title: 通过github和hexo学习写个人博客
tags:  GitHub+Hexo使用
reward: true
---


## **通过github和hexo学习写个人博客**


# 前言

使用github pages服务搭建博客的好处有：

1. 全是静态文件，访问速度快；
2. 免费方便，不用花一分钱就可以搭建一个自由的个人博客，不需要服务器不需要后台；
3. 可以随意绑定自己的域名，不仔细看的话根本看不出来你的网站是基于github的；
4. 数据绝对安全，基于github的版本管理，想恢复到哪个历史版本都行；
5. 博客内容可以轻松打包、转移、发布到其它平台；
6. 等等；

## 本文所使用的环境：

* Windows10 64位
* node.js@6.10.1
* GitHub Deskstop
* hexo@3.2.2

<!--more-->

## 准备工作

在开始一切之前，你必须完成以下工作：<br><br>

1. 有一个github账号，没有的话去注册一个；[请进入官网注册](https://github.com/)<br>
	**一定要写正确的邮箱,需要验证的哦!**<br><br>
  1.1 作用：是用来做博客的远程仓库、域名、服务器之类的，怎么与本地hexo建立连接等下讲。<br>
 	  github账号我也不再啰嗦了,没有的话直接申请就行了，跟一般的注册账号差不多

2. 安装git for windows（或者GitHub客户端）<br>

	2.1 GitHub客户端只有在线安装程序(和LOL安装程序一样,要在线下载),因为是国外的网站,下载很慢,且不容易成功,我也是在不经意的情况下完成的,网上有GitHub的安装包,自己去百度一下,这里就不再累述了

	因我用的是GitHub的windows客户端,只要在客户端输入了账号密码后,就会自动连接GitHub,并生成SSHKey,减少了很多麻烦,如果是安装的git for Windows 请留意下面的SSHKey的获取和设置<br>

    2.2 git for windows分享文件中下载安装就好,接着开始配置SSHKey<br>


	2.2.1 配置SSH key

	为什么要配置这个呢？因为你提交代码肯定要拥有你的github权限才可以，但是直接使用用户名和密码太不安全了，所以我们使用ssh key来解决本地和服务器的连接问题。
	
	用git bash执行如下命令：
	
	```bash
	$ cd ~/. ssh #检查本机已存在的ssh密钥
	```
	
	如果提示：No such file or directory 说明你是第一次使用git。
	
		ssh-keygen -t rsa -C "邮件地址"

	然后连续3次回车，最终会生成一个文件在用户目录下，打开用户目录，找到`.ssh\id_rsa.pub`文件，记事本打开并复制里面的内容，打开你的github主页，进入个人设置 -> SSH and GPG keys -> New SSH key：
	
	![](http://image.liuxianan.com/201608/20160818_143914_495_9084.png)<br>
	
	将刚复制的内容粘贴到key那里，title随便填，保存。

	2.2.2 测试是否成功

		$ ssh -T git@github.com # 注意邮箱地址不用改

	如果提示`Are you sure you want to continue connecting (yes/no)?`，输入yes，然后会看到：
	
	> Hi qq00964! You've successfully authenticated, but GitHub does not provide shell access.
	
	看到这个信息说明SSH已配置成功！
	
	此时你还需要配置：
	
		$ git config --global user.name "liuxianan"// 你的github用户名，非昵称
		$ git config --global user.email  "xxx@qq.com"// 填写你的github注册邮箱
	
	具体这个配置是干嘛的我没仔细深究。
  

3. 安装node.js(必须),并了解相关基础知识；[请参考这里](https://hexo.io/zh-cn/docs/)<br>
  3.1 作用：用来生成静态页面的 到相应平台的最新版本，一路安装即可。<br>
  3.2 安装 Node.js 的最佳方式是使用 nvm。<br><br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cURL:<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`$ curl https://raw.github.com/creationix/nvm/master/install.sh | sh`<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Wget::<br>
	&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh`<br><br>
	安装完成后，重启终端并执行下列命令即可安装 Node.js。<br><br>
	`$ nvm install stable`<br><br>
  3.3 您也可以[Node.js官网下载](https://nodejs.org/en/)也可在我的分享文件中下载来安装(安装时，请勾选Add to PATH选项)



4. 安装Hexo<br>


	4.1  hexo简介

	Hexo是一个简单、快速、强大的基于 Github Pages 的博客发布工具，支持Markdown格式，有众多优秀插件和主题。

	官网： http://hexo.io<br>
	github: https://github.com/hexojs/hexo

	4.2 原理
	
	由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。

	4.3 注意事项

	安装之前先来说几个注意事项：
	
	4.3.1. 很多命令既可以用Windows的cmd来完成，也可以使用git bash来完成，但是部分命令会有一些问题，为避免不必要的问题，建议全部使用git bash来执行；<br>
	4.3.2. hexo不同版本差别比较大，本篇文章的配置信息都是基于3.2.2的

	
 	 所有必备的应用程序安装完成后，即可在终端(`Git Bash`)使用 npm 安装 Hexo。

	`$ npm install -g hexo-cli`



# 搭建github博客

## 当所有准备工作完成后,就可以通过下面的方法看看效果

## 初始化

在电脑的某个地方新建一个名为hexo的文件夹（名字可以随便取），比如我的是`D:\Hexo_blog`，由于这个文件夹将来就作为你存放代码的地方，所以最好不要随便放。

	
	$ cd /d/hexo_blog
	
	$ hexo init
	

`$ hexo init`成功的截图:

![](http://i4.buimg.com/567571/8f3874a65fbb9104.png)

hexo会自动下载一些文件到这个目录，包括node_modules，目录结构如下图：

![](http://i2.muimg.com/567571/834278a3f7295eb9.png)<br>


	
	$ hexo g # 生成
	
	$ hexo s # 启动服务
	
执行 `hexo g `命令成功如下图:

![](http://i2.muimg.com/567571/708d3e9535a0592f.png)<br>

执行以上命令之后，hexo就会在public文件夹生成相关html文件，这些文件将来都是要提交到github去的：

![](http://i1.piimg.com/567571/39aad36983ed1882.png)<br>

`hexo s`是开启本地预览服务，打开浏览器访问 http://localhost:4000 即可看到内容，很多人会碰到浏览器一直在转圈但是就是加载不出来的问题，一般情况下是因为端口占用的缘故，因为4000这个端口太常见了，解决端口冲突问题请参考这篇文章：

http://blog.liuxianan.com/windows-port-bind.html

第一次初始化的时候hexo已经帮我们写了一篇名为 Hello World 的文章，默认的主题比较丑，打开时就是这个样子：

![](http://i1.piimg.com/567571/74d1f4ac7841149e.png)<br>

## 修改主题

既然默认主题很丑，那我们别的不做，首先来替换一个好看点的主题。这是[官方主题](https://hexo.io/themes/)<br><br>
这里以这个主题(hexo-theme-yilia)为例：

首先下载这个主题：


	$ cd /D/Hexo_blog

	$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

下载后的主题都在这里：

![](http://i1.piimg.com/567571/20b28b06cb20703a.png)<br>


修改根目录下`_config.yml`中的`theme: landscape`改为`theme: yilia`，然后重新执行`hexo g`来重新生成。

如果出现一些莫名其妙的问题，可以先执行hexo clean来清理一下public的内容，然后再来重新生成和发布。


这里有大量的主题列表使用方法里面 都有详细的介绍，我就不多说了。<br>
我这里有几款个人认为不错的主题，免去你们，一个一个的选了，欢迎吐槽我的审美，?<br>
　[Cover](https://github.com/daisygao/hexo-themes-cover) - A chic theme with facebook-like cover photo<br>
　[Oishi](https://github.com/henryhuang/oishi) - A white theme based on Landscape plus and Writing.<br>
　[Sidebar](https://github.com/hardywu/hexo-theme-sidebar) - Another theme based on Light with a simple sidebar<br>
　[TKL](https://github.com/SuperKieran/TKL) - A responsive design theme for Hexo. 一个设计优雅的响应式主题<br>
　[Tinnypp](https://github.com/levonlin/Tinnypp) - A clean, simple theme based on Tinny<br>
　[Writing](https://github.com/yunlzheng/hexo-themes-writing) - A small and simple hexo theme based on Light<br>
　[Yilia](https://github.com/litten/hexo-theme-yilia) - Responsive and simple style 优雅简洁响应式主题，我用得就是这个。<br>
　[Pacman](https://github.com/Voidly/pacman) voidy - A theme with dynamic tagcloud and dynamic snow<br>

## 上传之前

在上传代码到github之前，一定要记得先把你以前所有代码下载下来（虽然github有版本管理，但备份一下总是好的），因为从hexo提交代码时会把你以前的所有代码都删掉。

### 配置Github
1. `ssh key`肯定要配置好。
	> Hi qq00964! You've successfully authenticated, but GitHub does not provide shell access.
	
2. 建立Repository

建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】，比如说，如果你的github用户名是test，那么你就新建`test.github.io`的仓库（必须是你的用户名，其它名称无效），将来你的网站访问地址就是 http://test.github.io 了，是不是很方便？固定写法然后建
立关联<br>
![](http://i4.buimg.com/567571/031918c8a7e9965d.png)<br>

创建成功后，默认会在你这个仓库里生成一些示例页面，以后你的网站所有代码都是放在这个仓库里啦。

3.配置根目录下`_config.yml`中有关`deploy`的部分：注意： **`:  后面要有空格`**

	deploy:
	  type: git
	  repository: https://github.com/leopardpan/leopardpan.github.io.git
	  branch: master

此时直接执行`hexo d`的话一般会报如下错误：

	Deployer not found: github 或者 Deployer not found: git

原因是还需要安装一个插件：

	$ npm install hexo-deployer-git --save

安装插件成功截图:

![](http://i4.buimg.com/567571/f5ec60830a0b6dd4.png)

网上会有很多说法，有的type是github, 还有repository 最后面的后缀也不一样，是github.com.git，我也踩了很多坑，我现在的版本是hexo: 3.2.2，执行命令hexo -vsersion就出来了,貌似3.0后全部改成我上面这种格式了。 忘了说了，我没用SSH Keys如果你用了SSH Keys的话直接在github里复制SSH的就行了，总共就两种协议，相信你懂的。

## 上传到GitHub

如果你一切都配置好了，发布上传很容易，一句`hexo d`就搞定，当然关键还是你要把所有东西配置好。

执行配置命令：

	hexo deploy 或者 hexo d

发布成功截图如下:<br>

![](http://i4.buimg.com/567571/1efb03acc7d55a73.png)<br>

然后在浏览器中输入`http://qq00964.github.io` 就行了，我的 github 的账户叫qq00964 ,把这个改成你 github 的账户名就行了

![](http://i4.buimg.com/567571/af52da6be4d1326f.png)<br>

### 我刚建好就是这个样子,还没有更改里面的内容,全是默认的,等有时间了再把小窝美化一下

***

## 绑定域名(我没有绑定,都懂得!)

当然，你不绑定域名肯定也是可以的，就用默认的 `xxx.github.io` 来访问，如果你想更个性一点，想拥有一个属于自己的域名，那也是OK的。

首先你要注册一个域名，域名注册以前总是推荐去`godaddy`，现在觉得其实国内的阿里云也挺不错的，价格也不贵，毕竟是大公司，放心！

绑定域名分2种情况：带www和不带www的。

域名配置最常见有2种方式，CNAME和A记录，CNAME填写域名，A记录填写IP，由于不带www方式只能采用A记录，所以必须先ping一下`你的用户名.github.io`的IP，然后到你的域名DNS设置页，将A记录指向你ping出来的IP，将CNAME指向`你的用户名.github.io`，这样可以保证无论是否添加www都可以访问，如下：

![](http://i4.buimg.com/567571/b0641ad58e3556cc.png)

然后到你的github项目根目录新建一个名为CNAME的文件（无后缀），里面填写你的域名，加不加www看你自己喜好，因为经测试：

* 如果你填写的是没有www的，比如 mygit.me，那么无论是访问 http://www.mygit.me 还是 http://mygit.me ，都会自动跳转到 http://mygit.me<br>
* 如果你填写的是带www的，比如 www.mygit.me ，那么无论是访问 http://www.mygit.me 还是 http://mygit.me ，都会自动跳转到 http://www.mygit.me<br>
* 如果你填写的是其它子域名，比如 abc.mygit.me，那么访问 http://abc.mygit.me 没问题，但是访问 http://mygit.me ，不会自动跳转到 http://abc.mygit.me<br>

另外说一句，在你绑定了新域名之后，原来的`你的用户名.github.io`并没有失效，而是会自动跳转到你的新域名。

## 部署步骤

每次部署的步骤，可按以下三步来进行:<br>

	hexo clean #清理
	hexo generate  或(简写) hexo g  #生成
	hexo deploy	或(简写) hexo d #发布上传

一些常用命令：<br>

	hexo new "postName" 	 #新建文章(生成文件)
	hexo new page "pageName" #新建页面(生成文件夹,不会出现在博文目录)
	hexo generate			 #生成静态页面至public目录
	hexo server				 #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
	hexo deploy 			 #将.deploy目录部署到GitHub
	hexo help 				 #查看帮助
	hexo version  			 #查看Hexo的版本

缩写：<br>

	hexo n == hexo new
	hexo g == hexo generate
	hexo s == hexo server
	hexo d == hexo deploy

组合命令：<br>

	hexo s -g #生成并本地预览
	hexo d -g #生成并上传


## 写博客

定位到我们的hexo根目录，执行命令：

	$ hexo new 'my-first-blog'

hexo会帮我们在`_posts`下生成相关md文件：

![](http://i2.muimg.com/567571/e0d01cdc494facb1.png)<br>

我们只需要打开这个文件就可以开始写博客了，默认生成如下内容：<br>

	$ cd /d/hexo_blog/source/_posts
	$ less my-first-blog.md


![](http://i2.muimg.com/567571/2c4d4a5c76329557.png)<br>


![](http://i2.muimg.com/567571/404f14d1a7628cdf.png)<br>

当然你也可以直接自己新建md文件，用这个命令的好处是帮我们自动生成了时间。

一般完整格式如下：**注意 `:  后面有个空格 `**

	--- 
	title: postName #文章页面上的显示名称，一般是中文 
	date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改 
	categories: 默认分类 #分类 
	tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式， 
	description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面 
	--- 
	
	以下是正文


最终部署时生成：`hexo\public\my-second-blog\index.html`，但是它不会作为文章出现在博文目录。

## 写博客工具

我用的是MarkDownPad免安装版本,在百度的分享文件中有程序包,要先安装SDK后才可以正常使用<br>

该程序为汉化破解版,解压后运行,点击 `帮助` -- `markdown升级` 输入`注册码.tex`文件下的邮箱和密匙进行升级破解

![](http://i2.muimg.com/567571/4b6d758afbebea9d.png)

## Q&A>
一问：如何让文章想只显示一部分和一个 `阅读全文` 的按钮？<BR><br>
答：在文章中加一个 `<!--more-->` ， `<!--more-->` 后面的内容就不会显示出来了。

	使用github pages服务搭建博客的好处有：
	
	1. 全是静态文件，访问速度快；
	2. 免费方便，不用花一分钱就可以搭建一个自由的个人博客，不需要服务器不需要后台；
	3. 可以随意绑定自己的域名，不仔细看的话根本看不出来你的网站是基于github的；
	
	<!--more-->
	
	4. 数据绝对安全，基于github的版本管理，想恢复到哪个历史版本都行；
	5. 博客内容可以轻松打包、转移、发布到其它平台；
	6. 等等；

最终效果：<br>


![](http://i1.piimg.com/567571/d51238a5c114a0ab.png)<br>
<br>


二问：本地部署成功了，也能预览效果，但使用`username.github.io`访问，出现 404 .<br><br>
答：首先确认`hexo d`命令执行是否报错，如果没有报错，再查看一下你的 github 的 `username.github.io`仓库，你的博客是否已经成功提交了，你的 github 邮箱也要通过验证才行。



## 一些基本路径

　文章在`source/_posts`，编辑器可以用MarkDownPad，支持 markdown 语法。如果想修改头像可以直接在主题的`_config.yml`文件里面修改，友情链接，之类的都在这里，修改名字在`public/index.html`里修改，开始打理你的博客吧，有什么问题或者建议，都可以提出来，我会继续完善的。

Markdown语法参考链接: [作业部落](https://www.zybuluo.com/mdeditor)




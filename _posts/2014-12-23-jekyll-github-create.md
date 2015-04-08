---
published: true
title: 使用GitHub Pages上快速搭建个人博客
layout: post
author: 可里胡 
category: 技术
tags:
- github
- jekyll
---

使用[Github](http://github.com)搭建个人博客的天然优势在于，它将源码仓库和开发者个人结合在一起了，有了更多的社交属性。加之技术的大牛基本都在github上开挂，很多互联网企业也把自己的开源项目转移到github上，所以，github已成为开发者标配工具。当然你还需要搞个博客或项目介绍之类的，方便别人了解你，而[Github Pages](http://pages.github.com/)完全可以满足你的博客空间需求。

使用Github Pages之前，你需要了解：

*  Github Pages提供静态内容页码，当然，它还拥有强大的Automatic Generator，可以允许你编写动态网页上传，使用[Jekyll](http://jekyllrb.com/)处理为静态网页，当然，使用之前必须要按照ruby+jekyll的环境
*  [Markdown](http://daringfireball.net/projects/markdown/syntax)标记语言
*  大约300MB的空间限制
*  可以使用域名绑定
*  基于Git的发布方式
*  很多地方需要DIY，当然也有多种[Jekyll themes](http://jekyllrb.com/)模板可供使用

接下来，我以OSX为例，818如何快速搭建。


###1、准备工作

- ruby环境

mac 下集成了ruby开发环境，当然如果你得版本低于1.9，就需要升级到1.9以上，否则jekyll无法安装成功，1.9以上版本跳过此步。

使用RVM安装ruby，先安装RVM(当然前提是要装好git)：

<code>curl -L https://get.rvm.io | bash -s stable --rails --autolibs=enabled</code>

安装完成后可以使用RVM查看ruby的版本:

<code>rvm list</code>

选择ruby-2.0.0p481这个版本：

<code>rvm instal ruby-2.0.0p481</code>

指定默认使用这个版本：

<code>rvm --default use ruby-2.0.0p481</code>



- Jekyll安装

最后，为了顺利Gem安装jekyll，需要修改下RubyGems的源，国内一般改为淘宝的：
<pre>
<code>gem sources --remove http://rubygems.org/</code>
<code>gem sources --a http://ruby.taobao.org/</code>
</pre>

使用Gem安装Jekyll:

<code>gem install jekyll</code>

安装完成，可以启动jekyll server，试试是否已经安装OK

<code>jekyll server</code>

顺利的话，可以访问http://localhost:4000/访问

- 创建github pages

github上注册一个账号，创建一个youname.github.com的repository，然后进入你的Profile设置页面Settings-->SSH keys，添加SSH key:

<code>
ssh-keygen -t rsa -C "youremail@youremail.com"
</code>

系统会要求你输入加密串，这里可以设置一个加密串密码。

将生成的id_rsa.pub文件内容复制到github SSH Keys配置中:

<img src="http://pic.yupoo.com/hzg139/EyUmlHny/medium.jpg" alt="QQ20150408-1@2x" width="500" height="322" border="0" />

设置后可以测试下可否SSH到github:
<pre>
<code> ssh -T git@github.com
Hi hzg139! You've successfully authenticated, but GitHub does not provide shell access.</code>
</pre>

OK，成功。

###2、使用jekyll themes

现在，我们需要下载一份Jekyll themes模板，当然如果对Jekyll熟悉的话也可以自己DIY，只需要一些JS和CSS知识。为了快速搭建，前往[Jekyll themes](http://jekyllrb.com/)选择一个心仪的模板，以这个博客为例，我选择的是[Freshman2](https://github.com/yulijia/freshman21/)，先把模板clone到本地：

<pre><code>git clone https://github.com/yulijia/freshman21.git yourname.github.com
cd yourname.github.com</code></pre>

更改原来的git源地址为你的github地址：

<code>git remote set-url origin git@github.com:yourname/yourname.github.com.git</code>

好了，可以启动Jekyll测试下是否能runing.

###3、hello world
Jekyll是一个模板转换器，原理就是将Markdown标记语言的文件按照配置转换为静态WEB页面，一个典型的jekyll的文件结构如下：

<img src="http://pic.yupoo.com/hzg139/EyUvZDIR/medium.jpg" alt="QQ20150408-2@2x" width="445" height="500" border="0" />

正式发布文章前，需要更改下模板的配置，主要是_config.yml文件里头的参数，主要配置站点的属性，如post file的格式，使用Permalink这个参数，具体可以前往[官网](https://github.com/mojombo/jekyll/wiki/configuration)阅读。

按照Freshman21的模板默认配置，我只修改了个人信息。

现在可以发布一篇文章测试下：

在_post/下简历一个2015-04-01-test.md的文件，使用文本编辑器打开（Mac推荐Mou）,并输入如下内容：

<pre><code>
---
published: true
title: hellow world
layout: post
author: 可里胡 
category: test
tags:
- test
---
hello world!
</code></pre>

OK，头部的内容为固定的模板格式，保存后重启jekyll，不出意外可以看到你发布的文章。

接下来，需要发布到github：
<pre>
<code>git add .
git commit -m "first post"
git push -u origin master
</code>
</pre>

进入你得github page页，看是否已成功发布。

###4、其他配置项

 - 绑定域名<br/>
注册号域名后，需要在根目录下创建一个CNAME的文件，内容为你要绑定的域名，如：

<code>kelihu.com</code>

commit到github后，在域名解析中设置A记录，参考[官方配置](https://help.github.com/articles/tips-for-configuring-an-a-record-with-your-dns-provider/)，在解析主机中填写192.30.252.153 或 192.30.252.154
稍等片刻就可以访问了。


- 启用评论<br/>
先去[Disqus](https://disqus.com/)注册一个账号，注册账号为你的Disqus_shortname，配置到_config.yml中。

- 常见的坑<br/>
clone别人的模板后，记得更改github源，别人页面的代码、配置记得要更改干净
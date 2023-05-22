---
layout: post
title: 用 Jekyll 搭建一个 Github Pages 博客
categories: 荤菜
tags: blog ruby jekyll github-pages search-engine SEO
date: 2016-09-02
---

blog = web log，博客这一事物是用于记录自己的想法或者心路历程给具有相同价值观的人观看的。如今流行的框架是 hexo，基于 node.js，速度更快，样式更多。至于为什么选择 Jekyll？我只是更喜欢 ruby 而已。

关于更多的 hexo 和 Jekyll 的对比 [静态博客及博客系统Jekyll与Hexo的介绍][JekyllvsHexo]。

以下操作均在 windows 下进行，其他 OS 并不在讨论范围。

{% include brline %}

## 1) git 与 github

首先要安装 [git][gitbash]，另一个是 [github desktop][gitdesktop]，由于名字太长，以下简称 gitgui。

> 更新，这个带 gui 的 git 工具不仅难用，功能也不全，打开速度还超慢，不再建议日常使用。

然后，在[这里][signgit]注册

![sign-up][isignup]

{% include brline %}

## 2) 项目仓库

点击 new repository

仓库名为 username.github.io

![repository][icrerepo]

回到主页，repository 小窗口已经可以看到刚才新创建的项目仓库了

### __建立联系__

配置 ssh keys

如果是使用 gitgui，安装完成并登录后会自动帮我们添加ssh keys，不需要再手动配置。

然而，现在好像也不再需要手动去设置了，在第一次 commit 的时候 git 会弹一个小登录窗口。

> 在官网里点击自己头像 --> Settings --> SSH Keys and GPG Keys 查看自己的 SSH Keys

设置本地目录

gitgui可以按左上角的 + 号，选择 clone，选择刚刚新建的仓库，随便找个地方作为根目录，clone it。 

![clone][iclone]

然而一般都是使用 command prompt 找个目录直接 clone 下来。

{% include brline %}

## 3) 模板

博客基于 Jekyll，而新手往往摸不着头脑，这里有一些[现成的模板][jekyllmodel]可直接使用：

以 [hydejack][hj] 这个模板为例,可以直接下载压缩包，也可以 clone 到本地：

![model][imodel]

现在你已经有一个现成的网站结构了。

{% include brline %}

## 4) 推送与测试

选择最后修改的时间（时间轴最右），然后为这次的操作起个名字，commit。

![commit][icommit]

commit 只是为这次的修改存档，只有 push 到远程仓库才能更新你的数据，点一下 sync。

![sync][isync]

打开 <http://username.github.io> 。

{% include brline %}

## 5) 环境

Jekyll 基于 ruby，因此想要在本地构建一个测试环境需要具有 ruby 的开发和运行环境。

### __ruby__
[RubyInstaller][rubyins]。它能帮你在 windows 中安装 ruby 开发环境需要的所有东西。

下载，运行，安装!

### __ruby-dev__

duby devKit 是一个在 windows 上帮助简化安装及使用 ruby c/c++ 扩展如 RDiscount 和 RedCloth 的工具箱。 

>Ruby 2.2.4 版本已经整合了DevKit

### __ruby-gem__

gem 是一种应用程序打包部署解决方案，是一个方便而强大的 ruby 程序包管理器，ruby 的第三方插件是用 
gem 来管理，非常容易发布和共享，一个简单的命令就可以安装上第三方的扩展库。

> gem 就类似于 Debian 的 apt-get，centOS 的 yum，emacs 的 elpa。

> gem 也被2.2.4整合到安装包里了，只要知道有这几工具就行了

{% include brline %}

## 6) Jekyll

有了 gem 以后，安装软件就很傻瓜式了。

{% highlight ruby %}
$ gem install jekyll
{% endhighlight %}

由于国内网络原因，导致 rubygems.org 存放在 amazon s3 上面的资源文件间歇性连接失败。所以你会与遇到 
gem install 或 bundle install 的时候半天没有响应。

查看 gem 的下载镜像网站

{% highlight ruby %}
$ gem sources

*** CURRENT SOURCES ***

https://rubygems.org/

#=> 替换成国内的镜像网站 <https://ruby.taobao.org/>
#=> 注意是 https ，淘宝已经停止基于 HTTP 协议的镜像服务
$ gem sources --remove https://rubygems.org/
$ gem sources --add https://ruby.taobao.org/
{% endhighlight %}

{% include brline %}

## 7) Jekyll Serve

安装成功后进入到仓库的根目录，打开 bash 运行 Jekyll 服务

{% highlight ruby %}
$ jekyll serve 
{% endhighlight %}

![jekyll-serve][ijserver]

Jekyll 此时会在 localhost 的 4000 端口监听 http 请求，访问 <http://localhost:4000>

{% include brline %}

## 8) 自定义样式

首先你需要一些基本的 html 基础，然后你还要认识一下 **Liquid**。

Liquid 是一种标记语言，同时也是为了快速生成模板而诞生的模板语言。
Liquid 使用 ruby 编写而成，所以可以直接在 Jekyll 中使用。

你可以在 Jekyll 中通过 Liquid 方便地使用某些内置的变量，下面举几个例子：

{% highlight liquid %}
{% raw %}

#=> 返回你在 _config.yml 中定义的 url
{{ site.url }}

#=> 返回所有文章 (./_posts 的内容物)
{{ site.posts }}

#=> 返回当前页面的 page 变量，上面的 posts 其实就是一堆 page
{{ page }}

#=> 返回当前 page 的 title
{{ page.title }}

...

{% endraw %}
{% endhighlight %}

这里就不逐一列举，更多请参考 [jekyll 引用变量][jekyllvir]。

对 Jekyll 的变量有了一定的基本认知后就可以进行下一步了，比如对变量进行遍历、映射，创建临时变量等。

在这里我只举一个简单的例子：

{% highlight liquid %}
{% raw %}

#=> 将所有 post 映射成它的 title，并用 join 拼接成字符串，最后赋值给 titles
{% assign titles = site.posts | map: 'title' | join %}

#=> 你也可以写成这样，不过注意，你在 for 中的换行会导致 title 使用换行符拼接
{% capture titles %}
	{% for post in site.posts %} {{ post.title }} {% endfor %}
{% endcapture %}

{% endraw %}
{% endhighlight %}

使用 Liquid 配合 Jekyll 可以非常方便地对网站的样式进行修改，
你可以访问 [liquid markup][liquidmarkup] 了解更多的 Liquid 语法。

{% include brline %}

## 9) SEO 优化

写完第一篇博客的第二天，打开搜索引擎搜索了一下我的博客，发现什么也没有。。。

原来搜索引擎还没有收录我的网站，于是找了一些能够在短时间内收录自己网站的方法，顺便分享给大家。

### __向搜索引擎提交自己的网站__

作为最原始的办法，也是大家第一时间想到的办法，但其实这只是最被动，最低效的方法。这种向搜索引擎提交 
url 的方法的影响力在降低，对 seo 起的作用越来越小，对于这部分提交的数据搜索引擎更新非常之慢。

提交 url 的地址： <https://www.google.com/webmasters/tools/submit-url?pli=1>

### __向 DMOZ Directory 申请收录__

DMOZ Directory 就像站点的联合国，其权威度和被收录难度可想而知，一个新的网站被收录的几率的 0.01%。

而且，分类目录对网站推广的效果远不如技术性搜索引擎优化，故这种办法不在本文的讨论范围。

### __外部链接__

这是最好的方法，尽可能地获得多的外部链接。那具体是要怎么做呢，很简单：

在一些社交网站或者软件比如知乎，豆瓣，贴吧，豆瓣，微博，推特，脸书甚至网易云，
回复内容带上你的博客地址，不一定是首页，但必须是活动的，是超链接。

#### __原理是什么？__

搜索引擎会每天漫游并更新其数据库，当漫游站点时发现了你博客的 url，就会顺着 url 去 crawl 你的站点。

一个新站的漫游周期会比较长，漫游后一到两天，再搜索你的网址，就会发现你的网站已被列入了。

#### __为什么要获得尽量多的原因？__

如果你分析了你的链接，几天内被列入是没问题，但这不意味着你的博客就容易被搜索到了。

当你有较多的链接后，搜索引擎漫游网站时发现你的网址就会再次过来 crawl 你的网站，外链越多也就越频繁。

达到某个频率后就会开始深度漫游你的网站，收录更多的页面，并且获得更高的搜索优先度。

### __使用Google Search Console__

首先进入 [Google Webmasters](https://www.google.com/webmasters/)

找到 SEARCH CONSOLE：

![console][console]

有个红色的 ADD A PROPERTY： 

![add-repo][add-repo]

写上你的域名，比如 <http://ldcc.github.io.com> （笑： 

![prop][prop]

刷新一下就能看到刚才你添加的网址了：

![home][home]

然后查看一下 Console Messages：

![message][message]

然后按照提示的步骤去做吧，你的网站大概1小时内就能被收录了：

![presenc][presenc]

{% include brline %}

## 总结

多做一些高质量的外部链接，会有意想不到的收获。

当然，博客的内容是否高质量也是直接决定你网站排名高低的重要因素之一。

以上就是全部。

{% assign res = "first-blog" | prepend: site.res %}

[isignup]: {{res}}/sign-up.png
[icrerepo]: {{res}}/create-repository.png
[iclone]: {{res}}/git-clone.png
[imodel]: {{res}}/jekyll-model.png
[icommit]: {{res}}/commit.png
[isync]: {{res}}/sync.png
[ijserver]: {{res}}/jekyll-serve.png

[JekyllvsHexo]: http://www.dashashi.com/static_blog.html/
[jekyllmodel]: http://jekyllthemes.org/
[signgit]: https://github.com/
[gitbash]: https://git-scm.com/downloads/
[gitdesktop]: href="https://desktop.github.com/
[hj]: http://jekyllthemes.org/themes/hydejack/
[rubyins]: http://rubyinstaller.org/downloads/
[jekyllvir]: https://jekyllrb.com/docs/variables/
[liquidmarkup]: https://shopify.github.io/liquid/

[console]: {{res}}/search-console.png
[add-repo]: {{res}}/add-property.png
[prop]: {{res}}/enter-url.png
[home]: {{res}}/console-home.png
[message]: {{res}}/console-message.png
[presenc]: {{res}}/search-presenc.png
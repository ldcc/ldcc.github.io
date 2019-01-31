---
layout: post
title: 用 jekyll 搭建一个 github page 博客
category: MISC
date: 2016-09-02
---

blog $$\Rightarrow$$ web log，博客这一事物是用于记录自己的想法或者心路历程给具有相同价值观的人观看的。

## 为什么选择 jekyll

如今最流行的是 hexo，基于 node.js，速度更快，样式更多。而Jekyll，我只是不喜欢 js 而已。。。

关于更多的 hexo 和 jekyll 的对比 [静态博客及博客系统Jekyll与Hexo的介绍][JekyllvsHexo]。

一切操作均在 windows 下进行，其他 OS 并不在讨论范围。

## 1) git 与 github

首先要安装 [git][gitbash]，另一个是 [github desktop][gitdesktop]，由于名字太长，以下简称 gitgui。

> 更新，这个带 gui 的 git 工具不仅难用，功能也不全，打开速度还超慢，不再建议日常使用，实在好奇可以去玩一下。

然后，在[这里][signgit]注册

![sign-up][isignup]

## 2) 项目仓库

点击 new repository

仓库名为 username.github.io

![repository][icrerepo]

回到主页，repository 小窗口已经可以看到刚才新创建的项目仓库了

### 建立联系

配置 ssh keys

如果是使用 gitgui，安装完成并登录后会自动帮我们添加ssh keys，不需要再手动配置。

然而，现在好像也不再需要手动去设置了，在第一次 commit 的时候 git 会弹一个小登录窗口。

> 在官网里点击自己头像 --> Settings --> SSH Keys and GPG Keys 查看自己的 SSH Keys

设置本地目录

gitgui可以按左上角的 + 号，选择 clone，选择刚刚新建的仓库，随便找个地方作为根目录，clone it。 

![clone][iclone]

然而一般都是使用 command prompt 找个目录直接 clone 下来。

## 3) 现成的模板

博客基于 jekyll，而新手往往摸不着头脑，这里有一些[现成的模板][jekyllmodel]可直接使用：

以 [hydejack][hj] 这个模板为例,可以直接下载压缩包，也可以 clone 到本地：

![model][imodel]

现在你已经有一个现成的网站结构了。

## 4) 推送

选择最后修改的时间（时间轴最右），然后为这次的操作起个名字，commit。

![commit][icommit]

commit 只是为这次的修改存档，只有 push 到远程仓库才能更新你的数据，点一下 sync。

![sync][isync]

## 5) 测试

打开 <http://username.github.io> 。

## 6) 环境

jekyll 基于 ruby，因此想要在本地构建一个测试环境需要具有 ruby 的开发和运行环境。

### ruby
[RubyInstaller][rubyins]。它能帮你在 windows 中安装 ruby 开发环境需要的所有东西。

下载，运行，安装!

### ruby-dev

duby devKit 是一个在 windows 上帮助简化安装及使用 ruby c/c++ 扩展如 RDiscount 和 RedCloth 的工具箱。 

>Ruby 2.2.4 版本已经整合了DevKit

### ruby-gem

gem 是一种应用程序打包部署解决方案，是一个方便而强大的 ruby 程序包管理器，ruby 的第三方插件是用 gem 方式来管理，非常容易发布和共享，一个简单的命令就可以安装上第三方的扩展库。

> gem 就类似于 Debian 的 apt-get，centOS 的 yum，emacs 的 elpa。

> gem 也被2.2.4整合到安装包里了，只要知道有这几工具就行了

## 7) jekyll

有了 gem 以后，安装软件就很傻瓜式了。

{% highlight ruby %}

$ gem install jekyll

{% endhighlight %}

由于国内网络原因，导致 rubygems.org 存放在 amazon s3 上面的资源文件间歇性连接失败。所以你会与遇到 gem install 或 bundle install 的时候半天没有响应。

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

## 8) jekyll serve

安装成功后进入到仓库的根目录，打开 bash 运行 jekyll 服务

{% highlight ruby %}

$ jekyll serve 

{% endhighlight %}

![jekyll-serve][ijserver]

jekyll 此时会在 localhost 的 4000 端口监听 http 请求，访问 <http://localhost:4000>

## 9) 自定义样式

看到别人的 jekyll 主题那么漂亮是不是也很想自己写一个，首先你需要一些基本的 html 基础，然后你还要认识一下 __liquid__。

liquid 是一种标记语言，同时也是为了快速生成模板而诞生的模板语言。liquid 使用 ruby 编写而成，所以可以直接在 jekyll 中使用。

你可以在 jekyll 中通过 liquid 方便地使用某些内置的变量，下面举几个例子：

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

对 jekyll 的变量有了一定的基本认知后就可以进行下一步了，比如对变量进行遍历、映射，创建临时变量等。

在这里我只举一个简单的例子：

{% highlight liquid %}
{% raw %}

#=> 将所有 post 映射成它的 title，并用 join 拼接成字符串(无参的 join 默认用空格拼接)，最后赋值给 titles
{% assign titles = site.posts | map: 'title' | join %}

#=> 你也可以写成这样，不过注意，你在 for 中的换行会导致 title 使用换行符拼接
{% capture titles %}
	{% for post in site.posts %} {{ post.title }} {% endfor %}
{% endcapture %}

{% endraw %}
{% endhighlight %}

你可以访问 [liquid markup][liquidmarkup] 了解更多的 liquid 语法。

使用 liquid 配合 jekyll 可以非常方便地对网站的样式进行修改，以上就是全部。

{% assign res = page.path | slice: 18,20 | remove: ".md" | prepend: site.res %}

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

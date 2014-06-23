---
layout: post
title: 使用Github Pages建独立博客
description: Github本身就是不错的代码社区，他也提供了一些其他的服务，比如Github Pages，使用它可以很方便的建立自己的独立博客，并且免费。
---

[Github][]很好的将代码和社区联系在了一起，于是发生了很多有趣的事情，世界也因为他美好了一点点。Github作为现在最流行的代码仓库，已经得到很多大公司和项目的青睐，比如[jQuery][]、[Twitter][]等。为使项目更方便的被人理解，介绍页面少不了，甚至会需要完整的文档站，Github替你想到了这一点，他提供了[Github Pages][]的服务，不仅可以方便的为项目建立介绍站点，也可以用来建立个人博客。

Github Pages有以下几个优点：

<ul>
    <li>轻量级的博客系统，没有麻烦的配置</li>
    <li>使用标记语言，比如<a href="http://markdown.tw">Markdown</a></li>
    <li>无需自己搭建服务器</li>
    <li>根据Github的限制，对应的每个站有300MB空间</li>
    <li>可以绑定自己的域名</li>
</ul>

当然他也有缺点：

* 使用[Jekyll][]模板系统，相当于静态页发布，适合博客，文档介绍等。
* 动态程序的部分相当局限，比如没有评论，不过还好我们有解决方案。
* 基于Git，很多东西需要动手，不像Wordpress有强大的后台

大致介绍到此，作为个人博客来说，简洁清爽的表达自己的工作、心得，就已达目标，所以Github Pages是我认为此需求最完美的解决方案了。

## 购买、绑定独立域名
虽说[Godaddy][]曾支持过SOPA，并且首页放着极其不专业的大胸美女，但是作为域名服务商他做的还不赖，选择它最重要的原因是他支持支付宝，没有信用卡有时真的很难过。

域名的购买不用多讲，注册、选域名、支付，有网购经验的都毫无压力，优惠码也遍地皆是。域名的配置需要提醒一下，因为伟大英明的GFW的存在，我们必须多做些事情。

流传Godaddy的域名解析服务器被墙掉，导致域名无法访问，后来这个事情在[BeiYuu][]也发生了，不得已需要把域名解析服务迁移到国内比较稳定的服务商处，这个迁移对于域名来说没有什么风险，最终的控制权还是在Godaddy那里，你随时都可以改回去。

我们选择[DNSPod][]的服务，他们的产品做得不错，易用、免费，收费版有更高端的功能，暂不需要。注册登录之后，按照DNSPod的说法，只需三步（我们插入一步）：

<ul>
	<li>首先添加域名记录，可参考DNSPod的帮助文档：<a href="https://www.dnspod.cn/Support">https://www.dnspod.cn/Support</a></li>
	<li>在DNSPod自己的域名下添加一条<a href="http://baike.baidu.com/view/65575.htm">A记录</a>，地址就是Github Pages的服务IP地址：207.97.227.245</li>
	<li>在域名注册商处修改DNS服务:去Godaddy修改Nameservers为这两个地址：f1g1ns1.dnspod.net、f1g1ns2.dnspod.net。如果你不明白在哪里修改，可以参考这里：<a href="https://www.dnspod.cn/support/index/fid/119">Godaddy注册的域名如何使用DNSPod</a></li>
	<li>等待域名解析生效</li>
</ul>

域名的配置部分完成，跪谢方校长。

## 配置和使用Github
Git是版本管理的未来，他的优点我不再赘述，相关资料很多。推荐这本[Git中文教程][4]。

要使用Git，需要安装它的客户端，推荐在Linux下使用Git，会比较方便。Windows版的下载地址在这里：[http://code.google.com/p/msysgit/downloads/list](http://code.google.com/p/msysgit/downloads/list "Windows版Git客户端")。其他系统的安装也可以参考官方的[安装教程][5]。

下载安装客户端之后，各个系统的配置就类似了，我们使用windows作为例子，Linux和Mac与此类似。

在Windows下，打开Git Bash，其他系统下面则打开终端（Terminal）：
![Git Bash](/assets/images/bootcamp_1_win_gitbash.jpg)

还有一个是关于`category`的问题，根据`YAML`的语法，我们在文章头部可以定义文章所属的类别，也可以定义为`category:[blog,rss]`这样子的多类别，我在本地试一切正常，但是push到GitHub之后，就无法读取了，真让人着急，没有办法，只能采用别的办法满足我的需求了。这里还有一篇[Jekyll 本地调试之若干问题][18]，安装中如果有其他问题，也可以对照参考一下。

##结语
如果你跟着这篇不那么详尽的教程，成功搭建了自己的博客，恭喜你！剩下的就是保持热情的去写自己的文章吧。


[BeiYuu]:    http://beiyuu.com  "BeiYuu"
[Github]:   http://github.com "Github"
[jQuery]:   https://github.com/jquery/jquery "jQuery@github"
[Twitter]:  https://github.com/twitter/bootstrap "Twitter@github"
[Github Pages]: http://pages.github.com/ "Github Pages"
[Godaddy]:  http://www.godaddy.com/ "Godaddy"
[Jekyll]:   https://github.com/mojombo/jekyll "Jekyll"
[DNSPod]:   https://www.dnspod.cn/ "DNSPod"
[Disqus]: http://disqus.com/
[多说]: http://duoshuo.com/
[1]:    {{ page.url}}  ({{ page.title }})
[2]: http://markdown.tw/    "Markdown语法"
[3]:    http://baike.baidu.com/view/65575.htm "A记录"
[4]: http://progit.org/book/zh/ "Pro Git中文版"
[5]: http://help.github.com/mac-set-up-git/ "Mac下Git安装"
[6]: http://help.github.com/ssh-key-passphrases/
[7]: http://beiyuu.github.com
[8]: https://github.com/mojombo/jekyll/blob/master/README.textile
[9]: https://github.com/mojombo/jekyll/wiki/YAML-Front-Matter
[10]: https://github.com/mojombo/jekyll/wiki/configuration
[11]: https://github.com/beiyuu/beiyuu.github.com
[12]: http://docs.disqus.com/developers/universal/
[13]: http://mihai.bazon.net/projects/javascript-syntax-highlighting-engine
[14]: http://code.google.com/p/google-code-prettify/
[15]: https://github.com/mojombo/jekyll/wiki/Install
[16]: https://rvm.io/rvm/install/
[17]: http://jekyllbootstrap.com/
[18]: http://chxt6896.github.com/blog/2012/02/13/blog-jekyll-native.html
[a-record]: https://help.github.com/articles/my-custom-domain-isn-t-working

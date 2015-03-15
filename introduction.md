# 介绍 #

gcrawler是一个基于gevent实现的简单爬虫框架，使用方式模仿scrapy，但功能简单一些，适合于简单的小应用。

## 引子 ##

以前用scrapy写过一些简单的爬虫程序。但是我的需求实在太简单了，用scrapy有点大材小用，而且过于强大的缺点就是用起来太复杂，加上我也不太喜欢twisted——用各种回调实现的异步框架用起来还是不太自然。

前一阵接触了一下gevent （不知道为什么这样一个纯技术网站会在墙外），且不说据说它性能很好，关键是用patch的方式隐含提供异步支持的实现用起来真是太爽了。于是自己写了一个简单的爬虫框架，主要思路是模仿scrapy的。 关于Scrapy

首先来看Scrapy的架构：（从略，见scrapy文档 ）

用户编写的爬虫主要是需要实现Spider和Item Pipeline两部分，Scheduler和Downloader部分是由Scrapy提供，并且整个程序由Scrapy Engine所驱动。

工作流程是：程序启动后Scrapy Engine从Scheduler取得网址，然后通过Downloader去下载页面交给Spider处理，Spider分析页面后根据情况决定是继续抓下一级锭接的页面（返回一个Scrapy Request）还是返回数据（返回一个Item List），Spider返回的数据（Item List）将被汇总交给Item Pipeline处理，比如存成文件或存入数据库什么的。

之所以要重复一下Scrapy的工作流程，是因为gcrawler也是模仿这个流程来做的。

## Scrapy的局限及gcrawler的特点 ##

不过在说gcrawler之前，还是先谈一下我的需求中用Scrapy实现不太方便的地方。最主要的是我的应用中虽然也是用HTTP协议去抓取内容，但是有很多额外的要求，比如有时需要POST，有时需要BasicAuth，有时还需要处理一些特定的HTTP Header字段，这些虽然有Download Middleware也可以实现，但很不自然——一个简单的HTTP请求操作被分割得支离破碎，对于代码的日后维护会造成很大的麻烦。

比如要用feedparser读RSS，因为feedparser是下载解析一体的实现，这时Scrapy就不太方便，虽然也可以拆开实现：用Scrapy 下载再在Spider里用feedparser，但显然这种麻烦不是必要的。再比如说需要用OAuth进行REST API调用的话，用Scrapy的 Download Middleware我还真不知道要怎么做才好。

因此，gcrawler与scrapy最大的不同就是 Downloader的功能也将由用户实现（放在类似Scrapy的Spider中），用户可以自由控制下载操作的种种细节。乍一看似乎这样会增加用户的工作量，但实际上是简化了开发。比如前面说的feedparser或是OAuth调用，都可以直接实现，不用考虑分拆到不同的地方去做。那么由用户来实现下载工作会不会影响性能呢？Scrapy的一大优势就在于利用Twisted这个异步网络框架提供了网络访问的高性能。答案是不会，因为gevent提供了更为简单和高效的实现方式。二者的差异在于：

因为Scrapy是基于Twisted，而它是一个基于回调的异步框架，这就意味着用它来写 Downloader固然可以得到高性能（托异步的福），但同时带来额外的复杂性（拜回调所赐）。所以Scrapy内部实现了这个高性能的 Downloader，在提代高性能的同时屏蔽了这种复杂性，但代价就是前面所说的，用户自定义操作需要通过Middleware实现，结果还是增加了一些复杂性，并且使程序结构变得不太清晰。

而如前面所说，gevent通过patch的方式提供了隐含的异步支持，这样用户编写Downloader就成了简单的貌似“同步阻塞操作”，于是结构清晰与高性能得以兼得。个人根据gevent与twisted的性能比较 猜测，能有与scrapy相当甚至可能更好的性能（纯猜测，待求证）。这是gevent相对twisted的一个巨大的优点。

## 导航 ##
  * 目录：[目录](index.md)
  * 下一页：[使用说明](usage.md)
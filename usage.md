# 使用说明 #

## 基本使用说明 ##
gcrawler 的使用很简单：用户需要实现一个spider类，提供三个成员函数：scheduler, worker, pipeline。其中scheduler是一个生成器，用yield依次返回请求项（比如URL），worker相当于Scrapy里的 Downloader+Spider，用于下载页面内容并解析，pipeline与Scrapy的item pipeline类似，都是用于存储最终结果用。

scheduler和pipeline没什么好说的，重点在worker里。因为 gevent有隐含异步支持，所以这里不再需要像Scrapy里那样下载一个页面解析完后需要把请求重新发回Scheduler进行调度，而是可以直接 继续下载下一级链接的页面继续解析，整个流程会清晰很多，但又不会降低性能。当然要实现像Scrapy那样的方式也可以，只要在worker里把下一级页面链接再传递给scheduler即可。如果worker没有什么结果需要保存（比如上述把链接交给scheduler作下一步处理的情况），只需要简单返回一个None即可，pipeline会自动略过的。

一个简单的下载页面的例子如下：（从略，见源码中的测试用例）

## Downloader类的基本使用 ##
当然，为了方便实现类似Scrapy的应用，gcrawler提供了一个默认的Downloader，所以上面的例子可以简化如下，只需要提供urls，并实现pipeline保存结果即可：（从略，见源码中的测试用例）

## retryOnURLError decorator ##

另外还提供了一个retryOnURLError的decorator，用于自动重试，用法参见源码。

## 多级链接并发下载，Downloader类的扩展使用 ##

如果要使用多级并发下载的话，只要在派生类里增加一个（两级的情况，更多级的话可能需要更多的）解析方法。为了保持与scrapy一致，默认的解析方法也是叫做parse。

具体的spider实现举例如下：（从略，见源码中的测试用例）

如上面的代码所表现的那样，唯一的不同就是增加了一个parse方法，在这个方法里，你可以用任何你喜欢的方式去解析页面（比如正则表达式、XPath、 PyQuery等，这个例子里是用正则表达式），然后将解析结果返回给Downloader。这个例子只实现了一级链接（下载页面上的全部图片链接），如果还要进一步解析下一级页面甚至N级，只要在上面代码中“item = dict(url=i, parse=None)”中把None换成下一级的解析方法并实现这个方法即可。

## 导航 ##

  * 上一页：[项目介绍](introduction.md)
  * 目录：[目录](index.md)
  * 下一页：[安装说明](installation.md)
# 安装说明 #

在使用这个框架之前首先需要安装gevent，而 gevent又依赖libevent和greenlet。本来这事不复杂，gevent的文档里都有说，但可耻滴如前面所说，它的官网在中国大陆是“被不存在的”，所以我还是在这里说一下具体安装方法，并向有关部门坚决竖中指，你们是中国技术创新进步的最大阻力！

  * Ubuntu 10.04：
```
    apt-get install python-dev libevent-1.4-2 libevent-dev
    easy_install greenlet
    easy_install gevent
```
  * Debian 5：
```
    # 先在sources.list里增加一个sid源，比如：
    # deb http://mirrors.163.com/debian/ sid main
    apt-get install python-gevent
    # 如果还缺什么则参考ubuntu
```
  * FreeBSD 8：
```
    cd /usr/ports/devel/py-gevent
    make install clean
```
  * Mac OS X 10.6.x:
```
    # 当然需要安装XCode和HomeBrew（或其它方式安装libevent）
    brew install libevent
    easy_install greenlet
    easy_install gevent
```
  * Windows:

> ……这个自己Google去吧，友情建议还是不要在windows下浪费生命为好。

## 导航 ##
  * 上一页：[使用说明](usage.md)
  * 目录：[目录](index.md)
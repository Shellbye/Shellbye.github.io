---
author: shellbye
comments: true
date: 2014-08-26 09:28:56+00:00
layout: post
slug: python-%e7%88%ac%e8%99%ab%e7%a4%ba%e4%be%8b
title: Python 爬虫示例
wordpress_id: 1015
categories:
- tech_world
post_format:
- 日志
tags:
- Python
---

这是[《Python核心编程》](http://book.douban.com/subject/3112503/)里的一个例子，我加了点注释帮助理解和记忆。Window7 和Ubuntu14.04都测试通过。

{% highlight python %}
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from os import makedirs, unlink, sep
from os.path import isdir, exists, dirname, splitext, abspath
from string import replace, find, lower
from htmllib import HTMLParser
import urllib
from urlparse import urlparse, urljoin
from formatter import DumbWriter, AbstractFormatter
from cStringIO import StringIO

class Retriever(object):

    def __init__(self, url):
        self.url = url
        self.file = self.filename(url)

    def filename(self, url, deffile='index.htm'):
        # 使用http协议解析url地址，将url解析为这样的五元组：(scheme, netloc, path, query, fragment)
        parsedurl = urlparse(url, 'http:', 0)
        # 将主机地址（netloc）和路径（path）合并起来作为存储文件的路径名
        path = parsedurl[1] + parsedurl[2]
        # splitext将path分割为路径与后缀名，如（/path/to/file, txt）
        ext = splitext(path)
        # 如果后缀名为空，则给当前path添加默认的名字index.htm
        if ext[1] == '':
            # 若path结尾有“/”则直接添加index.htm，否则先添加“/”
            if path[-1] == '/':
                path += deffile
            else:
                path += '/' + deffile
        # 取出path中的目录部分（www.shellbye.com\\blog），然后与本地目录结合为一个目录('D:\\www.shellbye.com\\blog')
        ldir = dirname(abspath(path))
        # 如果不是以“/”作为目录分割符的类Unix系统，比如Windows，则需要把“”替换为相应的目录分割符
        # 因为类Unix系统的目录分割符与URI地址的分割符一样，所以可以不处理
        if sep != '/':        # os-indep. path separator
            ldir = replace(ldir, '/', sep)
        # 如果ldir目录不存在在创建
        if not isdir(ldir):      # create archive dir if nec.
            # 如果ldir存在但是不是目录则删除ldir。注，unlink即remove。
            if exists(ldir):
                unlink(ldir)
            makedirs(ldir)
        return path

    def download(self):        # download Web page
        try:
            # 下载self.url到self.file里
            retval = urllib.urlretrieve(self.url, self.file)
        except IOError:
            retval = ('*** ERROR: invalid URL "%s"' % self.url, )
        return retval

    def parseAndGetLinks(self):
        # 创建一个基本的HTML解释器，可能需要单独一篇文章来说这句
        self.parser = HTMLParser(AbstractFormatter(DumbWriter(StringIO())))
        # 解析html文件，获取所有的连接（带有href的）
        self.parser.feed(open(self.file).read())
        self.parser.close()
        return self.parser.anchorlist

class Crawler(object):

    count = 0

    def __init__(self, url):
        self.q = [url]
        self.seen = []
        # 获取url的主机名，domain，即域名
        self.dom = urlparse(url)[1]

    def getPage(self, url):
        r = Retriever(url)
        # 下载一个url对应的页面，出错即返回
        retval = r.download()
        if retval[0] == '*':     # error situation, do not parse
            print retval, '... skipping parse'
            return
        # 爬取统计自增
        Crawler.count += 1
        print '\n(', Crawler.count, ')'
        print 'URL:', url
        # 爬取到的文件的存储位置
        print 'FILE:', retval[0]
        # 加入已爬取队列
        self.seen.append(url)
        # 将从新页面获取的link进行处理
        links = r.parseAndGetLinks()  # get and process links
        for eachLink in links:
            # 当前link不是完整的url，则为其添加完整路径
            if eachLink[:4] != 'http' and find(eachLink, '://') == -1:
                eachLink = urljoin(url, eachLink)
            print '* ', eachLink,
            # 放弃mailto:式的连接
            if find(lower(eachLink), 'mailto:') != -1:
                print '... discarded, mailto link'
                continue
            # 对于没有处理过的link
            if eachLink not in self.seen:
                # 外链不管
                if find(eachLink, self.dom) == -1:
                    print '... discarded, not in domain'
                # 若已爬取则不处理，否则加入待爬取队列
                else:
                    if eachLink not in self.q:
                        self.q.append(eachLink)
                        print '... new, added to Q'
                    else:
                        print '... discarded, already in Q'
            else:
                print '... discarded, already processed'

    def go(self):
    # 掌管整个爬取进程，依次从q中取出待爬取的link，然后去爬取
        while self.q:
            url = self.q.pop()
            self.getPage(url)

def main():
    url = "http://shellbye.com"
    robot = Crawler(url)
    robot.go()

if __name__ == '__main__':
    main()
{% endhighlight%}

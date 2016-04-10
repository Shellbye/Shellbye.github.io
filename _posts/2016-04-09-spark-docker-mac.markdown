---
author: shellbye
comments: true
date: 2016-04-09 10:27:08+00:00
layout: post
slug: use-spark-in-docker-on-mac-osx
title: Use Spark in Docker on Mac
categories:
- tech_world
tags:
- spark
- docker
---

在Mac上安装Docker的过程是比较简单的，有详细的[官方文档]{:target="_blank"}介绍，
本文主要记录我在Docker中使用Spark的一些笔记。

##1.下载docker-spark  
我们需要的docker文件一般都是在[Docker Hub]{:target="_blank"}上，它类似Github，
只是上面存放的都是一些docker文件。我们需要的是sequenceiq/spark这个库，
具体的下载命令类似git，是  
`docker pull sequenceiq/spark:1.6.0`，
接下来就是困扰了我很久的地方了，在这个库的[文档]{:target="_blank"}中，
接下来就是用  
`docker build --rm -t sequenceiq/spark:1.6.0 .`来build了，
可是我尝试了很多次，每次都会报错
`unable to prepare context: unable to evaluate symlinks in Dockerfile path: 
lstat /Users/username/Dockerfile: no such file or directory`，
在网上找了很久也一直没有解决这个问题，直到搜到了作者的一个更老的版本的[博客]{:target="_blank"}，
里面关于build是这样说的，

> Alternatively you can always build your own container based on our Dockerfile.  

Alternatively！！！这不是坑爹嘛！！！为啥在1.6里面就没有这个关键的字眼了呢！
尤其是对我这种docker新手来说！！！不过话说回来，
主要也是我没理解了docker build的[含义]{:target="_blank"}：

> Build a new image from the source code at PATH.  

Build是用来依据Dockerfile重新创建一个image的，而我们其实在第一步的pull中就已经获得了一个image，
可以通过命令`docker images`来查看你已经有的images。


##2.启动docker-spark
当通过pull获得了image或者通过build得到了image之后，接下来就可以启动了，
`docker run -it -p 4040:4040 -h sandbox sequenceiq/spark:1.6.0 bash`  
具体的run命令的各个参数，可以参考[具体的文档]。
这个时候，你就已经进入了包含了spark的一个bash环境，然后就可以使用spark了。


理论上讲到这里就应该结束了，但是并不能，因为我在实际操作时发展，每次我在spark里做的改动，
当我下一次重新用上面的命令启动时，全部都丢失了，又一次需要重新开始。
在[SO]{:target="_blank"}上找到这个问答，
才明白原来run命令运行一次就可以了，run命令其实是启动了一个container，每次run的时候，
都是重新启动一个全新的container，所以如果要接着上次的工作进行的话，可以使用
`docker attach f357e2faab77`(注意最后一个是container的id，用`docker ps -a`可以查看)。

好了，这样的话一个基本的使用场景就搞定了，以后如果有新的发现再来记录。  


[官方文档]:https://docs.docker.com/engine/installation/mac/
[Docker Hub]:https://hub.docker.com/
[文档]:https://hub.docker.com/r/sequenceiq/spark/
[博客]:http://blog.sequenceiq.com/blog/2015/01/09/spark-1-2-0-docker/
[含义]:https://docs.docker.com/engine/reference/commandline/build/
[具体的文档]:https://docs.docker.com/engine/reference/run/
[SO]:http://stackoverflow.com/questions/19585028/i-lose-my-data-when-the-container-exits

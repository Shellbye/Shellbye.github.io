---
author: shellbye
comments: true
date: 2014-06-11 12:11:11+00:00
layout: post
slug: hadoop-environment-set-up
title: Hadoop环境搭建
wordpress_id: 913
categories:
- tech_world
tags:
- hadoop
- java
---

本文为电子科技大学研究生高级网络课后作业，主要参考[此文](http://codesfusion.blogspot.com/2013/10/setup-hadoop-2x-220-on-ubuntu.html)。


# 1.系统环境


在windows7中使用vmware workstation安装ubuntu来安装使用hadoop，

[![image001](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image001-300x192.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image001.png)


# 2.下载与解压


在hadoop官网（http://hadoop.apache.org/）的下载页面

（http://mirror.esocc.com/apache/hadoop/common/hadoop-2.2.0/）下载最新的稳定版hadoop，如图：

[![image003](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image003-300x85.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image003.png)



下载命令：

    
    wget http://mirror.esocc.com/apache/hadoop/common/hadoop-2.2.0/hadoop-2.2.0.tar.gz


下载成功如图所示：

[![image005](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image005-300x242.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image005.png)




解压命令：



    
     tar -xzvf hadoop-2.2.0.tar.gz




解压后如图所示：




[![image007](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image007-300x284.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image007.png)





# 3.java 环境配置




删除已有的sun jdk 环境，参考资料http://askubuntu.com/questions/84483/how-to-completely-uninstall-java




安装openjdk，使用命令：



    
    sudo apt-get install openjdk-7-jdk




`，配置相关变量，依次使用如下命令：`



    
    sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-7-openjdk-i386/bin/javac 1



    
    sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-7-openjdk-i386/bin/java 1



    
    sudo update-alternatives --set javac /usr/lib/jvm/java-7-openjdk-i386/bin/javac



    
    sudo update-alternatives --set java /usr/lib/jvm/java-7-openjdk-i386/bin/java




最后测试，使用命令



    
    java -version




显示如下，成功配置java环境。




[![image009](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image009-300x26.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image009.png)




安装openssh命令：



    
    sudo apt-get install openssh-server




至此基本环境搭建完成。





# 4. 添加hadoop用户和用户组




依次使用如下三条命令并填写相关信息：



    
    sudo addgroup hadoop



    
    sudo adduser --ingroup hadoop hduser



    
    sudo adduser hduser sudo





# 5. 设置hadoop环境变量




首先将2中解压好的hadoop文件放到/usr/local/下面，使用如下命令：



    
    mv hadoop-2.2.0 /usr/local/



    
    cd /usr/local/



    
    mv hadoop-2.2.0 hadoop




然后要给用户hadoop执行该hadoop文件的相应权限。



    
    sudo chown -R hduser:hadoop hadoop




打开home目录下的 .bashrc文件，并在文件末尾添加如下几行：




#Hadoop variables




export JAVA_HOME=/usr/lib/jvm/jdk/




export HADOOP_INSTALL=/usr/local/hadoop




export PATH=$PATH:$HADOOP_INSTALL/bin




export PATH=$PATH:$HADOOP_INSTALL/sbin




export HADOOP_MAPRED_HOME=$HADOOP_INSTALL




export HADOOP_COMMON_HOME=$HADOOP_INSTALL




export HADOOP_HDFS_HOME=$HADOOP_INSTALL




export YARN_HOME=$HADOOP_INSTALL




然后进入/usr/local/hadoop/etc/hadoop/中，修改hadoop-env.sh文件，将JAVA_HOME改为：




export JAVA_HOME=/usr/lib/jvm/jdk/




至此hadoop环境变量设置完成，退出重新用用户hadoop登录。




重新登录后输入如下命令检查hadoop版本：



    
    hadoop version




报错hadoop: command not found，但是使用如下命令却可以正确显示：



    
    /usr/local/hadoop/bin/hadoop version




原因是PATH中没有设置，使用如下命令



    
    export PATH=$PATH:/usr/local/hadoop/bin/




然后命令可以正常访问，整个过程截图如下：




 [![image011](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image011-300x220.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image011.png)




到这里为止，hadoop已安装完毕。





# 6.配置hadoop




进入/usr/local/hadoop/etc/hadoop中，编辑core-site.xml，并在<configuration>中添加如下几行：




<property>




   <name>fs.default.name</name>




   <value>hdfs://localhost:9000</value>




</property>




然后在编辑yarn-site.xml，继续在<configuration>添加如下几行：




<property>




   <name>yarn.nodemanager.aux-services</name>




   <value>mapreduce_shuffle</value>




</property>




<property>




   <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>




   <value>org.apache.hadoop.mapred.ShuffleHandler</value>




</property>




然后将mapred-site.xml.template重命名为mapred-site.xml，并依然在<configuration>中添加如下几行：




<property>




   <name>mapreduce.framework.name</name>




   <value>yarn</value>




</property>




回到home目录，新建如下两个文件夹（包含父目录），如下图：




mydata/hdfs/namenode




mydata/hdfs/datanode




[![image013](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image013-300x176.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image013.png)




重新回到/usr/local/hadoop/etc/hadoop中，编辑hdfs-site.xml，并在<configuration>中添加如下几行：




<property>




   <name>dfs.replication</name>




   <value>1</value>




 </property>




 <property>




   <name>dfs.namenode.name.dir</name>




   <value>file:/home/hduser/mydata/hdfs/namenode</value>




 </property>




 <property>




   <name>dfs.datanode.data.dir</name>




   <value>file:/home/hduser/mydata/hdfs/datanode</value>




 </property>




格式化namenode，使用如下命令：



    
    hdfs namenode -format




命令输出的开始和结束分别截图如下：




[![image015](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image015-300x137.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image015.png)




[![image017](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image017-300x201.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image017.png)




至此hadoop配置完成。





# 7.启动hadoop




进入/usr/local/hadoop/sbin中，运行如下命令启动HDFS服务：



    
    ./start-dfs.sh




启动截图如下：




[![image019](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image019-300x220.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image019.png)




然后运行如下命令启动YARN服务：



    
    ./start-yarn.sh




启动截图如下：




[![image021](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image021-300x79.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image021.png)




HDFS和YARN的关系可以简要的由下图概况：




[![image023](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image023-300x125.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image023.png)




最后，运行jsp命令检查是否需要的程序都已成功运行，成功输入如下结果：




[![image025](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image025-300x220.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image025.png)





# 8.运行hadoop自带示例




进入/usr/local/hadoop/中输入如下命令运行示例：



    
    hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar pi 2 5




运行截图头部如下：




[![image027](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image027-300x220.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image027.png)




尾部如下：




[![image029](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image029-300x220.png)](http://www.shellbye.com/blog/wp-content/uploads/2014/06/image029.png)




参考资料：




[1]. http://askubuntu.com/questions/84483/how-to-completely-uninstall-java




[2]. http://askubuntu.com/questions/302055/how-to-install-openjdk-7-jdk-on-ubuntu




-12-04-without-dragging-in-unrelated-gui-a




[3]. https://hadoop.apache.org/docs/r1.2.1/hdfs_user_guide.html




[4]. https://help.ubuntu.com/community/UsingTheTerminal




[5]. http://stackoverflow.com/questions/21369102/hadoop-command-not-found




[6]. http://zh.hortonworks.com/blog/introducing-apache-hadoop-yarn/




[7]. http://zh.hortonworks.com/hadoop/yarn/




[8]. http://docs.oracle.com/javase/1.5.0/docs/tooldocs/share/jps.html




[9]. http://codesfusion.blogspot.com/2013/10/hadoop-hdfs-tutorial-1.html




[10].http://codesfusion.blogspot.com/2013/11/hadoop-2x-core-hdfs-and-yarn-components.html




[11].




[n]. http://codesfusion.blogspot.com/2013/10/setup-hadoop-2x-220-on-ubuntu.html

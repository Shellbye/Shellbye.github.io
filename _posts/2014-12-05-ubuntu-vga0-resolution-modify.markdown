---
author: shellbye
comments: true
date: 2014-12-05 09:32:27+00:00
layout: post
slug: ubuntu-vga0-resolution-modify
title: ubuntu VGA0 resolution modify
wordpress_id: 1145
categories:
- 技术世界
tags:
- linux
- ubuntu
---

买了一个22寸的显示器，准备搭建双屏，原有的屏幕是23寸，分辨率是1920x1080，新的显示器在windows下是1680x1050，但是到了ubuntu下面，就只有1024x768，使用xrandr命令查看结果如下：

    
    Screen 0: minimum 320 x 200, current 2944 x 1080, maximum 32767 x 32767
    VGA1 connected 1024x768+1920+300 (normal left inverted right x axis y axis) 0mm x 0mm
       1024x768       60.0* 
       800x600        60.3     56.2  
       848x480        60.0  
       640x480        59.9  
    HDMI1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 521mm x 293mm
       1920x1080      60.0*+
       1680x1050      59.9  
       1280x1024      75.0     60.0  
       1440x900       59.9  
       1280x960       60.0  
       1280x720       60.0  
       1024x768       75.1     70.1     60.0  
       832x624        74.6  
       800x600        72.2     75.0     60.3     56.2  
       640x480        75.0     72.8     66.7     60.0  
       720x400        70.1  
    DP1 disconnected (normal left inverted right x axis y axis)
    VIRTUAL1 disconnected (normal left inverted right x axis y axis)
    



可以看到根本没有windown下的1680x1050，所以，就使用xrandr命令添加1680x1050的分辨率：

    
    xrandr --addmode VGA1 1680x1050



然后再重新xrandr一下可以看到已经有了

    
    Screen 0: minimum 320 x 200, current 3600 x 1350, maximum 32767 x 32767
    VGA1 connected 1680x1050+1920+300 (normal left inverted right x axis y axis) 0mm
     x 0mm
       1680x1050      59.9*    59.9* 
       1024x768       60.0  
       800x600        60.3     56.2  
       848x480        60.0  
       640x480        59.9  
    HDMI1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis)
     521mm x 293mm
       1920x1080      60.0*+
       1680x1050      59.9  
       1280x1024      75.0     60.0  
       1440x900       59.9  
       1280x960       60.0  
       1280x720       60.0  
       1024x768       75.1     70.1     60.0  
       832x624        74.6  
       800x600        72.2     75.0     60.3     56.2  
       640x480        75.0     72.8     66.7     60.0  
       720x400        70.1  
    DP1 disconnected (normal left inverted right x axis y axis)
    VIRTUAL1 disconnected (normal left inverted right x axis y axis)
    



然后就可以在系统设置里面选择分辨率了，耶！

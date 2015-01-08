---
author: shellbye
comments: true
date: 2014-04-16 13:46:16+00:00
layout: post
slug: java-graphics-fillrect
title: JAVA 图像渐变暗化 graphics fillRect
wordpress_id: 864
categories:
- tech_world
tags:
- java
---

前后折腾了大约有将近一天的工夫，终于完成了图像整体暗化（分分钟）和图像渐变暗化（近一天）的功能，时候才发现其实不应该急着写代码，仔仔细细地读文档是非常有必要的，可以少走很多的弯路。

大部分时间的浪费都是因为对Graphics中的方法的fillRect参数理解错误，在java文档中，是这样写的

    
    public abstract void fillRect(int x,
                int y,
                int width,
                int height)


于是我以为height就是要填充长方形区域的右下角的顶点位置，于是出来的图整个下半部分都是几乎全黑。后来自己用imageHelper.darkerImageBy()才发现原来用错了，在仔细读官方文档，看到是这样解释参数的：


<blockquote>Parameters:

`x` - the _x_ coordinate of the rectangle to be drawn.

`y` - the _y_ coordinate of the rectangle to be drawn.

`width` - the width of the rectangle to be drawn.

`height` - the height of the rectangle to be drawn.</blockquote>


也就是height并不是右下角点的纵坐标，而是长方形的高度，囧，改了之后顺江出来了效果。如下：

[caption id="attachment_866" align="alignnone" width="244"][![处理前](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1-244x300.jpg)](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1.jpg) 处理前[/caption]

[caption id="attachment_867" align="alignnone" width="244"][![处理后](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1397655325534-244x300.jpg)](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1397655325534.jpg) 处理后[/caption]

整体的处理是参考了stackoverflow上的[这个回答](http://stackoverflow.com/questions/12980780/how-to-change-the-brightness-of-an-image#answer-12982164)，处理后效果如下：

[caption id="attachment_869" align="alignnone" width="244"][![整体处理](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1397655628454-244x300.jpg)](http://www.shellbye.com/blog/wp-content/uploads/2014/04/1397655628454.jpg) 整体处理[/caption]

下面是全部的实现代码


    
    
    
    import javax.imageio.ImageIO;
    import java.awt.*;
    import java.awt.image.BufferedImage;
    import java.io.File;
    import java.io.IOException;
    
    /**
     * User: shellbye.com@gmail.com
     */
    public class ImageHelper {
        BufferedImage img;
        Graphics graphics;
        String orginalFileName;
        String orginalFileExt;
        String fileSaveToTemp;
        float percentage = 0.5f;
    
        public ImageHelper() {
        }
    
        public ImageHelper(File inputImage) throws IOException {
            init(inputImage);
        }
    
        public void init(File image) throws IOException {
            img = ImageIO.read(image);
            orginalFileName = image.getName();
            String[] temp = orginalFileName.split("\\.");
            orginalFileExt = temp[temp.length - 1];
            graphics = img.createGraphics();
        }
    
        public void getImage(String imageDir) throws IOException {
            File inputImage = new File(imageDir);
            init(inputImage);
        }
    
        public void darkerImageBy(float percentage) throws IOException {
            percentage = 1 - percentage;
            int brightness = (int) (256 - 256 * percentage);
            graphics.setColor(new Color(0, 0, 0, brightness));
            graphics.fillRect(0, 0, img.getWidth(), img.getHeight());
        }
    
        public void darkerImage2By(float percentage) throws IOException {
            percentage = 1 - percentage;
            int brightness = (int) (256 - 256 * percentage);
    
            // 图像切割粒度，每fineness行划分为一个小块
            int fineness = 2;
            int degree = img.getHeight() / fineness;
            int lastHeight = 0;
            for (int i = 0; i < degree; i++) {
                int tempBrightness = brightness * i / degree;
                int tempHeight = img.getHeight() * i / degree;
                graphics.setColor(new Color(0, 0, 0, tempBrightness));
                graphics.fillRect(0, lastHeight, img.getWidth(), img.getHeight() / degree);
                lastHeight = tempHeight + 1;
            }
        }
    
        public void saveImageTo(String saveToDir) throws IOException {
            File file = new File(saveToDir + System.currentTimeMillis() + "." + orginalFileExt);
            ImageIO.write(img, orginalFileExt, file);
        }
    
        public static void main(String[] args) throws IOException {
            ImageHelper imageHelper = new ImageHelper();
            imageHelper.getImage("C://Users/Administrator/Desktop/1.png");
    //        imageHelper.darkerImageBy(0.50f);
            imageHelper.darkerImage2By(0.80f);
            imageHelper.saveImageTo("C://Users/Administrator/Desktop/");
    
        }
    }

---
author: shellbye
comments: true
date: 2014-08-06 06:22:49+00:00
layout: post
slug: java-date-string-%e4%ba%92%e6%8d%a2
title: JAVA Date String 互换
wordpress_id: 998
categories:
- tech_world
tags:
- java
---

1.DateToString.java


    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    import java.util.Locale;
    import java.util.TimeZone;
    
    /**
     * User: shellbye.com@gmail.com
     */
    public class DateToString {
        public static void main(String[] arg){
            Date now = new Date();
            // 设置显示的日期格式，具体格式信息参考下面官方文档，用这个，几乎可以把时间格式化为任何样子
            // http://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html
            // 这里的第二个参数是指用英文的时间格式来格式化时间，如果没有参数就会默认使用本地设置
            SimpleDateFormat sdf = new SimpleDateFormat("E, dd-MMM-yyyy HH:mm:ss z", Locale.ENGLISH);
            // 设置时区，GMT（Greenwich Mean Time 格林尼治标准时间），
            // CST（多个含义，请参考：http://baike.baidu.com/subview/638082/14687089.htm#viewPageContent）
            sdf.setTimeZone(TimeZone.getTimeZone("GMT"));
            System.out.println("GMT time: " + sdf.format(now));
            // output
            // GMT time: Wed, 06-Aug-2014 06:10:09 GMT
        }
    }



2.StringToDate.java

    
    import java.text.SimpleDateFormat;
    import java.util.Date;
    import java.util.Locale;
    
    /**
     * User: shellbye.com@gmail.com
     */
    public class StringToDate {
        public static void main(String[] arg) throws Exception{
            String timeString = "Wed, 06-Aug-2014 06:10:09 GMT";
            SimpleDateFormat sdf = new SimpleDateFormat("E, dd-MMM-yyyy hh:mm:ss z", Locale.ENGLISH);
            Date date = (Date)sdf.parse(timeString);
            System.out.println(date.toString());
            // output,注意到这里的打印结果是使用的本地的时间格式，因为转化之后没有对Date进行设置
            // Wed Aug 06 14:10:09 CST 2014
        }
    }

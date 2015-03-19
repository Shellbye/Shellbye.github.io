---
author: shellbye
comments: true
date: 2015-03-19 11:27:08+00:00
layout: post
slug: java-httpclient-post-login
title: java httpclient post login
categories:
- 技术世界
tags:
- java
---

关于用JAVA模拟登陆的东西实在是没少写，但是一直没有怎么总结过，所以，就三大招聘网站的登录，
也写了不止一次了，昨天又需要写中华英才的模拟登陆，于是仔细整理了一下，并且最大限度的去掉了
不需要的Header信息，使代码尽量简单，于是形成了下面这个模拟登陆的过程，我测试使用的是个人
账号，企业账号应该不会差别很大吧，之前写过一次。

这次写的过程中，在POST的参数的上耗费了不少时间，居然忘记了使用NameValuePair，在表单数据上
居然异想天开的用了HashMap，于是迟迟无法登陆，后来换成了NameValuePair，瞬间就登录了。其他方面
都不是很负责，主要是中华英才的登录流程不是很复杂，只需要将用户名和密码POST到这个地址
http://www.chinahr.com/modules/jsperson/login_ajax.php?noblock=1就可以了，然后访问这个地址
http://www.chinahr.com/modules/jsperson/userinfo_json.php?noblock=1&a=1就是验证是否登录成功了。

在doGet和doPost的这两个函数里面，整体的思路都是围绕request进行的，大致概况就一下三步：

    1.初始化request，加入cookie
    2.发送request
    3.更新cookie
    
基本上都可以简单的概况为以下三行代码：

{% hightlight java %}
        installCookie(request);
        HttpResponse response = client.execute(request);
        updateCookie(response);
{% endhighlight %}

POST中稍微有所不同，因为要构造POST表单数据，我的做法是把表单数据用HashMap初始化，
然后在installFormData中使用上面提到的NameValuePair封装。

以下为全部代码，登录成功的话打印出来的数据中将包含“"is_login":true”，以及用户信息。


{% highlight java %}
package com.shellbye;

import org.apache.http.Header;
import org.apache.http.HttpResponse;
import org.apache.http.NameValuePair;
import org.apache.http.client.CookieStore;
import org.apache.http.client.HttpClient;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.methods.HttpRequestBase;
import org.apache.http.cookie.Cookie;
import org.apache.http.impl.client.BasicCookieStore;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.impl.cookie.BasicClientCookie;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.protocol.HTTP;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/18
 */
public class HttpUtils {
    private static CookieStore cs = new BasicCookieStore();
    public static String UNAME = "jianguo.bai@hirebigdata.cn";
    public static String PASSWORD = "123456";

    public static String doGet(String url) throws Exception {
        HttpClient client = HttpClientBuilder.create().build();
        HttpGet request = new HttpGet(url);

        installCookie(request);
        HttpResponse response = client.execute(request);
        updateCookie(response);

        return getHtml(response);
    }

    public static String doPost(String url) throws Exception {
        HttpClient client = HttpClientBuilder.create().build();
        HttpPost request = new HttpPost(url);

        HashMap<String, String> formData = new HashMap<String, String>();
        formData.put("uname", UNAME);
        formData.put("password", PASSWORD);
        formData.put("http_referer", "");
        formData.put("action", "login");
        formData.put("typeid", "");
        formData.put("remember", "0");
        installFormData(formData, request);

        installCookie(request);
        HttpResponse response = client.execute(request);
        updateCookie(response);

        return getHtml(response);
    }

    public static void installFormData(HashMap<String, String> parameter, HttpPost request) {
        List<NameValuePair> formData = new ArrayList<NameValuePair>();
        for (String key : parameter.keySet()) {
            formData.add(new BasicNameValuePair(key, parameter.get(key)));
        }
        request.setEntity(new UrlEncodedFormEntity(formData, HTTP.DEF_CONTENT_CHARSET));
    }

    public static String getHtml(HttpResponse response) throws Exception {
        BufferedReader rd = new BufferedReader(
                new InputStreamReader(response.getEntity().getContent()));

        StringBuffer result = new StringBuffer();
        String line;
        while ((line = rd.readLine()) != null) {
            result.append(line);
        }

        return result.toString();
    }

    public static void installCookie(HttpRequestBase request) {
        String cookieStr = "";
        List<Cookie> list = cs.getCookies();
        for (Cookie cookie : list) {
            cookieStr += cookie.getName() + "=" + cookie.getValue() + ";";
        }
        if (cookieStr.length() > 1) {
            request.addHeader("Cookie", cookieStr);
        }
    }

    public static void updateCookie(HttpResponse response) {
        Header[] headers = response.getHeaders("Set-Cookie");
        for (Header h : headers) {
            String name = h.getName();
            String value = h.getValue();

            if ("Set-Cookie".equalsIgnoreCase(name)) {
                String[] tempStr = value.split(";");
                for (String str : tempStr) {
                    String[] cookies = str.split("=");
                    cs.addCookie(new BasicClientCookie(cookies[0], cookies[1]));
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        HttpUtils.doPost("http://www.chinahr.com/modules/jsperson/login_ajax.php?noblock=1");
        String login_check = HttpUtils.doGet("http://www.chinahr.com/modules/jsperson/userinfo_json.php?noblock=1&a=1");
        System.out.println(login_check);
    }
}

{% endhighlight %}
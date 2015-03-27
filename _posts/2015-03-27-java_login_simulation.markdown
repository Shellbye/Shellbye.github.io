---
author: shellbye
comments: true
date: 2015-03-27 11:27:08+00:00
layout: post
slug: java-login-simulation
title: java login simulation
categories:
- tech_world
tags:
- java
---

前几天用httpclient写了[中华英才的模拟登陆]({% post_url 2015-03-19-java_httpclient_post_login %})，
今天又用同样的方法写了知乎的模拟登陆。本来所有的代码都可以复用，但是因为之前的那个类设计的不够好，
导致代码复用出现不少问题，于是干脆Repeat Myself了，把代码直接copy了过来复用。
但是依然没有成功，后来debug才发现原来是知乎的一个特殊的cookie导致的，该cookie的name是z_c0，它的值很奇特，
值中居然包含等号“=”，直接导致我的 ```updateCookie```函数在更新cookie时出现了问题，因为在其内部，
这个函数是通过分割等号获取cookie内部的各个键值的。
于是我用了```split```的另一个重载方法[split(String regex, int limit)](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html#split(java.lang.String,%20int))，
并将第二个参数传为2（即分割只进行一次，只分割成两个子串即可），便可以成功登录了。
回过头来想想，其实最初的提取cookie值的方法就不对，不应该直接用等号分割，因为等号太常见了，
很可能后面的值中也有，所以最佳的解决方法就是```split```的重载方法```split(String regex, int limit)```。

更新
----
重写了前面的博客中提到的java模拟登陆的HttpUtils类，按照如上所说的方式更新了cookie的获取机制，
使得该类可以更加通用的用来登陆网站，起码目前的中华英才和知乎都是测试通过了，都可以登录。

好了，Talk is cheap, 看代码吧。

先来工具类 HttpUtils

{% highlight java %}
package com.shellbye.resume;

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
 * Date: 2015/3/27
 */
public class HttpUtils {
    public CookieStore cs = new BasicCookieStore();

    public String doGet(String url){
        HttpClient client = HttpClientBuilder.create().build();
        HttpGet request = new HttpGet(url);
        try {
            installCookie(request);
            HttpResponse response = client.execute(request);
            updateCookie(response);

            return getHtml(response);
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }

    }

    public static String getHtml(HttpResponse response) {
        StringBuffer result = new StringBuffer();
        try {
            BufferedReader rd = new BufferedReader(
                    new InputStreamReader(response.getEntity().getContent()));

            String line;
            while ((line = rd.readLine()) != null) {
                result.append(line);
            }
        }catch (Exception e){
            e.printStackTrace();
        }

        return result.toString();
    }

    public void installCookie(HttpRequestBase request) {
        String cookieStr = "";
        List<Cookie> list = cs.getCookies();
        for (Cookie cookie : list) {
            cookieStr += cookie.getName() + "=" + cookie.getValue() + ";";
        }
        if (cookieStr.length() > 1) {
            request.addHeader("Cookie", cookieStr);
        }
    }

    public void updateCookie(HttpResponse response) {
        Header[] headers = response.getHeaders("Set-Cookie");
        for (Header h : headers) {
            String name = h.getName();
            String value = h.getValue();

            if ("Set-Cookie".equalsIgnoreCase(name)) {
                String[] tempStr = value.split(";");
                for (String str : tempStr) {
                    String[] cookies = str.split("=", 2);
                    if (cookies.length == 1)
                        cs.addCookie(new BasicClientCookie(cookies[0], ""));
                    else
                        cs.addCookie(new BasicClientCookie(cookies[0], cookies[1]));
                }
            }
        }
    }

    public static void installFormData(HashMap<String, String> parameter, HttpPost request) {
        List<NameValuePair> formData = new ArrayList<NameValuePair>();
        for (String key : parameter.keySet()) {
            formData.add(new BasicNameValuePair(key, parameter.get(key)));
        }
        request.setEntity(new UrlEncodedFormEntity(formData, HTTP.DEF_CONTENT_CHARSET));
    }

    public String doPost(String url, HashMap<String, String> formData){
        HttpClient client = HttpClientBuilder.create().build();
        HttpPost request = new HttpPost(url);

        installFormData(formData, request);

        installCookie(request);
        try {
            HttpResponse response = client.execute(request);
            updateCookie(response);

            return getHtml(response);
        }catch (Exception e){
            e.printStackTrace();
            return null;
        }
    }
}

{% endhighlight %}


然后是测试类HttpTest

{% highlight java %}
package com.shellbye.resume;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

import java.util.HashMap;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/27
 */
public class HttpTest {
    public static void main(String[] args){
        HttpUtils chinaHr = new HttpUtils();
        HashMap<String, String> chinaHrFormData = new HashMap<String, String>();
        chinaHrFormData.put("uname", "your_email@domain.com");
        chinaHrFormData.put("password", "can_you_guess");
        chinaHrFormData.put("http_referer", "");
        chinaHrFormData.put("action", "login");
        chinaHrFormData.put("typeid", "");
        chinaHrFormData.put("remember", "0");
        chinaHr.doPost(
                "http://www.chinahr.com/modules/jsperson/login_ajax.php?noblock=1",
                chinaHrFormData
        );
        String result = chinaHr.doGet("http://www.chinahr.com/modules/jsperson/userinfo_json.php?noblock=1&a=1");
        System.out.println(result);

        HttpUtils zhihu = new HttpUtils();
        HashMap<String, String> zhihuFormData = new HashMap<String, String>();
        zhihuFormData.put("email", "your_email@domain.com");
        zhihuFormData.put("password", "can_you_guess");
        zhihuFormData.put("_xsrf", "e499552b0cb6895d694849fd6f8bb01d");
        zhihu.doPost(
                "http://www.zhihu.com/login",
                zhihuFormData
        );
        String result2 = zhihu.doGet("http://www.zhihu.com/");
        Document doc = Jsoup.parse(result2);
        System.out.println(doc.select(".zu-top-nav-userinfo ").get(0).select(".name").text());
    }
}

{% endhighlight %}

由于我已经用[Jsoup](http://jsoup.org/)解析了知乎的网页，所以两行输出应该分别是

    {"is_login":true,.....//后面省略，但是会包含你的用户名
    知乎用户名


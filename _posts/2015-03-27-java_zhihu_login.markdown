---
author: shellbye
comments: true
date: 2015-03-27 11:27:08+00:00
layout: post
slug: java-zhihu-login
title: java zhihu login
categories:
- tech_world
tags:
- java
---

前几天用httpclient写了[中华英才的模拟登陆]({% post_url 2015-03-19-java_httpclient_post_login %})，
今天又用同样的方法写了知乎的模拟登陆。本来所有的代码都可以复用，但是因为之前的那个类设计的不够好，
导致代码复用出现不少问题，于是干脆Repeat Myself了，把代码直接copy了过来复用。
但是依然没有成功，后来debug才发现原来是知乎的一个cookie导致的，该cookie的name是z_c0，它的值很奇特，
值中居然包含等号“=”，直接导致我的 ```updateCookie```函数在更新cookie时出现了问题，因为在其内部，
这个函数是通过分割等号获取cookie内部的各个键值的。
于是我用了```split```的[另一个重载方法](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html#split(java.lang.String,%20int))，
并将第二个参数传为2（即分割只进行一次，只分割成两个子串即可），便可以成功登录了。

好了，Talk is cheap, 看代码吧。


{% highlight java %}
package resume;

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
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

/**
 * User: shellbye.com@gmail.com
 * Date: 2015/3/27
 */
public class ZhihuLogin {
    private static CookieStore cs = new BasicCookieStore();
    static String email = "jianguo.bai@hirebigdata.cn";
    static String password = "wsc111111";
    static String _xsrf = "e499552b0cb6895d694849fd6f8bb01d";
    public static void main(String[] args) throws Exception{
        doPost("http://www.zhihu.com/login");
        Document doc = Jsoup.parse(doGet("http://www.zhihu.com/"));
        System.out.println(doc.select(".zu-top-nav-userinfo ").get(0).select(".name").text());
    }

    public static String doGet(String url) throws Exception {
        HttpClient client = HttpClientBuilder.create().build();
        HttpGet request = new HttpGet(url);

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

    public static void doPost(String url) throws Exception{
        HttpClient client = HttpClientBuilder.create().build();
        HttpPost request = new HttpPost(url);

        HashMap<String, String> formData = new HashMap<String, String>();
        formData.put("email", email);
        formData.put("password", password);
        formData.put("_xsrf", _xsrf);
        installFormData(formData, request);

        installCookie(request);
        HttpResponse response = client.execute(request);
        updateCookie(response);
        return;
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
                    String[] cookies = str.split("=", 2);
                    if (cookies.length == 1)
                        cs.addCookie(new BasicClientCookie(cookies[0], ""));
                    else{
                        cs.addCookie(new BasicClientCookie(cookies[0], cookies[1]));
                    }
                }
            }
        }
    }
}

{% endhighlight %}

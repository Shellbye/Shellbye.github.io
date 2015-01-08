---
author: shellbye
comments: true
date: 2014-07-28 07:35:00+00:00
layout: post
slug: httpresponse-%e8%8e%b7%e5%8f%96html%e5%86%85%e5%ae%b9
title: HttpResponse 获取html内容
wordpress_id: 978
categories:
- tech_world
tags:
- java
---

import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.impl.client.BasicResponseHandler;
    import org.apache.http.impl.client.HttpClients;
    
    public class Main {
    
        public static void main(String[] args) throws Exception {
            HttpClient httpclient = HttpClients.createDefault();
            String location = "http://shellbye.com";
            HttpGet httpget = new HttpGet(location);
            HttpResponse getResponse2 = httpclient.execute(httpget);
            String responseString = new BasicResponseHandler()
                    .handleResponse(getResponse2);
            System.out.println(responseString);
        }
    }

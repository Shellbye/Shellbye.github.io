---
author: shellbye
comments: true
date: 2014-07-07 12:37:02+00:00
layout: post
slug: linkedin-api-message
title: LinkedIn API Message
wordpress_id: 950
categories:
- 技术世界
tags:
- API
- LinkedIn
---

因为项目需要，写了一些linkedin的接口，今天在写私信的接口的时候遇到了一些问题，折腾了很久终于解决了。

1.读文档也不能盲人摸象 比如在写linkedin私信接口时，如果光是看[这个文档](https://developer.linkedin.com/documents/messaging-between-connections-api)，那么你就悲剧了，因为它给出的信息很少。你明显可以感觉到它少了很多信息。于是乎，你就要往根目录上寻找了。比如[这篇文档](https://developer.linkedin.com/documents/writing-linkedin-apis)，就给出了一个所有接口公共的部分，这个是需要注意的一点地方。

2.尽信书不如无书 上面提到的第一份文档，里面给出了需要传入参数的一个json格式，如下：

{% highlight YAML %}
{
  "recipients": {
    "values": [
    {
      "person": {
        "_path": "/people/~",
       }
    },
    {
      "person": {
        "_path": "/people/abcdefg",
       }
    }]
  },
  "subject": "You did it.",
  "body": "This is a test from wwww.shellbye.com!"
}
{% endhighlight %}

为了防止有什么拼写错误（或者就是比较懒而已），相信大家大多和我一样，会直接从这里copy出来json数据，然后修改，不好意思，这次你完蛋了，因为上面给出的json格式是有错误的，你可以自己在[这个网站](http://www.bejson.com/)上测试，这里json数据的两个"_path"所在行都有错误，都多了结尾的逗号“,”，导致当你把这份json传回去时，linkedin会告诉你bad request，无语。。。 注：经过测试发现"/people/~"存在的意义就是把发给用户abcdefg的私信给发送者本人也发一份，所以我的demo代码里就去掉了那一个person。 以下是剪短的代码：

{% highlight java %}
import org.scribe.model.OAuthRequest;
import org.scribe.model.Response;
import org.scribe.model.Verb;

public class Main {

    public static void main(String[] args) {
        Verb type = Verb.POST;
        String url = "https://api.linkedin.com/v1/people/~/mailbox";
        String token = "thisisthethetokenfromlinkedinwhichisverylongusuallythisisthethetokenfromlinkedinwhichisverylongusuallythisisthethetokenfromlinkedinwhichisverylongusually";
        String params = "{\n" +
                "  \"recipients\": {\n" +
                "    \"values\": [\n" +
                "    {\n" +
                "      \"person\": {\n" +
                "        \"_path\": \"/people/d3oL9OfED3\"\n" +
                "       }\n" +
                "    }]\n" +
                "  },\n" +
                "  \"subject\": \"This is a test.\",\n" +
                "  \"body\": \"This is a test from www.shellbye.com!\"\n" +
                "}";
        OAuthRequest request = new OAuthRequest(type, url);
        request.addQuerystringParameter("oauth2_access_token", token);
        request.addHeader("Content-Type","application/json");//因为参数的传递方式有xml和json两种，所以这一行的作用是告诉linkedin你传来的是json的数据格式
        request.addPayload(params);
        Response response = request.send();
        int code = response.getCode();
        System.out.println(code);
    }
}
{% endhighlight %}

maven文件pom.xml

{% highlight YAML %}

 
  4.0.0
  linkedInMailBox
  war
  1.0-SNAPSHOT
  http://maven.apache.org


  
		
            org.scribe
            scribe
            1.3.5
        
  
    
   	    compile
        test
        
            
                org.apache.maven.plugins
                maven-war-plugin
                2.4
            
        
    


{% endhighlight %}


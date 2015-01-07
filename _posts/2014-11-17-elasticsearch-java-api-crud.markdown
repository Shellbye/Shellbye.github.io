---
author: shellbye
comments: true
date: 2014-11-17 13:40:56+00:00
layout: post
slug: elasticsearch-java-api-crud
title: 'Elasticsearch JAVA api CRUD '
wordpress_id: 1108
categories:
- 技术世界
tags:
- elasticsearch
- java
- lucene
---

参考[官方文档](http://www.elasticsearch.org/guide/en/elasticsearch/client/java-api/current/index.html)，将Elasticsearch的java版本的增删改查记录如下：

pom.xml文件片段:

注：这里lucene和Elasticsearch的版本号一定要配对

    
    <dependency>
        <groupid>org.apache.lucene</groupid>
        <artifactid>lucene-core</artifactid>
        <version>4.10.2</version>
    </dependency>
    <dependency>
        <groupid>org.apache.lucene</groupid>
        <artifactid>lucene-queryparser</artifactid>
        <version>4.10.2</version>
    </dependency>
    <dependency>
        <groupid>org.apache.lucene</groupid>
        <artifactid>lucene-analyzers-common</artifactid>
        <version>4.10.2</version>
    </dependency>
    <dependency>
        <groupid>org.elasticsearch</groupid>
        <artifactid>elasticsearch</artifactid>
        <version>1.4.0</version>
    </dependency>



java文件

    
    package Elasticsearch;
    
    import org.elasticsearch.action.delete.DeleteResponse;
    import org.elasticsearch.action.get.GetResponse;
    import org.elasticsearch.action.index.IndexResponse;
    import org.elasticsearch.action.search.SearchResponse;
    import org.elasticsearch.client.Client;
    import org.elasticsearch.client.transport.TransportClient;
    import org.elasticsearch.common.settings.ImmutableSettings;
    import org.elasticsearch.common.settings.Settings;
    import org.elasticsearch.common.transport.InetSocketTransportAddress;
    import org.elasticsearch.common.xcontent.XContentBuilder;
    import org.elasticsearch.node.Node;
    
    import java.util.HashMap;
    import java.util.Map;
    
    import static org.elasticsearch.node.NodeBuilder.nodeBuilder;
    import static org.elasticsearch.common.xcontent.XContentFactory.*;
    
    /**
     * Created by shellbye on 11/17/14.
     */
    public class FirstTest {
        public static void main(String[] args) throws Exception{
            System.out.println("====start=====");
    
            // on startup
            Client client = new TransportClient()
                    .addTransportAddress(new InetSocketTransportAddress("127.0.0.1", 9300));
    //======================================================================================================================
            // create json
    //        Map<String, Object> objectHashMap = new HashMap<String, Object>();
    //        objectHashMap.put("user", "shellbye2");
    //        objectHashMap.put("postDate", "2014-11-17");
    //        objectHashMap.put("message", "yes2");
    
    //======================================================================================================================
            // index ok
    //        IndexResponse indexResponse = client.prepareIndex("twitter", "tweet")
    //                .setSource(objectHashMap)
    //                .execute()
    //                .actionGet();
    //======================================================================================================================
            // get ok
    //        GetResponse getResponse = client.prepareGet("twitter", "tweet", "1")
    //                .execute()
    //                .actionGet();
    //        System.out.println(getResponse.getSourceAsString());
    
    //======================================================================================================================
            // delete ok
    //        DeleteResponse deleteResponse = client.prepareDelete("twitter", "tweet", "AUm98kB9uyGar4QgbtbY")
    //                .execute()
    //                .actionGet();
    
    //======================================================================================================================
            // search ok
            SearchResponse searchResponse = client.prepareSearch().execute().actionGet();
            System.out.println(searchResponse.toString());
    
    //======================================================================================================================
            // on shutdown
            client.close();
            System.out.println("====end=====");
        }
    }
    

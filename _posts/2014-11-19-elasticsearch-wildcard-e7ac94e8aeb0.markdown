---
author: shellbye
comments: true
date: 2014-11-19 09:34:13+00:00
layout: post
slug: elasticsearch-wildcard-%e7%ac%94%e8%ae%b0
title: Elasticsearch wildcard 笔记
wordpress_id: 1120
categories:
- 技术世界
tags:
- elasticsearch
---

仍然使用[上一篇文章](http://www.shellbye.com/blog/%E6%8A%80%E6%9C%AF%E4%B8%96%E7%95%8C/elasticsearch-rescore-%E8%87%AA%E5%AE%9A%E4%B9%89%E8%AF%84%E5%88%86/)的user.json数据，假如要搜索名字包含jack的人，则最先想到的应该就是wildcard,大概会这么写：
使用chrome的postman构造请求：
1.原始请求
url地址：http://localhost:9200/shellbye/user/_search
请求方法：post
参数（raw）：

    
    {
      "query": {
        "wildcard": {
          "name": "jack"
        }
      }
    }


返回结果：

    
    {
        "took": 3,
        "timed_out": false,
        "_shards": {
            "total": 5,
            "successful": 5,
            "failed": 0
        },
        "hits": {
            "total": 2,
            "max_score": 1,
            "hits": [
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "1",
                    "_score": 1,
                    "_source": {
                        "name": "jack",
                        "age": 30,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "2",
                    "_score": 1,
                    "_source": {
                        "name": "jack jack",
                        "age": 31,
                        "addr": "Sichuang"
                    }
                }
            ]
        }
    }


你会发现jackson这个应该也是要返回却没有返回，这个感觉不符合我对wildcard的理解哦，是不是呢？要返回所有包含（不仅仅是匹配）jack的user应该怎么做呢？那就是使用通配符“*”和“?”。
2.带通配符“*”的请求：
url地址：http://localhost:9200/shellbye/user/_search
请求方法：post
参数（raw）：

    
    {
      "query": {
        "wildcard": {
          "name": "*jack*"
        }
      }
    }


返回结果：

    
    {
        "took": 8,
        "timed_out": false,
        "_shards": {
            "total": 5,
            "successful": 5,
            "failed": 0
        },
        "hits": {
            "total": 4,
            "max_score": 1,
            "hits": [
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "4",
                    "_score": 1,
                    "_source": {
                        "name": "jackson jackson",
                        "age": 33,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "1",
                    "_score": 1,
                    "_source": {
                        "name": "jack",
                        "age": 30,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "2",
                    "_score": 1,
                    "_source": {
                        "name": "jack jack",
                        "age": 31,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "3",
                    "_score": 1,
                    "_source": {
                        "name": "jackson",
                        "age": 32,
                        "addr": "Sichuang"
                    }
                }
            ]
        }
    }


这下就所有包含jack的user就都返回了，但是官方文档也说了，不建议在wildcard的开头加通配符。所以如果不是必须要，最好就不要在开头加通配符。
3.带通配符“?”的请求：
url地址：http://localhost:9200/shellbye/user/_search
请求方法：post
参数（raw）：

    
    {
      "query": {
        "wildcard": {
          "name": "jac?"
        }
      }
    }


返回结果：

    
    {
        "took": 1,
        "timed_out": false,
        "_shards": {
            "total": 5,
            "successful": 5,
            "failed": 0
        },
        "hits": {
            "total": 2,
            "max_score": 1,
            "hits": [
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "1",
                    "_score": 1,
                    "_source": {
                        "name": "jack",
                        "age": 30,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "2",
                    "_score": 1,
                    "_source": {
                        "name": "jack jack",
                        "age": 31,
                        "addr": "Sichuang"
                    }
                }
            ]
        }
    }


注：“*”通配任何个（一个或多个）字符，“?”通配一个字符。

参考资料：
[1].[http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-wildcard-query.html

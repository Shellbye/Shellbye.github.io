---
author: shellbye
comments: true
date: 2014-11-19 04:59:04+00:00
layout: post
slug: elasticsearch-rescore-%e8%87%aa%e5%ae%9a%e4%b9%89%e8%af%84%e5%88%86
title: Elasticsearch rescore 自定义评分
wordpress_id: 1110
categories:
- 技术世界
tags:
- elasticsearch
---

昨天研究了一下elasticsearch的自定义评分功能，也就是rescore，整理如下：
0.测试数据：
user.json

    
    {"index": {"_index": "shellbye","_type": "user","_id": "1"}}
    {"name":"jack","age":30,"addr":"Sichuang"}
    {"index": {"_index": "shellbye","_type": "user","_id": "2"}}
    {"name":"jack jack","age":31,"addr":"Sichuang"}
    {"index": {"_index": "shellbye","_type": "user","_id": "3"}}
    {"name":"jackson","age":32,"addr":"Sichuang"}
    {"index": {"_index": "shellbye","_type": "user","_id": "4"}}
    {"name":"jackson jackson","age":33,"addr":"Sichuang"}



将以上文本保存到本地，命名为user.json，然后开启ES，cd到user.json所在目录并使用如下命令将数据添加到ES中。
curl -XPOST localhost:9200/_bulk?pretty --data-binary @user.json

1.basic search
使用chrome的postman构造请求：
url地址：http://localhost:9200/shellbye/user/_search
请求方法：post
参数（raw）：

    
    {
      "query": {
        "match": {
          "name": "jackson"
        }
      }
    }


返回结果如下：

    
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
            "max_score": 0.30685282,
            "hits": [
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "3",
                    "_score": 0.30685282,
                    "_source": {
                        "name": "jackson",
                        "age": 32,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "4",
                    "_score": 0.2712221,
                    "_source": {
                        "name": "jackson jackson",
                        "age": 33,
                        "addr": "Sichuang"
                    }
                }
            ]
        }
    }



2.rescore
使用chrome的postman构造请求：
url地址：http://localhost:9200/shellbye/user/_search
请求方法：post
参数（raw）：

    
    {
      "query": {
        "match": {
          "name": "jackson"
        }
      },
      "rescore": {
        "query": {
          "rescore_query": {
            "function_score": {
              "query": {
                "match_all": {}
              },
              "script_score": {
                "script": "doc['age'].value"
              }
            }
          }
        }
      }
    }



返回结果如下：


    
    {
        "took": 2,
        "timed_out": false,
        "_shards": {
            "total": 5,
            "successful": 5,
            "failed": 0
        },
        "hits": {
            "total": 2,
            "max_score": 33.27122,
            "hits": [
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "4",
                    "_score": 33.27122,
                    "_source": {
                        "name": "jackson jackson",
                        "age": 33,
                        "addr": "Sichuang"
                    }
                },
                {
                    "_index": "shellbye",
                    "_type": "user",
                    "_id": "3",
                    "_score": 32.306854,
                    "_source": {
                        "name": "jackson",
                        "age": 32,
                        "addr": "Sichuang"
                    }
                }
            ]
        }
    }



参考资料：
[1].[http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-function-score-query.html](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-function-score-query.html)
[2].[http://stackoverflow.com/questions/17314123/search-by-size-of-object-type-field-elastic-search](http://stackoverflow.com/questions/17314123/search-by-size-of-object-type-field-elastic-search)
[3].[https://www.packtpub.com/big-data-and-business-intelligence/mastering-elasticsearch](https://www.packtpub.com/big-data-and-business-intelligence/mastering-elasticsearch)

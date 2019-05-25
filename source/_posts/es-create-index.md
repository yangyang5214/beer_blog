---
title: elasticsearch 创建 index
date: 2019-05-25 10:54:51
tags: elasticsearch
---


> 记录 es index 的创建。

假设 index 为  company,type 为 company（后面 es 版本的type已删除）

### 基本

创建 index 的语句为

```
PUT company
```

查找

```
GET company
```


最最基本的 index 没有任何的设置

```
{
  "company": {
    "aliases": {},
    "mappings": {},
    "settings": {
      "index": {
        "creation_date": "1558679407106",
        "number_of_shards": "5",
        "number_of_replicas": "1",
        "uuid": "yY9ivxyYRBCU8UmQSW4n6w",
        "version": {
          "created": "5050399"
        },
        "provided_name": "company"
      }
    }
  }
}
```
<!--more-->

### 设置副本数量为0

[官网](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html 
)
number_of_shards 属性

```
PUT company/_settings
{
    "index" : {
        "number_of_replicas" : 0
    }
}

```

```
{
  "company": {
    "aliases": {},
    "mappings": {},
    "settings": {
      "index": {
        "creation_date": "1558679912411",
        "number_of_shards": "5",
        "number_of_replicas": "0",
        "uuid": "o-cYo0w3QbiUceuWp_kksw",
        "version": {
          "created": "5050399"
        },
        "provided_name": "company"
      }
    }
  }
}
```


### 设置拼音分词


https://github.com/medcl/elasticsearch-analysis-pinyin/



[官网](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html 
)


第一步：先关闭
```
POST /company/_close
```

第二步：增加分词
```
PUT /company/_settings
{
  "index": {
    "analysis": {
      "analyzer": {
        "pinyin_analyzer": {
          "tokenizer": "my_pinyin"
        }
      },
      "tokenizer": {
        "my_pinyin": {
          "type": "pinyin",
          "keep_first_letter": true,
          "keep_separate_first_letter": true,
          "keep_full_pinyin": true,
          "keep_original": false,
          "limit_first_letter_length": 16,
          "lowercase": true
        }
      }
    }
  }
}
```

第三步:
```
POST /company/_open
```

查看索引

```
{
  "company": {
    "aliases": {},
    "mappings": {},
    "settings": {
      "index": {
        "number_of_shards": "5",
        "provided_name": "company",
        "creation_date": "1558679912411",
        "analysis": {
          "analyzer": {
            "pinyin_analyzer": {
              "tokenizer": "my_pinyin"
            }
          },
          "tokenizer": {
            "my_pinyin": {
              "lowercase": "true",
              "keep_original": "false",
              "keep_first_letter": "true",
              "keep_separate_first_letter": "true",
              "type": "pinyin",
              "limit_first_letter_length": "16",
              "keep_full_pinyin": "true"
            }
          }
        },
        "number_of_replicas": "0",
        "uuid": "o-cYo0w3QbiUceuWp_kksw",
        "version": {
          "created": "5050399"
        }
      }
    }
  }
}
```


测试：pinyin_analyzer

```
GET /medcl/_analyze
{
  "text": ["刘德华"],
  "analyzer": "pinyin_analyzer"
}
```

结果：
```
{
  "tokens": [
    {
      "token": "l",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 0
    },
    {
      "token": "liu",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 0
    },
    {
      "token": "ldh",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 0
    },
    {
      "token": "d",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 1
    },
    {
      "token": "de",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 1
    },
    {
      "token": "h",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 2
    },
    {
      "token": "hua",
      "start_offset": 0,
      "end_offset": 0,
      "type": "word",
      "position": 2
    }
  ]
}
```


创建 mapping. 注意是给 字段 address 设置了分词
```
POST /company/_mapping 
{
        "properties": {
            "address": {
                "type": "keyword",
                "fields": {
                    "pinyin": {
                        "type": "text",
                        "store": false,
                        "term_vector": "with_offsets",
                        "analyzer": "pinyin_analyzer",
                        "boost": 10
                    }
                }
            }
        }
    
}
```

测试

添加一条数据

```
POST company/company/1
{
  "name" : "学妹",
  "address" : "贵州省遵义市遵义县"
}
```

查询测试

```
GET /company/company/_search
{
  "query": {
    "match_phrase": {
      "address.pinyin": "guizhou"
    }
  }
}
```
注意点：拼音分词会把你输入的中文也转化为拼音，然后进行搜索。假设你输入的是：泸州。就是转化为 luzhou.然后，结果集就不是期望的那样了。如下图：

![](https://beer-1256523277.cos.ap-shanghai.myqcloud.com/beer/blog/es_2019052501.png
)

我的解决方法是在需要拼音搜索的字段 copy 一份，然后根据需要去搜索。比如：company_name_cn 字段，新增一个 company_name_cn_py 字段。如果关键词是中文则使用 company_name_cn 匹配，如果是拼音，则使用 company_name_cn_py  匹配。（ps:目前找不到好的解决方法。20190525）

### 设置最大返回数量

```
PUT company/_settings
{
    "index" : {
        "max_result_window" : 100000
    }
}
```

查看结果：

```
{
  "company": {
    "aliases": {},
    "mappings": {
      "company": {
        "properties": {
          "address": {
            "type": "keyword",
            "fields": {
              "pinyin": {
                "type": "text",
                "boost": 10,
                "term_vector": "with_offsets",
                "analyzer": "pinyin_analyzer"
              }
            }
          },
          "name": {
            "type": "text",
            "fields": {
              "keyword": {
                "type": "keyword",
                "ignore_above": 256
              }
            }
          },
          "sex": {
            "type": "keyword",
            "fields": {
              "pinyin": {
                "type": "text",
                "boost": 10,
                "term_vector": "with_offsets",
                "analyzer": "pinyin_analyzer"
              }
            }
          },
          "test": {
            "type": "keyword",
            "fields": {
              "pinyin": {
                "type": "text",
                "boost": 10,
                "term_vector": "with_offsets",
                "analyzer": "pinyin_analyzer"
              }
            }
          }
        }
      }
    },
    "settings": {
      "index": {
        "number_of_shards": "5",
        "provided_name": "company",
        "max_result_window": "100000",
        "creation_date": "1558679912411",
        "analysis": {
          "analyzer": {
            "pinyin_analyzer": {
              "tokenizer": "my_pinyin"
            }
          },
          "tokenizer": {
            "my_pinyin": {
              "lowercase": "true",
              "keep_original": "false",
              "keep_first_letter": "true",
              "keep_separate_first_letter": "true",
              "type": "pinyin",
              "limit_first_letter_length": "16",
              "keep_full_pinyin": "true"
            }
          }
        },
        "number_of_replicas": "0",
        "uuid": "o-cYo0w3QbiUceuWp_kksw",
        "version": {
          "created": "5050399"
        }
      }
    }
  }
}
```

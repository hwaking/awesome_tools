-- ES 操作命令

# 索引管理 index
'''
1. 索引创建
curl -X PUT "localhost:9200/customer"

2. 索引查询
curl -X GET "localhost:9200/_cat/indices?v"

3. 索引删除
curl -X DELETE "localhost:9200/customer"


'''

# 文档管理 document
'''
1. 文档创建
curl -X PUT "localhost:9200/customer/_doc/1" -H 'Content-Type:application/json' -d '{"name":"Tommy"}'

2. 文档查询
curl -X GET "localhost:9200/customer/_doc/1?pretty"

3. 文档删除
curl -X DELETE "localhost:9200/customer/_doc/1"


3. 文档更新
直接在数据上更新即可
curl -X PUT "localhost:9200/customer/_doc/1" -H 'Content-Type:application/json' -d '{"name":"Tommy","age":"100"}'

'''

# ES 中数据模式
'''
<REST Verb>/<Index>/<Type>/<ID>
'''


-- ES 批处理命令

# 使用_bulk 实现数据的批量读写
'''
1. 批量插入
curl -X POST "localhost:9200/test/_doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"index":{"_id":"11"}}
{"name":"lucy"}
{"index":{"_id":"12"}}
{"name":"lily"}
'

2. 批量更新

curl -X POST "localhost:9200/test/_doc/_bulk?pretty" -H 'Content-Type: application/json' -d'
{"update":{"_id":"11"}}
{"doc": { "name": "lily becomes big lily" }}
{"delete":{"_id":"12"}}
'


3. 导入json文件
curl -H "Content-Type: application/json" -XPOST "localhost:9200/bank/_doc/_bulk?pretty&refresh" --data-binary "@accounts.json"


'''


DSL 查询检索

# 查询api
'''
1. url 查询
返回bank索引下所有的文档
curl -X GET "localhost:9200/bank/_search?q=*&sort=account_number:asc&pretty"

2. 使用请求体检索
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match_all": {} },
  "sort": [
    { "account_number": "asc" }
  ]
}
'

通过size控制返回样例数据的个数，并通过from控制样例数据起始位置
curl -X GET "localhost:9200/bank/_search" -H 'Content-Type: application/json' -d '
"query":{"match_all":{}},
"from":10,
"size":10
'
上述功能对分页查询非常有用
'''



# 搜索功能

1. 指定字段查询
默认返回原始文档hits._source, 可以请求返回源文档中的几个指定字段
如，只返回两个字段：account_number 和 balance 字段

curl -X GET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d '
{
"query":{"match_all":{}},
"_source":["account_number", "balance"]
}

相当于 select account_number, balance from bank;


* 查询account_number 为20 的文档
curl -X GET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d '
{
    "query":{"match":{"account_number":20}}
}
'
相当于 select * from bank where account_number=20;

* 查询男性
curl -X GET 'localhost:9200/bank/_search?pretty' -H 'Content-Type: application/json' -d '
{
    "query":{"match":{"gender":"M"}}
}
'
相当于SELECT * FROM bank WHERE gender ='M';

* 查询address中包含"mill"的账户
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "address": "mill" } }
}
'

相当于SELECT * FROM bank WHERE address LIKE '%mill%'

* 查询address中包含"mill"或者"lane"的账户
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": { "match": { "address": "mill lane" } }
}
'

相当于SELECT * FROM bank WHERE address LIKE '%mill' OR address LIKE '%lane%'



2. bool查询
bool查询允许我们使用布尔逻辑将较小的查询组合成较大的查询
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}
'
相当于SELECT * FROM bank WHERE address LIKE '%mill%lane%'
注意：must相当于and，shoud相当于or，must_not相当于！
      逻辑运算符：与/或/非，and/or/not，在这里就是must/should/must_not
      可以在bool查询中同时组合must、should和must_not子句。
      此外，我们可以在任何bool子句中编写bool查询，以模拟任何复杂的多级布尔逻辑。

curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "age": "40" } }
      ],
      "must_not": [
        { "match": { "state": "ID" } }
      ]
    }
  }
}
'
相当于SELECT * FROM bank WHERE age LIKE '%40%' AND state NOT LIKE '%ID%'


3. 过滤查询 

* 分数是一个数值，它是文档与我们指定的搜索查询匹配程度的相对度量（PS：相似度）。分数越高，文档越相关，分数越低，文档越不相关。

bool查询还支持filter子句，该子句允许使用查询来限制将由其他子句匹配的文档，而不改变计算分数的方式。


使用range查询，用于数字或日期过滤
用一个布尔查询返回所有余额在20000到30000之间（包括30000，BETWEEN...AND...是一个闭区间）的账户

curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}
'

4. 聚集
相当于SQL中的聚集函数，比如分组、求和、求平均数之类的

* state对所有帐户进行分组，然后按照count数降序（默认）返回前10条（默认）
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      }
    }
  }
}
'

类似于SELECT state, COUNT(*) FROM bank GROUP BY state ORDER BY COUNT(*) DESC LIMIT 10;
注意，我们将size=0设置为不显示搜索结果，因为我们只想看到响应中的聚合结果。


* 按照state分组，然后取balance的平均值
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state.keyword"
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}
'
相当于 SELECT state, COUNT(*), AVG(balance) FROM bank GROUP BY state ORDER BY COUNT(*) DESC LIMIT 10;



* 根据年龄段(20-29岁，30-39岁，40-49岁)来分组，然后根据性别分组，最后得到平均账户余额，每个年龄等级，每个性别：
curl -X GET "localhost:9200/bank/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "group_by_age": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "from": 20,
            "to": 30
          },
          {
            "from": 30,
            "to": 40
          },
          {
            "from": 40,
            "to": 50
          }
        ]
      },
      "aggs": {
        "group_by_gender": {
          "terms": {
            "field": "gender.keyword"
          },
          "aggs": {
            "average_balance": {
              "avg": {
                "field": "balance"
              }
            }
          }
        }
      }
    }
  }
}
'



































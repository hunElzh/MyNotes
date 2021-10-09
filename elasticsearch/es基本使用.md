## RESTful风格API 和 JSON格式的数据

> Elasticsearch搜索完全遵循RESTful风格，并且参数传递完全符合JSON的格式

一个Elasticsearch请求和任何HTTP请求一样由若干相同组件组成

```js
curl -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

被< >标记的部件基本介绍：

- VERB：HTTP请求方法：GET、POST、PUT、HEAD、DELETE等
- PROTOCOL：`http` 或 `https`
- HOST：Elasticsearch 集群中任意节点主机名（ip），或者用localhost代表本地机器上的节点
- PORT：运行 Elasticsearch HTTP 服务的端口号，默认是9200
- PATH：API 的终端路径（例如 `_count` 将返回集群中文档数量）。Path 可能包含多个组件，例如：`_cluster/stats` 和 `_node/stats/jvm`（这俩是啥？）
- QUERY_STRING：任意可选的查询字符串参数（例如 `?pretty` 将格式化地输出JSON返回值，使其更容易阅读）
- BODY：一个 JSON 格式的请求体

### 测试发起请求

下列命令，可以计算集群中文档的数量

```js
curl -XGET 'http://localhost:9200/_count?pretty' -d '
{
    "query": {
        "match_all": {}
    }
}
'
```

`curl` 请求返回一个像下面一样的 JSON 格式数据

```js
{
    "count" : 0,
    "_shards" : {
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    }
}
```

仔细观察我们不难发现，该返回结果中没有看到 HTTP 头信息是因为我们并没有要求 `curl` 显示它们。如果想要想要看到头信息，就需要结合 -i 参数来使用 `curl` 命令

```js
curl -i -X<VERB> '<PROTOCOL>://<HOST>:<PORT>/<PATH>?<QUERY_STRING>' -d '<BODY>'
```

我们使用 curl 发起一次es请求，会存在很多重复的地方，例如 主机名、端口号、以及`curl`命令本身。因此我们可以将上面的 crul 请求进行简化，简化格式如下

```sense
GET /_count
{
    "query": {
        "match_all": {}
    }
}
```

这个精简格式的es请求包含 VERB、PATH、BODY，通过这三个信息即可完成 es 搜索



## 面向文档

Elasticsearch 是对一个被称之为文档的对象进行一系列操作的。

在 Elasticsearch 的定义中，文档区别于传统的键和值列表。通常他们拥有更加复杂的数据结构，可能包括日期、地理信息、其他对象或数组等。

Elasticsearch 对文档进行 索引、检索、排序和过滤，要区别于对行列数据的操作。这是一种完全不同的思考方式，也是 Elasticsearch 能支持复杂全文检索的原因。

传统的关系型数据库存储对象：首先要将对象扁平化，即一个字段对应对象的一个属性。当查询对象时，又需要将其重新构造成为对象。

###  Elasticsearch 使用 JSON 表示一个 user 对象

```js
{
    "email":      "john@smith.com",
    "first_name": "John",
    "last_name":  "Smith",
    "info": {
        "bio":         "Eco-warrior and defender of the weak",
        "age":         25,
        "interests": [ "dolphins", "whales" ]
    },
    "join_date": "2014/05/01"
}
```

虽然原始的 `user` 对象很复杂，但这个对象的结构和含义在 JSON 版本中都得到了体现和保留。在 Elasticsearch 中将对象转化为 JSON 后构建索引要比在一个扁平的表结构中要简单的多。 



## 入门案例：索引员工文档

### 索引的基本介绍

- 索引（名词）：ES中存储文档的地方被称之为索引（indexes/indices），每一个存储在ES中的文档都会有一个索引，这个索引即标志着文档在ES中存储的位置。
- *索引*（动词）：将文档**存储至ES索引**的操作被称之为*索引*。从*索引*的解释上来看，它与SQL中的 `INSERT` 关键词非常相似。
- 倒排索引：ES和Lucene使用的一种特定的数据结构。在ES中，每一个属性都是*被索引*的，没有倒排索引的属性时不能被ES搜索到的。

### 需求分析

我们受雇于 *Megacorp* 公司，作为 HR 部门新的 *“热爱无人机”* （*"We love our drones!"*）激励项目的一部分，我们的任务是为此创建一个员工目录。该目录应当能培养员工认同感及支持实时、高效、动态协作，因此有一些业务需求： 

- 支持包含多值标签、数值、以及全文本的数据（给一个文档对象附上多种标签）
- 检索任一员工的完整信息（通过员工索引搜索指定员工）
- 允许结构化搜索，比如查询30岁以上的员工（像结构化数据库一般搜索范围）
- 允许简单的全文搜索以及较复杂的短语搜索（搜索一段文字的或其片段？）
- 支持在匹配文档内容中高亮显示搜索片段（ctrl + F？这样的高亮？）
- 支持基于数据创建和管理分析仪表盘（可视化？）

在ES的定义中，员工数据将会以*员工文档*的形式存储。即一个文档代表一个员工。将文档存储至ES的这一行为被称之为*索引*。



### 将员工文档加入*索引*

- 每个员工对应一个*文档*，文档包含该员工的所有信息。（以JSON形式存储）
- 每个文档都是 `employee` *类型*。
- 将该类型置于 *索引* `megacorp` 内。
- 该索引保存在我们的 ES 集群中。（目前还是单机，嘻嘻）

我们通过一下命令即可完成一个员工文档索引至 *索引*  的操作，（同理加入不同的员工2、3号）

~~~ js
PUT /megacorp/employee/1
{
    "first_name" : "John",
    "last_name" :  "Smith",
    "age" :        25,
    "about" :      "I love to go rock climbing",
    "interests": [ "sports", "music" ]
}
~~~

上面的命令包含了一下信息

- megacorp：*索引名称* 表示的实体意义是公司名
- employee：*类型名称* 表示这一类都是雇员
- 1：雇员的ID
- JSON文档：存储文档的详细信息

执行员工文档加入索引的操作

~~~ js
{
  "_index" : "megacorp",	//索引名称
  "_type" : "employee",		//类型名称
  "_id" : "1",		//雇员id
  "_version" : 2,	//将相同员工文档加入索引，此值会递增
  "result" : "updated",		//对相同索引/类型/id，第一次操作返回created，否则返回updated
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 1,  //将相同的员工文档加入索引，此值会递增
  "_primary_term" : 1
}
~~~



### 检索文档

通过最简单的 GET 请求检索我们所需要的文档（PUT是新增文档，同理DELETE即为删除指定文档）

```curl
GET /megacorp/employee/1
```

GET请求返回的文档对应的元数据信息

```js
{
  "_index" : "megacorp",
  "_type" : "employee",
  "_id" : "1",
  "_version" : 6,	//由于我重复加入索引，导致_version和_seq_no这俩属性值不为1
  "_seq_no" : 5,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {		//_source存储文档中的信息
    "first_name" : "John",
    "last_name" : "Smith",
    "age" : 25,
    "about" : "I love to go rock climbing",
    "interests" : [
      "sports",
      "music"
    ]
  }
}
```



### 轻量搜索

刚刚我们的 **检索文档** 是对获取到指定文档的信息（Megacrop公司下的第一个员工信息），而现在我们使用`_search` 方法，搜索目前公司中的所有雇员！（`_search` 默认返回10条数据）

```sense
GET /megacorp/employee/_search
```

GET请求返回的结果如下

~~~ js
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 3,
    "max_score" : 1.0,
    "hits" : [ //其中包含该ES中的所有文档信息
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "2",
        "_score" : 1.0,
        "_source" : {
          "first_name" : "Jane",
          "last_name" : "Smith",
          "age" : 32,
          "about" : "I like to collect rock albums",
          "interests" : [
            "music"
          ]
        }
      },
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 1.0,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        }
      },
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "3",
        "_score" : 1.0,
        "_source" : {
          "first_name" : "Douglas",
          "last_name" : "Fir",
          "age" : 35,
          "about" : "I like to build cabinets",
          "interests" : [
            "forestry"
          ]
        }
      }
    ]
  }
}
~~~

返回结果中返回给用户所需的所有信息，它不仅告知我们匹配了哪些文档，还包含了文档中的元数据信息

可是如果我们只想获取到姓氏为 “Smith” 的这个雇员呢？此时我们就要使用 *高亮搜索*  ，说白了就是一个涉及到  *查询字符串* 的搜索。而这个查询字符串，我们通过 URL 参数的形式传递给 ES 搜索引擎，q中包含 URL 传递的参数

```sense
GET /megacorp/employee/_search?q=last_name:Smith
```

GET 请求的返回结果如下

~~~ js
{
  "took" : 18,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "2",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "Jane",
          "last_name" : "Smith",
          "age" : 32,
          "about" : "I like to collect rock albums",
          "interests" : [
            "music"
          ]
        }
      },
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        }
      }
    ]
  }
}
~~~



### 表达式搜索

#### 基本使用（一个全文搜索的例子）

> 关键词 `query` 、`match`

表达式搜索相对于轻量搜索，他更加健壮，并且支持结构更加复杂的查询。

表达式搜索将需要搜索的内容封装在了JSON请求之中，自此就不再在URL中使用 *query-string* 参数，而是用一个请求体将其替代。

下例与轻量搜索中的 `GET /megacorp/employee/_search?q=last_name:Smith` 实现效果完全一致，但这是是表达式搜索的最基本使用，后续介绍更复杂的表达式搜索	

```sense
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "last_name" : "Smith"
        }
    }
}
```

#### 复杂表示

> 关键词`query`、`bool`、`must`、`match`、`filter`、`range`

此时需求更换，我们这次仍旧是要搜索姓氏为 Smith 的员工，但这次我们只需要年龄大于 30 岁的。这时候我们将查询稍作调整即可，使用过滤器 filter 达到目标需求。

```sense
GET /megacorp/employee/_search
{
	”query" : {
		"bool" : {
			"must" ： {
                "match" : {
                    "last_name" : "Smith"  //条件一：员工姓smith
                }
			},
			"filter" : {
				“range" : {
					"age" : { "gt" : 30 }  //条件二：员工年龄大于三十岁
				}
			}
		}
	}
}
```

> `filter` 中是一个 `range` 过滤器，`gt` 是过滤器的条件（greate than）

搜索结果如下，果然，只返回了30岁以下的员工信息

~~~ js
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.2876821,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "2",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "Jane",
          "last_name" : "Smith",
          "age" : 32,
          "about" : "I like to collect rock albums",
          "interests" : [
            "music"
          ]
        }
      }
    ]
  }
}
~~~

表达式搜索是 ES 的主要使用方式



### 全文搜索

> 关键词 `query` 、`match` 

截止目前的搜索相对都比较简单，搜索单个姓名，通过年龄过滤，这些通过传统的数据库都可以轻松实现。接下来就来尝试写稍微高级点儿的搜索——全文搜索。

全文搜索，只要与搜索的属性值有相同单词，就会被查询出来，并且每个被搜索到的 *文档* 都会被设定一个**相关性得分**。

搜索所有喜欢攀岩的员工：

```sense
GET /megacorp/employee/_search
{
    "query" : {
        "match" : {
            "about" : "rock climbing"
        }
    }
}
```

这一段语句，是在 `match` 的 `about` 属性上搜索 “rock climbing”。最终得到两个匹配的文档 ，

在这个查询结果中，我们在his对象和his数组中看到了两个属性：

- his数组中的 `_score`：与 `about` 搜索内容的相关性得分
- his对象中的 `max_score`：记录his数组中的最高的相关性得分

~~~ js
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 2,
    "max_score" : 0.5753642,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 0.5753642,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        }
      },
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "2",
        "_score" : 0.2876821,
        "_source" : {
          "first_name" : "Jane",
          "last_name" : "Smith",
          "age" : 32,
          "about" : "I like to collect rock albums",
          "interests" : [
            "music"
          ]
        }
      }
    ]
  }
}
~~~



### 短语搜索

> 关键词 `query` 、`match_phrase`

现在我们想找到一个介绍里面说自己喜欢 rock climbing 的哥们，之前的全文搜索并不奏效了，因为搜 rock climbing 还会把对应属性中带 rock 和带 climbing 的文档给整出来，但这并不符合我们的期望，因为我们现在只想找到介绍里面说自己喜欢 rock climbing 的哥们的文档。

全文搜索满足不了我们的需求了，此时就要用到另一个强力的搜索方式——短语搜索

~~~ sense
GET /megacorp/employee/_search
{
  "query": {
    "match_phrase": {
      "about": "rock climbing"
    }
  }
}
~~~

此时，我们能查找到的结果文档中，`about` 属性必须含有 ”rock climbing“，否则将不会被检索出来

~~~ js
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.5753642,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 0.5753642,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        }
      }
    ]
  }
}
~~~



### 高亮搜索

> 关键词`highlight`、`fields`

此时，我们要将搜索到的结果中高亮显示搜索的内容，这时候就要用到此部分介绍的搜索方式——高亮搜索

高亮搜索的用法其实就是在常规的表达式搜索中，添加高亮字段显示。通过下面例子理解高亮搜索

```sense
GET /megacorp/employee/_search
{
    "query" : {
        "match_phrase" : {
            "about" : "rock climbing"
        }
    },
    "highlight": {
        "fields" : {
            "about" : {}
        }
    }
}
```

当执行该查询是，返回结果与之前的短语搜索一样，但不同的是结果中多了一个叫做 `highlight` 的部分。这个部分包含了 `about` 属性匹配的文本片段，并以 HTML 中的 `<em></em>` 标签对匹配上的文本字段进行封装。

 ~~~ JS
{
  "took" : 2,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.5753642,
    "hits" : [
      {
        "_index" : "megacorp",
        "_type" : "employee",
        "_id" : "1",
        "_score" : 0.5753642,
        "_source" : {
          "first_name" : "John",
          "last_name" : "Smith",
          "age" : 25,
          "about" : "I love to go rock climbing",
          "interests" : [
            "sports",
            "music"
          ]
        },
        "highlight" : {
          "about" : [
            "I love to go <em>rock</em> <em>climbing</em>"
          ]
        }
      }
    ]
  }
}
 ~~~



### 分析（执行失败）

> 关键词`aggs`、`terms`（aggs是英文聚合aggregations的缩写）

现在是最后一个需求，统计出最受员工欢迎的兴趣爱好

ES 有一个功能叫做聚合，它允许我们基于数据生成一些精细分析结果。聚合与 SQL 中的 `GROUP BY` 类似但更强大

下例就是运用 `aggs` 挖掘出员工中最受欢迎的兴趣爱好

```sense
GET /megacorp/employee/_search
{
  "aggs": {
    "all_interests": {
      "terms": { "field": "interests" }
    }
  }
}
```

暂时忽略掉语法，我们直接看结果




























































































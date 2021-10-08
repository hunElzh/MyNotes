### RESTful风格API 和 JSON格式的数据

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

#### 测试发起请求

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



### 面向文档

Elasticsearch 是对一个名为文档的对象进行一系列操作的。

在 Elasticsearch 的定义中，文档区别于传统的键和值列表。通常他们拥有更加复杂的数据结构，可能包括日期、地理信息、其他对象或数组等。

Elasticsearch 对文档进行 索引、检索、排序和过滤，要区别于对行列数据的操作。这是一种完全不同的思考方式，也是 Elasticsearch 能支持复杂全文检索的原因。

传统的关系型数据库存储对象：首先要将对象扁平化，即一个字段对应对象的一个属性。当查询对象时，又需要将其重新构造成为对象。

####  Elasticsearch 使用 JSON 表示一个 user 对象

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














































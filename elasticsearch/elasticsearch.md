### es 文件目录结构

~~~ markdown
├── bin # 二进制脚本存放目录，包括 elasticsearch 来指定运行一个 node，包括 elasticsearch-plugin 来安装 plugins
├── config # 包含了 elasticsearch.yml 配置文件
├── data # 节点上分配的每个 index/分片 的数据文件
├── lib
├── LICENSE.txt
├── logs
├── modules
├── NOTICE.txt
├── plugins # 插键文件存放的位置
└── README.textile
~~~





### 所遇异常

~~~ markdown
1. 文件写入权限异常
Exception in thread "main" java.nio.file.AccessDeniedException: /usr/local/elasticsearch/es-7.3.0-node-1/config/jvm.options
意思是当前路径下的文件没有写权限
使用 chmod 777 path 为文件设定权限
~~~

~~~ markdown
2. es版本异常
我们要装elasticsearch6.8版本,所以要装jdk1.8,elasticsearch版本在7.0以后需要jdk1.9以上,安装是会出现和jdk不匹配
~~~


### 常用命令

- docker -v：查看版本，检查docker是否安装成功

- docker pull xxx：拉取docker中央仓库中的指定镜像（若不指定版本则为最新release版）

- docker images：查看下载的镜像

- docker run：运行镜像，将镜像启动成容器**（相当于安装软件）**
  - docker run -it --name=mysql mysql:5.7 /bin/bash（-it方式会在容器启动后直接进入容器，当对容器操作完毕后，使用exit命令是容器会跟着关闭）
  - docker run -id --name=mysql mysql:5.7（-id方式会在后台启动容器，此时若想再进入容器，需要输入 docker exec -it musql /bin/bash）
- docker ps -a 查看运行中的容器

> 启动、停止、重启、删除容器

- docker start rabbitmq：docker启动rabbitmq**（相当于启动软件）**
- docker stop rabbitmq：docker终止rabbitmq
- docker restart rabbitmq：docker重启rabbitmq

- docker rm rabbitmq：删除容器（前提是停止容器）
- docker rmi rabbitmq：删除镜像

> 针对docker的一些操作

- systemctl start docker：启动docker
- systemctl enable docker：开机启动docker
- systemctl stop docker：停止docker
- systemctl status docker：查看docker状态
- docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)：启动所有的容器命令
- docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)：关闭所有的容器命令
- docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2)：删除所有的容器命令
- docker rmi $(docker images | awk '{print $3}' |tail -n +2)：删除所有的镜像



### docker运行mysql

> 注意服务器端口是否开放；
>
> 注意mysql是否开放外部ip登录root权限；

~~~ shell
docker run -id --restart=always --privileged=true \
-p 3306:3306 \
--name=mysql \
-v /root/mysql/conf:/etc/mysql/conf.d \
-v /root/mysql/logs:/logs \
-v /root/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=lzh151151 \
mysql:5.7

//docker与mysql一系列映射
-v 目录映射(数据卷的挂载)  -v 宿主机地址:容器地址
-e 设置环境变量
~~~

参数说明：

- **-p 3306:1501**：将容器的 3306 端口映射到宿主机的 3306端口。
- **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf 配置目录
- **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs 日志目录
- **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 数据目录
- **-e MYSQL_ROOT_PASSWORD=heimaleyou：**初始化 root 用户的密码。 



### docker安装ES

~~~ shell
docker run -id --restart=always --privileged=true \
-v /root/elk/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-e "ES_JAVA_OPTS=-Xms128m -Xmx128m" --name es-node2 \
-p 9200:9200 -p 9300:9300 \
-e "discovery.type=single-node" elasticsearch:6.8.8

-v 宿主机目录 : es目录
~~~

-v把宿主机的/root/elk/elasticsearch/plugins目录挂载到ElasticSearch的plugins目录，后面安装IK分词器时会用到。

配置es集群环境

1. 进入docker容器内部：docker exec -it `<name>` /bin/bash

2. 进入/usr/share/elasticsearch/config目录，编辑目录下的elasticsearch.yml，编写es集群环境配置

   ~~~ yml
   cluster.name: "elasticsearch"   
   network.host: 0.0.0.0
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   
   # cluster.name：自定义集群名称
   # network.host：当前es节点绑定的ip地址，默认127.0.0.1，如果需要开放对外访问这个属性必须设置。
   # http.cors.enabled：是否支持跨域，默认为false
   # http.cors.allow-origin：当设置允许跨域，默认为*，表示支持所有域名，如果我们只是允许某些网站能访问，那么可以使用正则表达式
   ~~~



### docker安装redis

- 拉取redis镜像

  > docker pull redis:5.0

- 创建容器，设置端口映射和访问密码

  	>docker run -id --restart=always --privileged=true --name=redis -p 6379:6379 redis:5.0 --requirepass "lzh151151"



### docker安装rabbitmq





### docker数据卷

数据卷是什么：

- 数据卷是宿主机中的一个目录或文件
- 当容器目录和数据卷目录绑定后，对方的修改就会立即同步
- 一个数据卷可以被多个容器同时挂载，一个容器也可以被挂在多个数据卷（数据卷与容器是多对多的关系）

<img src="E:\Study\Mynotes\MyNotes\img\J$OBQJ73@A@ZM7HK.png" alt="J$OBQJ73@A@ZM7HK" style="zoom:67%;" />





### 安装docker-compose

~~~ shell
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

docker-compose --version  显示docker-compose版本
~~~





### 所遇问题

> docker run xxx拉起容器时，报错 “Failed to mount API filesystem, freezing” 
>
> 原因：权限不够
>
> 解决方法：docker run 时添加一个 --privileged 参数



> Error starting userland proxy: listen tcp4 0.0.0.0:3306: bind: address already in use.
>
> 原因：端口被占用
>
> 解决方法：1. linux输入命令 netstat -tanlp；2. kill PID 杀死3306端口进程



> Error response from daemon: driver failed programming external connectivity on endpoint mysql
>
> 原因：端口映射或网络出问题
>
> 解决方法：重启docker    systemctl restart docker 




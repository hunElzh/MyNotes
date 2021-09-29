### 常用命令

- docker -v：查看版本，检查docker是否安装成功

- docker pull xxx：拉取docker中央仓库中的指定镜像（若不指定版本则为最新release版）

- docker images：查看下载的镜像

- docker run：启动容器**（相当于安装软件）**
  - docker run -it --name=mysql mysql:5.7 /bin/bash（-it方式会在容器启动后直接进入容器，当对容器操作完毕后，使用exit命令是容器会跟着关闭）
  - docker run -id --name=mysql mysql:5.7（-id方式会在后台启动容器，此时若想再进入容器，需要输入 docker exec -it musql /bin/bash）
- docker ps -a 查看运行中的容器

> 启动、停止、重启、删除容器

- docker start rabbitmq：docker启动rabbitmq**（相当于启动软件）**
- docker stop rabbitmq：docker终止rabbitmq
- docker restart rabbitmq：docker重启rabbitmq

- docker rm rabbitmq：删除容器（前提是停止容器）
- docker rmi rabbitmq：rmi同时删除容器和镜像

> 针对docker的一些操作

- systemctl start docker：启动docker
- systemctl enable docker：开机启动docker
- systemctl stop docker：停止docker
- systemctl status docker：查看docker状态
- docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)：启动所有的容器命令
- docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)：关闭所有的容器命令
- docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2)：删除所有的容器命令
- docker rmi $(docker images | awk '{print $3}' |tail -n +2)：删除所有的镜像



### docker启动mysql

~~~ shell
docker run -id --restart=always --privileged=true \
-p 3306:1501 \
--name=mysql \
-v /root/mysql/conf:/etc/mysql/conf.d \
-v /root/mysql/logs:/logs \
-v /root/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=lzh151151 \
mysql:5.7
~~~

- 参数说明：

- - **-p 3306:1501**：将容器的 3306 端口映射到宿主机的 1501端口。
  - **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf 配置目录

- - **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs 日志目录
  - **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 数据目录

- - **-e MYSQL_ROOT_PASSWORD=heimaleyou：**初始化 root 用户的密码。



![B0FMKFXY%RX9JOWXL3V8](C:\Users\User\Desktop\MyNotes\img\B0FMKFXY%RX9JOWXL3V8.png)



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
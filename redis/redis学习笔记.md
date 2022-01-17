### redis的相关配置

环境 centos7 

版本redis-6.0.6

注意redis全局启动命令名！！！

-  *systemctl stop redis_6379* ：全局终止redis
-  *systemctl status redis_6379* ：查看redis状态
-  *systemctl start redis_6379* ：启动redis
- redis-cli -p 端口号：通过端口号连接redis客户端
- redis启动需要指定对应配置文件



redis相关文件的存放

- redis全局配置文件路径 /etc/redis/6379.conf
- redis解压后文件存放路径 /usr/local/redis
- redis安装包文件存放路径 /usr/local/redis-6.0.6
- redis主从复制配置文件存放路径 /myredis





### redis的基本使用


### CentOS7的安装



### Vmware虚拟机的三种网络模式（网络适配器）

#### 三种网络模式的基本简介

​		VMnet0（桥接模式）VMnet0表示的是用于桥接模式下的虚拟交换机；VMnet1（仅主机模式）VMnet1表示的是用于仅主机模式下的虚拟交换机；VMnet8（NAT模式）VMnet8表示的是用于NAT模式下的虚拟交换机。

​		同时，在主机上对应的有VMware Network Adapter VMnet1和VMware Network Adapter VMnet8两块虚拟网卡，它们分别作用于仅主机模式与NAT模式下。在“网络连接”中我们可以看到这两块虚拟网卡，如果将这两块卸载了，可以在vmware的“编辑”下的“虚拟网络编辑器”中点击“还原默认设置”，可重新将虚拟网卡还原。



#### Bridged（桥接模式）

​		桥接模式是将主机网卡与虚拟机网卡利用虚拟网桥进行通信。在桥接模式下，虚拟机ip地址需要与主机在同一个网段，如果需要联网，则网关与DNS需要与主机网卡一致。

​		在这种模式下，VMware 虚拟出来的操作系统就像是局域网中的一台独立的主机，它可以访问网内任何一台机器。需要手工为虚拟系统配置 IP 地址、子网掩码，而且还要和宿主机器处于同一网段，这样虚拟系统才能和宿主机器进行通信。同时，由于这个虚拟系统是局域网中的一个独立的主机系统，那么就可以手工配置它的 TCP/IP 配置信息，以实现通过局域网的网关或路由器访问互联网。



#### NAT（网络地址转换模式）

​		使用 NAT 模式，就是让虚拟系统借助 NAT（网络地址转换）功能，通过宿主机器所在的网络来访问公网。也就是说，使用 NAT 模式可以实现在虚拟系统里访问互联网，但前提是主机可以访问互联网。NAT 模式下的虚拟系统的 TCP/IP 配置信息是由 VMnet8（NAT）虚拟网络的 DHCP 服务器提供的，无法进行手工修改，因此虚拟系统也就无法和本局域网中的其他真实主机进行通讯。采用 NAT 模式最大的优势是虚拟系统接入互联网非常简单，不需要进行任何其他的配置，只需要主机器能访问互联网即可。这种情况下，主机可以 ping 通虚拟机，虚拟机也能 ping 通主机。



#### Host-Only（仅主机模式）

​		在 Host-only 模式下，虚拟网络是一个全封闭的网络，它唯一能够访问的就是主机，当然多个虚拟机之间也可以互相访问。其实 Host-only 网络和 NAT 网络很相似，不同的地方就是 Host-only 网络没有 NAT 服务，所以虚拟网络不能连接到 Internet。主机和虚拟机之间的通信是通过 VMware Network Adepter VMnet1 虚拟网卡来实现的。此时如果想要虚拟机上外网则需要主机联网并且网络共享。



### 网络配置（NAT）

参考博客https://blog.csdn.net/dif90304/article/details/101758657

- 编辑虚拟网络配置

  ~~~ markdown
  1. 进入虚拟网络编辑器
  2. 选择Vment8 NAT模式的网络适配器
  3. NAT设置：在其中设置网关
  4. DHCP设置：在其中设置ip池
  ~~~

- 使用ifconfig查看网络配置：

    ~~~ markdown
    ens33：………………
    lo：………………
    virbr0：………………
    rx packets rx bytes：接收数据包，接收比特位
    tx packets tx bytes：发送数据包，发送比特位
    ~~~

    ​		其中lo是本地网络，不用管；virbr0是虚拟网卡，可以删除；主要是配置ens33，并删除virbr0。

- 删除virbr0：

    ~~~ markdown
    [root@localhost ~]# ifconfig virbr0 down #关闭virbr0网卡
    [root@localhost ~]# brctl delbr virbr0 #删除virbr0网卡
    [root@localhost ~]# systemctl disable libvirtd.service  #因为虚拟网卡是libvirtd服务创建的，而该服务在Linux中没有太大用处，因此此处直接禁止开机启动
    ~~~

- 配置ens33：

    ~~~ markdown
    [root@localhost ~]# cd /etc/sysconfig/network-scripts/ #进入网络配置文件夹中
    [root@localhost network-scripts]# vim ifcfg-ens33 #编辑ifcfg-ens33文件
    
    以下为ifcfg-ens33具体配置
    TYPE=Ethernet #网络类型
    BOOTPROTO=none #IP获取方式，有dhcp自动获取和静态IP（none/static）
    NAME=ens33 #网络名称
    UUID=d1755a41-8026-42f1-871d-9cd78fa2aa3c
    DEVICE=ens33 #驱动名称
    ONBOOT=yes #设置开机启动
    IPADDR=192.168.137.3 #设置静态IP地址
    NETMASK=255.255.255.0 #子网掩码
    GATEWAY=192.168.137.2 #网关
    DNS1=114.114.114.114 #DNS
    DNS2=202.96.134.133
    
    注：在ens33中配置的网关IP要与 = 编辑->虚拟网络编辑器->NET模式->NET设置窗口中的网关IP = 主机网关
    ~~~

- 修改虚拟网络编辑器

    ~~~ markdown
    编辑 -> 虚拟网络编辑器 -> 更改设置（管理员运行） -> 修改子网ip
    ~~~

- 重启网络配置

    ~~~ markdown
    service network restart #重启网络配置
    reboot #重启虚拟机
    ~~~

- 防火墙设置
  - firewall -cmd --state：查看防火墙状态
  - systemctl stop firewalld.service：停止防火墙
  - systemctl disable firewalld.service：禁止防火墙开机启动

### Linux目录

- **bin(Binary) 存放二进制可执行文件(ls,cat,mkdir等)**
- **boot 存放用于系统引导时使用的各种文件**
- media linux
- dev 用于存放设备文件
- **etc 存放系统配置文件**
- **home 存放所有用户文件的根目录，新建用户后，home目录中默认生成一个用户目录**
- lib 存放跟文件系统中的程序运行所需要的共享库及内核模块
- mnt 系统管理员安装临时文件系统的安装点
- **opt 额外安装的可选应用程序包所放置的位置**
- proc 虚拟文件系统，存放当前内存的映射
- **root 该目录未系统管理员，超级用户目录**
- sbin(Super User Binary) 存放二进制可执行文件，只有root才能访问
- tmp 用于存放各种临时文件
- **usr 用于存放系统应用程序，比较重要的目录/usr/local 本地管理员软件安装目录**
- var 用于存放运行时需要改变数据的文件



### 文件夹/文件操作

- mkdir dirname：创建文件夹名为dirname的文件夹
- rmdir dirname：删除文件夹名为dirname的文件夹



- vi/vim filename：可以创建一个文件名为filename的文件
- cat filename：读文件
- vim filename：进入文件
- cp ：复制文件
- cp  -r  被复制文件（可位通配符）  目的位置：将制定文件复制至目的位置
- ln（-s）filename：建立文件的强（弱）链接
- ls：查看当前目录下的所有文件
- ll：查看当前目录下所有文件的详细信息
- pwd：查看当前目录的路径
- rm filename：删除文件
- **mv：移动文件（没用过）**
- chmod  权限  filename：修改文件权限
  - 权限[-rwx] : [4+2+1]  -------- r读权限，w写权限，x执行权限
- |：管道命令，将前面的结果传递给后面的命令



### vim文本编辑器对文本的操作

pdf的第39页

- yy：拷贝当前行；nyy 拷贝当前行向下的 n 行
- p：粘贴拷贝的内容
- dd：删除当前行；ndd删除当前行向下的 n 行
- :set nu：显示行号；:set nonu：取消行号显示
- /value：在文件中查找 value，按回车开始查找，输入 n 为查找下一个
- G：光标指向文档最末行
- gg：光标指向文档最首行
- u：在编辑模式下输入文本，切换至正常模式，按 u 以撤回编辑模式下输入的文本



### 开机、重启和用户登陆

- shutdown
  - shutdown -h now：立即关机
  - shutdown -h 1：一分钟后关机
  - shutdown -r now：立即重启
- halt
  - 直接使用，立即关机
- reboot
  - 直接使用，重启系统

- syn：把内存中的数据同步到磁盘，关机前建议执行！！！
- su- 用户名：切换用户
- 运行级别



### 用户管理

> /home/目录即为存放用户的目录，目录下有各个创建的用户对于家的目录，当用户登录时，会自动地进入到自己家的目录之下。

- 基本介绍
  - Linux 是一个多用户多任务的操作系统，因此任何一个要使用的系统资源的用户，都必须先向系统管理员申请一个账号，然后以这个账号的身份进入系统。
  - Linux 的用户需要至少一个组。

#### 用户相关操作

- 添加用户（为什么我创建完用户之后，用户目录下一个文件也没有？？？）

  > 当用户创建成功后，会自动创建和同用户名的家目录
  >
  > 但是在用户目录中我无法查看到其中内容（我可是根目录啊！！！）

  - useradd	[选项]	用户名：新添加一个用户（选项目前未知）
  - useradd	-d	指定目录	新的用户名：给新创建的用户指定家目录

- 用户密码管理

  - passwd	用户名：输入此命令后便可进行密码设置操作

- 删除用户

  - userdel	用户名：删除指定用户，但是保留 home 目录

  - userdel	-r	用户名：删除指定用户以及用户 home 目录

    为什么在删除用户时，我们一般不会将 home 目录删除？

- 查看用户信息
  - id	用户名：输入正确用户名，可以查看该用户的具体信息，具体信息如下
    - uid：用户 id 号
    - gid：用户组所在的 id 号
    - groups：用户组名
- 切换用户
  - su	-	用户名：切换至指定用户名
  - exit：从被切换用户退回至切换用户

#### 组的相关操作

- 增删用户组
  - groupadd	组名：新增指定组名的用户组
  - groupdel	组名：删除指定组名的用户组
- 在创建用户时为其分配用户组
  - useradd	-g	用户组	用户名：在创建新用户时，为其分配用户组
- 修改用户的用户组
  - usermod	-g	用户组	用户名：将指定用户移入至指定组中

#### 与用户管理相关的部分文件

- /etc/passwd：用户的配置文件，记录用户的各种信息
  - 用户名：口令：用户 id：组 id：注释性描述：主目录：登陆Shell
- /etc/shadow：*口令* 的配置文件（）
  - 登录名：命令最后一次修改时间：嘴角时间间隔：最大时间间隔：警告时间：不活动时间：失效时间：标志
- /etc/group：组的配置文件，记录 Linux 包含的组的信息
  - 组名：口令：组 id：组内用户列表



### Linux 实用指令

#### 运行级别相关指令

- init[012356]：切换至 1、2、3、5、6 这几个不同级别的指令当中
  - 0：关机
  - 1：单用户【找回丢失密码】
  - 2：多用户状态没有网络服务
  - 3：多用户状态有网络服务
  - 4：系统未使用保留给用户
  - 5：图形界面
  - 6：系统重启

#### 帮助指令

- man	命令或配置文件：获取详细的帮助信息（可惜是英文）
- help	命令：获取 shell 内置命令的帮助信息（可惜是英文）

#### 文件目录类

- pwd：获取当前工作目录的绝对路径
- ls    [选项]：显示当前目录下的信息
  - ls	-a：显示当前目录所有的文件和目录（包括隐藏文件）
  - ls	-l：以列表的方式显示文件的详细信息
  - ls	-al：以列表的方式显示文件的详细信息（包括隐藏文件）
- cd：路径切换指令
- mkdir	[选项]    要创建的目录
  - -p：创建多级目录
- rmdir	[选项]    要删除的空目录
  - rm    -rf	指定目录：迭代删除目录以及目录中的所有文件
- touch    文件名：创建指定文件名的空文件
- cp    [选项]    source    dest：拷贝文件到指定目录



### Linux进程管理

#### 查看进程

- ps [选项]：查看进程

  - -a：显示当前终端的所有进程信息
  - -u：以用户的格式显示进程信息
  - -x：显示后台进程运行的参数
  - -ef：以全格式显示当前所有的进程
    - -e：显示所有进程
    - -f：全格式

- top：查看进程状态

  > ps -A -o stat,ppid,pid,cmd | grep -e '^[Zz]' 查看 linux 中的僵尸进程
  >
  > ps -e j |grep T：查看停止进程的详细状态信息。stopped进程的STAT状态为T

- 进程参数

  - USER：用户名
  - PID：进程 id
  - PPID：父进程
  - %CPU：进程占用 cpu 的百分比
  - %MEM：进程占用物理内存的百分比
  - VSZ：进程占用的虚拟内存
  - RSS：进程占用的物理内存大小
  - TTY：终端名称（缩写）
  - STAT：进程的状态
    - S：休眠进程
    - s：父进程
    - R：运行进程
    - +：前台进程
    - Z：僵死进程（对于僵死进程应予手动终止）
    - N：低优先级进程
    - <：高优先级进程
    - D：短期等待进程
    - T：被跟踪或者被停止进程
  - START：进程的启动时间
  - TIME：进程使用CPU的总时间
  - COMMAND：进程执行时的命令行

#### 杀死进程

- kill [选项] 进程号：通过进程号杀死进程
  - -9：表示强迫进程立即停止
- killall 进程名称：通过进程名称杀死进程（支持通配符）



### Linux通配符

- *：匹配仍和字符和仍和数目的字符（LZH *：匹配LZH开头）

- ？：匹配单一数目的任何字符（L？：匹配L开头）

- []：匹配[]之内的任意一个字符

- [! ]：匹配[! ]之外的任意一个字符



### shell脚本



### 其他操作

#### 进程相关

- top ：查看线程

- ctrl + c ：退出并终止当前线程

- ps -e j |grep T：查看进程详细状态。stopped进程的STAT状态为T
  - D 不可中断 uninterruptible sleep (usually IO)
  - R 运行 runnable (on run queue)
  - S 中断 sleeping
  - T 停止 traced or stopped
  - Z 僵死 a defunct (”zombie”) process 

- ps -A -ostat,ppid,pid,cmd | grep -e '^[T]'：查看stopped的进程信息。
  - kill -9 PID：可以杀死对应PID的stopped进程，若不杀死将会阻止其他进程进行
    - PID：这是 Linux 中在其命名空间中唯一标识进程而分配给它的一个号码，称做进程ID号，简称PID。

#### 安装下载

- rpm -qa：查看所有安装包文件（通过管道过滤）

- yum remove 文件名：使用yum删除安装包文件（使用通配符批量删除）





### 常用工具

> 以下工具均是通过yum进行下载

- vim：强大的文本编辑器

- ifconfig：查看网络状态
- wget：下载网络资源







### 外部文件安装位置

- jdk：/usr/lib/jvm

- maven:  /devsoft/maven3





### 所遇问题

#### 解决xshell链接虚拟机慢的问题

问题描述：xshell连接centos7过于缓慢，并且会出现断开连接的情况

原因：ssh的服务端在连接时会自动加测dns环境是否一致，因此将其修改为不检测即可

解决方法：

1. 修改文件/etc/ssh/sshd_config
2. 将 UseDNS 修改为 no
3. 重启ssh服务：systemctl restart sshd

#### yum 安装docker失败

问题描述：yum install -y docker 报错 yum [Errno12]

原因：dns没有设置好

解决方法：

1. vim 进入网络配置文件

2. 设置合适的dns（这里我使用的是8.8.8.8和4.2.2.2）

3. service network restart：重启网络服务

   此时问题解决，yum install -y docker 安装成功
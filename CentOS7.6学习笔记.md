## 局域网网络环境

```shell
IP:192.168.0.109
GATEWAY:192.168.0.1
NETMASK:255.255.255.0
```

## 基础软件安装

```shell
#问题：主机能ping通虚拟机，虚拟机不能通主机？
#主机关闭防火墙即可

#问题：虚拟机不能上网？
#桥接模式开启在VMware中：编辑->虚拟网络编辑->更改配置->选择虚拟机网卡
#虚拟机网络配置
IPADDR=192.168.0.110
GATEWAY=192.168.0.1
PREFIX=24
DNS1=114.114.114.114
DNS2=8.8.8.8


#配置Java环境变量
#查看是否已经安装JDK
rpm -qa | grep  -i java
#如果有Java，删除系统自带的Java，java-xxx是系统上的Java
rpm -e --nodeps java-xxx
#解压安装包
tar -xzvf jdk-8u201-linux-x64.tar.gz
mv jdk-8u201-linux-x64 /usr/local/java
vi /etc/profile
#在文件末尾添加以下内容
export JAVA_HOME=/usr/local/java
export PATH=$JAVA_HOME/bin:$PATH 
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

#在线安装nginx
vi /etc/yum.repos.d/nginx.repo
#添加以下内容
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/7/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/7/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key

#安装nginx
yum install -y nginx
#查看nginx安装目录
rpm -ql nginx
#启动nginx
systemctl start nginx

#nginx的启动脚本位置
/usr/lib/systemd/system/nginx.service
#内容是
[Unit]
Description=nginx - high performance web server
Documentation=http://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target



#开机启动服务学习
[Unit]
Description=开机启动学习
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/echo.pid
ExecStart=/home/shell/echoNow

[Install]
WantedBy=multi-user.target

#自己写的脚本执行报203错误(code=exited, status=203/EXEC)，原因是脚本最上面没有加#!/bin/sh

#测试结果设置软件启动的先后顺序使用After在什么服务启动之后启动即可

#ssh链接错误Socket error Event: 32 Error: 10053
#确认原因为IP冲突

#安装MySQL
#卸载系统自带的Mariadb
rpm -qa|grep mariadb
#显示以下内容
mariadb-libs-5.5.60-1.el7_5.x86_64
#卸载系统自带的数据库：mariadb-libs-5.5.60-1.el7_5.x86_64(此处自带的系统为系统上查出来的Mariadb)
rpm -e --nodeps mariadb-libs-5.5.60-1.el7_5.x86_64
#确认是否卸载成功，以下命令没有任何返回值即表示成功
rpm -qa|grep mariadb
#解压安装包
tar -zxf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
#移动至安装目录
mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
#检查MySQL是否存在（如果存在，可以使用mysql数据库）
rpm -qa | grep mysql
#安装MySQL首先删除etc目录下的my.cnf文件，不安装就不用删除
rm /etc/my.cnf
#显示rm: 无法删除"/etc/my.cnf": 没有那个文件或目录（系统一般没有，如果有就删除）
#检查mysql用户名和用户组是否存在
cat /etc/group | grep mysql
cat /etc/passwd | grep mysql
#mysql用户组和用户名不存在时创建
useradd -M -s /sbin/nologin mysql
#设置MySQL用户密码（此处密码设置为123456）
passwd mysql
cd /usr/local/mysql
mkdir data
cd /usr/local/
chown -R mysql:mysql ./mysql/
cd /usr/local/mysql
#初始化MySQL
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
#命令执行结果
2019-06-28T14:58:52.645430Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2019-06-28T14:58:52.858226Z 0 [Warning] InnoDB: New log files created, LSN=45790
2019-06-28T14:58:52.898654Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2019-06-28T14:58:52.958460Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 3e94dd3e-99b5-11e9-b729-000c29579c0c.
2019-06-28T14:58:52.959502Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2019-06-28T14:58:52.960461Z 1 [Note] A temporary password is generated for root@localhost: DlujbL)A=4=D
#注意以上最后的密码
#A temporary password is generated for root@localhost: DlujbL)A=4=D
#启用加密链接数据库，这一步必须在初始化数据之后，初始化时data中不能有文件
bin/mysql_ssl_rsa_setup  --datadir=/usr/local/mysql/data
#修改data中的SSL文件的所属用户及用户组
chown -R mysql:mysql ./data/
#或者直接创建/etc/my.cnf
vi /etc/my.cnf
#my.cnf内容如下，以下到event_scheduler = on结束

[mysql]
# 设置mysql客户端默认字符集，尝试utf8mb4
default-character-set=utf8mb4 
[mysqld]
#禁用DNS解析，加快访问速度
skip-name-resolve
#无操作超时退出：默认8个小时8*3600=28800秒
interactive_timeout=288000
#设置3366端口
port = 3366 
# 设置mysql的安装目录
basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放目录
datadir=/usr/local/mysql/data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
#0表名区分大小写，1表名不区分大小写
lower_case_table_names=0
#数据库可以接受的最大数据包
max_allowed_packet=16M
# 计划事件开启:
event_scheduler = on

#修改脚本权限
chmod 644 /etc/my.cnf
#添加mysql命令链接，在任何地方都能直接使用mysql命令
ln -s /usr/local/mysql/bin/mysql /usr/bin
#添加mysqldump命令软连接在备份数据库使用
ln -s /usr/local/mysql/bin/mysqldump /usr/bin
#设置开机启动服务
vi /usr/lib/systemd/system/mysql.service

#开机启动服务内容
[Unit]
Description=MySQL Server5.7.24
Documentation=mysql(5.7.24)
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
PIDFile=/var/run/mysql/mysql.pid
User=mysql
Group=mysql
ExecStart=/usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf
#进程打开的最大文件数
LimitNOFILE = 5000


#启动MySQL
systemctl start mysql
#查看MySQL状态：是active(running)表示启动成功
systemctl status mysql

#进入MySQL
mysql -u root -p
#输入初始化的密码
password:
#修改root密码，不修改无法进行其他操作
mysql>set password=password('123456');
#添加不限制IP访问的新用户、
mysql>GRANT ALL PRIVILEGES ON *.* TO 'zouyaowen'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
#刷新设置
mysql>flush privileges;
#退出mysql
mysql>exit

#设置开机启动
systemctl enable mysql



#更换阿里云镜像源
yum -y install wget
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
#或者
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

#清除缓存
yum clean all  
#生成缓存
yum makecache


#安装Redis
tar -zxf redis-5.0.2.tar.gz -C /usr/local/
mv redis-5.0.2 redis



#开启15672端口public(作用域)、15672/tcp(端口和访问类型)、permanent(永久生效)
firewall-cmd --zone=public --add-port=15672/tcp --permanent
#开启80端口public(作用域)、5672/tcp(端口和访问类型)、permanent(永久生效)
firewall-cmd --zone=public --add-port=5672/tcp --permanent
firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
firewall-cmd --zone=public --add-port=3366/tcp --permanent
firewall-cmd --zone=public --add-port=8679/tcp --permanent

#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示80/tcp表示成功
firewall-cmd --list-ports

```

## Docker学习

```shell
#安装docker
yum -y install docker
#启动docker
systemctl start docker
#查看docker的状态
systemctl status docker

#修改国内的镜像源
vi /etc/docker/daemon.json
#修改成以下地址
{
    "registry-mirrors": [
        "加速地址"
    ],
    "insecure-registries": []
}
#国内加速地址
#Docker中国区官方镜像(http://www.docker-cn.com/registry-mirror) 
https://registry.docker-cn.com
#网易 
http://hub-mirror.c.163.com
#ustc 
https://docker.mirrors.ustc.edu.cn
#阿里（使用正常）
https://kfwkfulq.mirror.aliyuncs.com
#最后设置完一定要重启一下docker 
service docker restart








#查看已经下载的docker镜像:镜像只是一个只读的模板，像是一个系统光盘镜像
docker images
#测试docker是否安装成功
docker run hello-world
#以下内容表示成功
Unable to find image 'hello-world:latest' locally
Trying to pull repository docker.io/library/hello-world ... 
latest: Pulling from docker.io/library/hello-world
1b930d010525: Pull complete 
Digest: sha256:41a65640635299bab090f783209c1e3a3f11934cf7756b09cb2f1e02147c6ed8
Status: Downloaded newer image for docker.io/hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
#搜索镜像
docker search java
#下载镜像
docker pull java
#删除指定名称的镜像
docker rmi hello-world
#删除所有镜像
docker rmi -f $(docker images)
#保存镜像
docker save busybox > busybox.tar
docker save --output busybox.tar busybox
#加载保存的镜像文件
docker load < busybox.tar.gz
docker load --input fedora.tar

#通过Dockerfile构建镜像
docker build [OPTIONS] PATH | URL | -

#新建并启动容器
docker run 
-d:表示后台运行
-P:随机端口映射(大写的P)
-p:指定端口映射（小写的p）,有四种格式
    ip:hostPort:containerPort
    ip::containerPort
    hostPort:containerPort
    containerPort
–network:指定网络配置选择，有以下四种网络配置
–network=bridge： 默认选项，表示连接到默认的网桥
–network=host：容器使用宿主机的网络
–network=container:NAME_or_ID：告诉Docker让新建的容器使用已有容器的网络配置
–network=none：不配置该容器的网络，用户可自定义网络配置
#例：在Java镜像内执行/bin/echo 'Hello World'
docker run java /bin/echo 'Hello World'
#运行nginx
docker run -d -p 91:80 nginx
#以上命令运行Nginx会运行成功后Nginx容器会停止，以下命令不会停止
docker run -p 80:80 -d nginx
#访问http://Docker宿主机IP:91/
#需要注意的是，使用docker run命令创建容器时，会先检查本地是否存在指定镜像。如果本地不存在该名称的镜像，Docker就会自动从Docker Hub下载镜像并启动一个Docker容器

#通过主机目录映射到容器
docker  run  -p  80:80  -d  -v  $PWD/html:usr/share/nginx/html  docker.io/nginx
#-v  $PWD/html:usr/share/nginx/html   表示把当前路径下html目录映射为usr/share/nginx/html
#也就是说主机下的html就是容器下的usr/share/nginx/html
#html内的文件修改和添加就等同于容器usr/share/nginx/html文件操作
#外网访问就可以访问得到，就不用再登录容器操作文件了

#查看运行中的容器
docker ps
该表格包含了七列，含义如下：
① CONTAINER_ID：表示容器ID。
② IMAGE：表示镜像名称。
③ COMMAND：表示启动容器时运行的命令。
④ CREATED：表示容器的创建时间。
⑤ STATUS：表示容器运行的状态。Up表示运行中，Exited表示已停止。
⑥ PORTS：表示容器对外的端口号。
⑦ NAMES：表示容器名称。该名称默认由Docker自动生成，也可使用docker run命令的–name选项自行指定。

```

命令格式：

```
docker ps [OPTIONS]
```

参数：

| Name, shorthand | Default | Description                                          |
| --------------- | ------- | ---------------------------------------------------- |
| `--all, -a`     | `false` | 列出所有容器，包括未运行的容器，默认只展示运行的容器 |
| `--filter, -f`  |         | 根据条件过滤显示内容                                 |
| `--format`      |         | 通过Go语言模板文件展示镜像                           |
| `--last, -n`    | `-1`    | 显示最近创建n个容器（包含所有状态）                  |
| `--latest, -l`  | `false` | 显示最近创建的容器（包含所有状态）                   |
| `--no-trunc`    | `false` | 不截断输出                                           |
| `--quiet, -q`   | `false` | 静默模式，只展示容器的编号                           |
| `--size, -s`    | `false` | 显示总文件大小                                       |

```shell
#停止运行的容器,容器唯一标识与名称都可以
docker stop 784fd3b294d7
#强制停止
docker kill 784fd3b294d7
#启动已经停止的容器
docker start 784fd3b294d7
#重启容器,先执行docker stop再docker start
docker restart 784fd3b294d7


#进入已经运行的容器
#第一种方式(测试显示进nginx容器内部容器就会停止)
docker attach 784fd3b294d7
#很多场景下，使用docker attach 命令并不方便。当多个窗口同时attach到同一个容器时，所有窗口都会同步显示。同理，如果某个窗口发生阻塞，其他窗口也无法执行操作。

#第二种进入容器的方式：使用nsenter
#nsenter工具包含在util-linux 2.23或更高版本中。为了连接到容器，我们需要找到容器第一个进程的PID，可通过以下命令获取
docker inspect --format "{{.State.Pid}}" $CONTAINER_ID
#获得PID后，就可使用nsenter命令进入容器了
nsenter --target "$PID" --mount --uts --ipc --net --pid
#例
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
784fd3b294d7        nginx               "nginx -g 'daemon off"   55 minutes ago      Up 3 minutes        443/tcp, 0.0.0.0:91->80/tcp   backstabbing_archimedes
[root@localhost ~]# docker inspect --format "{{.State.Pid}}" 784fd3b294d7
95492
[root@localhost ~]# nsenter --target 95492 --mount --uts --ipc --net --pid
root@784fd3b294d7:/#
#也可将以上两条命令封装成一个Shell，从而简化进入容器的过程



#第三种进入容器的方式：docker exec
docker exec -it 容器id /bin/bash



#删除容器：docker rm
docker rm 784fd3b294d7
#删除所有容器
docker rm -f $(docker ps -a -q)



#导出容器：docker，将容器导出成一个压缩包文件
docker export [OPTIONS] CONTAINER
#例
docker export red_panda > latest.tar
docker export --output="latest.tar" red_panda


#导入容器：使用docker import 命令即可从归档文件导入内容并创建镜像
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
参数：
Name,   shorthand	     Default	       Description
--change, -c		                       将Dockerfile指令应用到创建的镜像
--message, -m		                       为导入的镜像设置提交信息
#例
docker import nginx2.tar nginx

#进入容器
docker exec -it 容器id /bin/bash
#例
docker exec -it 容器ID /bin/bash




#数据卷之间可以共享数据


```

## DockerFile学习

```shell
#简单的DockerFile学习,VOLUME处于可移植性考虑，只能设置容器内的目录

#第一步编写可执行脚本
From centos
VOLUME ["/home/dataVolumeContainer1","/home/dataVolumeContainer2"]
CMD echo "docker build success…… finished!"
CMD /bin/bash

#生成新的镜像,不要忘记最后那一个点，脚本名称如果是Dockerfile可以不用加-f
docker build -f /可执行脚本路径 -t 镜像名称:版本号 .
#执行构建命令
chmod u+x Dockerfile
docker build -f /home/docker/Dockerfile -t zouyaowen .

#Dockerfile基础知识

#每个保留字指令必须大写，而且指令后面必须加一个参数
#指令从上到下顺序执行
#井号表示注释
#每条指令都会创建一个新的镜像层，并对镜像进行提交

#DocekrFile主要命令
#FROM、MAINTAINER、RUN、EXPOSE、WORKDIR、ENV、ADD、COPY、VOLUME、CMD、ENTRYPOINT、ONBUILD

#FROM：基础镜像，当前镜像基于哪一个镜像
#MAINTAINER：镜像维护者的姓名和邮箱zyw<1227701903@qq.com>
#RUN:容器构建时需要运行的命令
#EXPOSE：对外暴露的端口
#WORKDIR：指定创建容器后，终端默认登录进来的工作目录
#ENV：定义环境变量，在其它指令中可以使用
#例
ENV INIT_PATH /usr/local
WORKDIR $INIT_PATH
#ADD:复制加解压缩
#COPY：单纯的复制，两种写法：COPY src dst/COPY ["src","dst"]
#VOLUME:数据卷["/home/dataVolumeContainer1","/home/dataVolumeContainer2"]
#CMD:最后一行命令起作用
#ENTRYPOINT：不会被替换，命令会追加
#ONBUILD：被继承是触发



#构建Dockerfile案例
FROM centos

ENV INIT_PATH /usr/local
WORKDIR $INIT_PATH

RUN yum -y install vim
RUN yum -y install net-tools
RUN yum -y install wget curl

EXPOSE 80
EXPOSE 8080
EXPOSE 8679
EXPOSE 3366
EXPOSE 5672
EXPOSE 15672

CMD echo "build-----------------secussful"
CMD /bin/bash

#文件夹名称为Dockerfile2，构建命令
docker build -f /home/docker/Dockerfile2 -t zCentOS:1.0 .
#以上镜像名称不能有大写字母
docker build -f /home/docker/Dockerfile2 -t zcentos:1.0 .
#运行镜像需要加版本号
docker run -it zcentos:1.0 /bin/bash

```



## 自定义Tomcat镜像

```shell
FROM centos
MAINTAINER zyw<1227701903@qq.com>
#将宿主机当前上下文文件c.txt复制进/usr/local文件夹下并重命名
COPY c.txt /usr/local/container.txt
#将Java与Tomcat添加进容器内
ADD jdk-8u201-linux-x64.tar.gz /usr/local
ADD apache-tomcat-8.5.41.tar.gz /usr/local
#安装vim编辑器
yum -y install vim
#设置终端访问默认路径
ENV INIT_PATH /usr/local
WORKDIR $INIT_PATH
#配置Java与Tomcat环境变量
ENV JAVA_HOME /usr/local/jdk-8u201-linux-x64
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-8.5.41
ENV CATALINA_BASE /usr/local/apache-tomcat-8.5.41
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
#容器运行时监听的端口
EXPOSE 8080
#启动tomcat
#ENTRYPOINT ["/usr/local/apache-tomcat-8.5.41/bin/start.sh"]
#CMD ["/usr/local/apache-tomcat-8.5.41/bin/catalina.sh","run"]
CMD /usr/local/apache-tomcat-8.5.41/bin/start.sh && tail -F /usr/local/apache-tomcat-8.5.41/logs/catalina.out


```


















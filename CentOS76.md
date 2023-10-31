# CentOS7.6环境搭建 笔记

# IP配置

```shell
#以下为CentOS7.6虚拟机配置，桥接模式
cd /etc/sysconfig/network-scripts/
vi ifcfg-ens33
#以下为配置内容
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#修改为static
BOOTPROTO=static
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=92712a7c-25b4-48c0-a77b-2aac00083e43
DEVICE=ens33
#修改为yes
ONBOOT=yes

#添加网络四要素配置
IPADDR=192.168.0.100
PREFIX=24
GATEWAY=192.168.0.1
DNS1=114.114.114.114
DNS2=8.8.8.8
```

# 服务器配置

```shell
#关闭Selinux
vi /etc/selinux/config
#修改SELINUX=enforcing为SELINUX=disabled

#替换国内的yum源
#备份默认的yum源
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
#或者
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo


# 腾讯云服务器更换服务器名称
#  vi /etc/hostname  修改后重启即可
```



# 上传安装文件

```shell
[root@localhost software]# ls
jdk-8u212-linux-x64.tar.gz  mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz  zookeeper-3.4.9.tar.gz
kafka_2.12-2.1.1.tgz        mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz  redis-5.0.2.tar.gz
```

# JDK1.8离线安装

```shell
#解压安装包
tar -xzf jdk-8u212-linux-x64.tar.gz
mv jdk1.8.0_212 /usr/local/java
vi /etc/profile
#在文件末尾添加以下内容
export JAVA_HOME=/usr/local/java
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

#重新加载配置文件
source /etc/profile
```

# 在线安装Nginx

```shell
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
[root@localhost software]# rpm -ql nginx
/etc/logrotate.d/nginx
/etc/nginx
/etc/nginx/conf.d
/etc/nginx/conf.d/default.conf
/etc/nginx/fastcgi_params
/etc/nginx/koi-utf
/etc/nginx/koi-win
/etc/nginx/mime.types
/etc/nginx/modules
/etc/nginx/nginx.conf
/etc/nginx/scgi_params
/etc/nginx/uwsgi_params
/etc/nginx/win-utf
/etc/sysconfig/nginx
/etc/sysconfig/nginx-debug
/usr/lib/systemd/system/nginx-debug.service
/usr/lib/systemd/system/nginx.service
/usr/lib64/nginx
/usr/lib64/nginx/modules
/usr/libexec/initscripts/legacy-actions/nginx
/usr/libexec/initscripts/legacy-actions/nginx/check-reload
/usr/libexec/initscripts/legacy-actions/nginx/upgrade
/usr/sbin/nginx
/usr/sbin/nginx-debug
/usr/share/doc/nginx-1.16.0
/usr/share/doc/nginx-1.16.0/COPYRIGHT
/usr/share/man/man8/nginx.8.gz
/usr/share/nginx
/usr/share/nginx/html
/usr/share/nginx/html/50x.html
/usr/share/nginx/html/index.html
/var/cache/nginx
/var/log/nginx
#查看Nginx配置文件语法是否正确
[root@localhost /]# nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
#启动nginx
systemctl start nginx
#启动失败
Job for nginx.service failed because a configured resource limit was exceeded. See "systemctl status nginx.service" and "journalctl -xe" for details.
#失败原因
[root@localhost /]# journalctl -xe
7月 17 00:43:00 localhost.localdomain groupadd[15245]: group added to /etc/gshadow: name=nginx
7月 17 00:43:00 localhost.localdomain groupadd[15245]: new group: name=nginx, GID=995
7月 17 00:43:00 localhost.localdomain useradd[15250]: new user: name=nginx, UID=997, GID=995, home=/va7月 17 00:43:01 localhost.localdomain systemd[1]: Reloading.
7月 17 00:43:01 localhost.localdomain systemd[1]: Reloading.
7月 17 00:43:01 localhost.localdomain yum[15235]: Installed: 1:nginx-1.16.0-1.el7.ngx.x86_64
7月 17 00:46:32 localhost.localdomain polkitd[6239]: Registered Authentication Agent for unix-process:7月 17 00:46:32 localhost.localdomain systemd[1]: Starting nginx - high performance web server...
-- Subject: Unit nginx.service has begun start-up
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
-- 
-- Unit nginx.service has begun starting up.
7月 17 00:46:32 localhost.localdomain systemd[1]: Failed to read PID from file /var/run/nginx.pid: Inv7月 17 00:46:32 localhost.localdomain nginx[15359]: nginx: [emerg] open() "/var/run/nginx.pid" failed 
7月 17 00:46:32 localhost.localdomain systemd[1]: nginx.service never wrote its PID file. Failing.
7月 17 00:46:32 localhost.localdomain systemd[1]: Failed to start nginx - high performance web server.-- Subject: Unit nginx.service has failed
-- Defined-By: systemd
-- Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
-- 
-- Unit nginx.service has failed.
-- 
-- The result is failed.
7月 17 00:46:32 localhost.localdomain systemd[1]: Unit nginx.service entered failed state.
7月 17 00:46:32 localhost.localdomain systemd[1]: nginx.service failed.
7月 17 00:46:32 localhost.localdomain polkitd[6239]: Unregistered Authentication Agent for unix-proces

#重新启动就可以成功了
systemctl start nginx

firewall-cmd --zone=public --add-port=80/tcp --permanent
firewall-cmd --zone=public --add-port=443/tcp --permanent
#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示80/tcp表示成功
firewall-cmd --list-ports
```

# 离线安装Nginx

```shell
# 安装编译软件
yum install -y gcc-c++
yum install -y pcre pcre-devel
yum install -y openssl openssl-devel
yum install -y zlib zlib-devel

# 下载nginx软件
wget https://nginx.org/download/nginx-1.22.0.tar.gz

# 解压软件
tar -zxf nginx-1.22.0.tar.gz
cd nginx-1.22.0
# 配置软件
#./configure --prefix=/usr/local/nginx/
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
# 安装软件   增加模块重新编译后make后替换编译产物nginx即可，make install 会重装
make && make install

# 配置环境变量
export PATH=/usr/local/nginx/sbin:$PATH
# 重新加载环境变量
source /etc/profile

# 查看Nginx配置是否正确
nginx -t

# 启动Nginx
nginx


```

## Nginx配置文件参考

```shell

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    
    # 隐藏版本号
    server_tokens off;
    
    # header传递允许有带下划线字段
    underscores_in_headers on;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    server {
        listen       80;
        server_name  suiyueran.top;
        charset utf-8;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        # 将请求转成https
        rewrite ^(.*)$ https://$host$1 permanent;


        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        #  error_page   500 502 503 504  /50x.html;
        #  location = /50x.html {
        #      root   html;
        #  }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    
    server {
        listen       443 ssl;
        server_name  suiyueran.top;
        ssl_certificate      /usr/local/nginx/conf/suiyueran.top.pem;
        ssl_certificate_key  /usr/local/nginx/conf/suiyueran.top.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}


```



## Nginx前后端配置

```nginx

# nignx页面域名直接访问
location / {
		root /usr/share/nginx/html
		index index.htm index.html
}

# 多个前端系统部署同一个Nginx配置方案,域名添加路径转发
# 页面域名后加路径转发配置方案一
location /1234 {
		alias /work/front-end/1234;
		index index.htm index.html;
}

# 页面域名后加路径转发配置方案二
location /1235 {
		root /work/front-end/;
		try_files $uri /1235/index.html;
}

# 本地端口转发到另一个端口 在配置proxy_pass代理转发时，如果后面的url加/，表示绝对根路径；如果没有/，表示相对路径
location /api/ {
    # proxy_pass http:127.0.0.1:8081      实际映射关系     域名/api/URI---->   域名:端口/api/URI
    # proxy_pass http:127.0.0.1:8081/     实际映射关系     域名/api/URI---->   域名:端口/URI
		proxy_pass http:127.0.0.1:8081/
		# 总结：后端接口转发location 映射前后都需要有/    proxy_pass最后没有/拼接location，有则不拼接
}

# 将域名转发到本地端口
location / {
		proxy_pass http:127.0.0.1:8081;
		# 修改转发请求头，让8080端口的应用可以受到真实的请求
		proxy_set_header Host $proxy_host; 
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}

# 域名转发到另外一个域名
server{
  listen 80;
  server_name  www.qq.com;
  index  index.php index.html index.htm;

  location / {
    proxy_pass  http://www.baidu.com;
    proxy_set_header Host $proxy_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}


```

## Nginx高级配置

```shell
# 根据header参数转发不同的服务   header头中env=env_8081   获取key对应的值  $http_env  
location ~/front/(.*) {
    proxy_set_header Host $proxy_host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    if ($http_env = "env_8081"){
        proxy_pass http://node_v1/$1;
        break;
    }
    if ($http_env = "env_8082"){
        proxy_pass http://node_v2/$1;
        break;
    }
}


# cation匹配命令

~      #波浪线表示执行一个正则匹配，区分大小写
~*     #表示执行一个正则匹配，不区分大小写
^~     #^~表示普通字符匹配，不是正则匹配。如果该选项匹配，只匹配该选项，不匹配别的选项，一般用来匹配目录
=      #进行普通字符精确匹配
@      #"@" 定义一个命名的 location，使用在内部定向时，例如 error_page, try_files



# 参考
https://blog.51cto.com/u_15127532/3942184
https://segmentfault.com/a/1190000002797606?spm=a2c6h.12873639.article-detail.6.489465b0r2BYIq

```





## Nginx配置理解

```nginx

location ~/abc/(.*)/(.*) {
    set $param1 $1
    set $param2 $2
    content_by_lua_block {
        ngx.say(ngx.var.param1)
        ngx.say(ngx.var.param2)
    }
}
# 路径为  domain/abc/aaa/bbb
# 输出为： 
aaa
bbb
```



## Nginx防止爬虫压测

```shell
# 配置目录conf下面   agent_deny.conf

# 禁止Scrapy等工具的抓取
if ($http_user_agent ~* (Scrapy|Curl|HttpClient)) {
  return 403;
}
# 禁止指定UA及UA为空的访问
if ($http_user_agent ~ "FeedDemon|JikeSpider|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|YisouSpider|HttpClient|MJ12bot|heritrix|EasouSpider|LinkpadBot|Ezooms|^$" )
{
  return 403;
}
# 禁止非GET|HEAD|POST|PUT|DELETE 方式的抓取
if ($request_method !~ ^(GET|HEAD|POST|PUT|DELETE)$) {
  return 403;
}
 
# 禁止使用代理ip来访问，或禁止使用压力测试软件进行dos攻击
  
if ($http_user_agent ~* ApacheBench|WebBench|java/){
                return 403;
        }
if ($http_user_agent ~* (Wget|ab) ) {
   return 403;
}
 
if ($http_user_agent ~* LWP::Simple|BBBike|wget) {
            return 403;
}



# 在配置文件server中  插入如下代码   插入到server还是location中需要验证
include agent_deny.conf;
```

## Nginx参考资料

```shell
https://zhuanlan.zhihu.com/p/384752564
# 前端灰度方案
https://juejin.cn/post/6960932304432660511
# lua发动http请求
https://blog.csdn.net/zhangxiao93/article/details/69220644
https://cloud.tencent.com/developer/article/1043931
https://blog.csdn.net/yulaoer53/article/details/116534813

https://juejin.cn/post/7118945309425664008

# 多域名配置
https://cloud.tencent.com/developer/article/2144511
```

## Nginx配置LUA

```shell
# 安装编译软件
yum install -y gcc-c++
yum install -y pcre pcre-devel
yum install -y openssl openssl-devel
yum install -y zlib zlib-devel

# 下载nginx软件
wget https://nginx.org/download/nginx-1.22.0.tar.gz


# LuaJIT-2.0.5.tar.gz方案废弃的替换解决方案
yum install -y git
git clone https://github.com/openresty/luajit2
cd luajit2
make && make install PREFIX=/usr/local/LuaJIT

# 以下为废弃方案
# wget http://luajit.org/download/LuaJIT-2.0.5.tar.gz
# LuaJIT-2.0.5.tar.gz不建议使用，会报错nginx: [alert] detected a LuaJIT version which is not OpenResty’s; many optimizations will be disabled and performance will be compromised
# 解压软件
# tar -zxf LuaJIT-2.0.5.tar.gz
# cd LuaJIT-2.0.5
# make && make install PREFIX=/usr/local/LuaJIT
# 卸载原有的安装  make uninstall



# 规避错误/usr/local/nginx/sbin/nginx: error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory

# 方案一
ln -s /usr/local/LuaJIT/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2
# 方案二
echo "/usr/local/LuaJIT/lib" > /etc/ld.so.conf.d/usr_local_lib.conf
# 方案三
cp /usr/local/LuaJIT/lib/libluajit-5.1.so.2 /usr/local/lib/
echo "/usr/local/lib"  >>/etc/ld.so.conf
# 刷新配置
ldconfig

vim /etc/profile
# 最后添加两行配置
export LUAJIT_LIB=/usr/local/LuaJIT/lib
export LUAJIT_INC=/usr/local/LuaJIT/include/luajit-2.0
# 重新加载配置文件
source /etc/profile
# 验证配置
lua -v

# 添加编译模块
cd /usr/local/
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
# 使用此版本 lua-nginx-module-0.10.14,-------------0.10.22版本会有问题
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.14.tar.gz
tar -zxf v0.3.0.tar.gz 
tar -zxf v0.10.14.tar.gz


cd /home/software
tar -zxf nginx-1.22.0.tar.gz
cd nginx-1.22.0
# 编译软件    安装软件   增加模块重新编译后make后替换编译产物nginx即可，make install 会重装
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_flv_module --with-http_stub_status_module --with-http_gzip_static_module --with-http_realip_module --with-pcre --add-module=/usr/local/lua-nginx-module-0.10.14 --add-module=/usr/local/ngx_devel_kit-0.3.0 --with-stream


make && make install



# 配置环境变量
vim /etc/profile
export PATH=/usr/local/nginx/sbin:$PATH
# 重新加载环境变量
source /etc/profile

# 查看Nginx配置是否正确
nginx -t

nginx -t
# 错误参考以上解决方案
# nginx: error while loading shared libraries: libluajit-5.1.so.2: cannot open shared object file: No such file or directory

# 启动Nginx
nginx

# 验证LUA，nginx http server里添加配置
location /hello_lua {
    default_type 'text/html';
    content_by_lua 'ngx.say("hello, lua!")';
}



```



## Nginx配置实战

```nginx

#user  nobody;
worker_processes  4;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
    
    # 隐藏版本号
    server_tokens off;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    server {
        listen       80;
        server_name  suiyueran.top;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        # 将请求转成https
        rewrite ^(.*)$ https://$host$1 permanent;


        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        #  error_page   500 502 503 504  /50x.html;
        #  location = /50x.html {
        #      root   html;
        #  }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    underscores_in_headers on;
    # HTTPS server
    #
    server {
        listen       443 ssl;
        server_name  suiyueran.top;
        ssl_certificate      /usr/local/nginx/conf/suiyueran.top.pem;
        ssl_certificate_key  /usr/local/nginx/conf/suiyueran.top.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
	      location /api/ {
            proxy_pass http://127.0.0.1:8081/;
	        proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        
        location /book/ {
            proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;


            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods 'POST, GET, OPTIONS, PUT, DELETE,UPDATE';
            add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization,jwt_token';
            

            if ($request_method = 'OPTIONS') {
	 		          return 204;
	 	        }

            
            proxy_pass http://127.0.0.1:8082/;
        }




        location /front/ {
		    proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            if ($http_env = "env_8081"){
                proxy_pass http://node_v1/$1/$2;
				        break;
            }
            if ($http_env = "env_8082"){
                proxy_pass http://node_v2;
                break;
            }
        }


        location ~/header/(.*) {
            proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            if ($http_env = "env_8081"){
                proxy_pass http://node_v1/$1;
                break;
            }
            if ($http_env = "env_8082"){
                proxy_pass http://node_v2/$1;
                break;
            }
        } 
     }

	   upstream node_v1  {
         server 127.0.0.1:8081;
     }
 
     upstream node_v2  {
         server 127.0.0.1:8082;
     }  
}



```

## Nginx支持配置多域名

> 两个域名的备案信息展示：

```html
<a href="https://beian.miit.gov.cn/" target="_blank">皖ICP备18026052号-1</a>
<a href="https://beian.miit.gov.cn/" target="_blank">皖ICP备18026052号-2</a>
```

> 配置修改

```nginx

#user  nobody;
worker_processes  4;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #access_log  logs/access.log  main;
    
    # 隐藏版本号
    server_tokens off;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    server {
        listen       80;
        server_name  suiyueran.top;

        location / {
            root   html;
            index  index.html index.htm;
        }

        # 将请求转成https
        rewrite ^(.*)$ https://$host$1 permanent;
    }
    # 支持有下划线的header参数
    underscores_in_headers on;
    
    server {
        listen       443 ssl;
        server_name  suiyueran.top;
        ssl_certificate      /usr/local/nginx/conf/suiyueran.top.pem;
        ssl_certificate_key  /usr/local/nginx/conf/suiyueran.top.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
	      location /api/ {
            proxy_pass http://127.0.0.1:8081/;
	        proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
  
        location /book/ {
            proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Methods 'POST, GET, OPTIONS, PUT, DELETE,UPDATE';
            add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization,jwt_token';
            
            if ($request_method = 'OPTIONS') {
	 		          return 204;
	 	        }
            proxy_pass http://127.0.0.1:8082/;
        }

        location /front/ {
		        proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            if ($http_env = "env_8081"){
                proxy_pass http://node_v1/$1/$2;
				        break;
            }
            if ($http_env = "env_8082"){
                proxy_pass http://node_v2;
                break;
            }
        }

        location ~/header/(.*) {
            proxy_set_header Host $proxy_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            if ($http_env = "env_8081"){
                proxy_pass http://node_v1/$1;
                break;
            }
            if ($http_env = "env_8082"){
                proxy_pass http://node_v2/$1;
                break;
            }
        } 
     }

	   upstream node_v1  {
         server 127.0.0.1:8081;
     }
 
     upstream node_v2  {
         server 127.0.0.1:8082;
     }  
     # /usr/local下创建freewheeling_conf，进目录创建freewheeling.conf文件
     # include后面必须跟绝对路径，相对路径不起作用
     include /usr/local/freewheeling_conf/*.conf;
}


    
    
    # freewheeling.conf内容，将之前的html目录及页面复制一份命名成freewheeling
    # cp rf html ./freewheeling
    
    server {
        listen       80;
        server_name  freewheeling.top;

        location / {
            # 创建freewheeling存放页面
            root   freewheeling;
            index  index.html index.htm;
        }

        # 将请求转成https
        rewrite ^(.*)$ https://$host$1 permanent;
    }  
    
    
    server {
        listen       443 ssl;
        server_name  freewheeling.top;
        ssl_certificate      /usr/local/nginx/freewheeling_conf/freewheeling.top.pem;
        ssl_certificate_key  /usr/local/nginx/freewheeling_conf/freewheeling.top.key;
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        location / {
            root   freewheeling;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   freewheeling;
        } 
     }
    


```







# 前后端泳道环境构建





# MySQL5.7.24

```shell
#卸载系统自带的Mariadb
rpm -qa|grep mariadb
#显示以下内容
mariadb-libs-5.5.56-2.el7.x86_64
#卸载系统自带的数据库：mariadb-libs-5.5.56-2.el7.x86_64(此处自带的系统为系统上查出来的Mariadb)
rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
#确认是否卸载成功，以下命令没有任何返回值即表示成功
rpm -qa|grep mariadb
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
#设置MySQL用户密码（密码不少于8个字符，密码要记住，此处密码设置为zouyaowen）
passwd mysql
更改用户 mysql 的密码。
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
#上传mysql安装包mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
cd /home/software
#解压安装包
tar -xzf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
#在/usr/local下创建mysql目录
mkdir /usr/local/mysql
#复制mysql安装包到/usr/local/mysql/
cp -r /home/software/mysql-5.7.24-linux-glibc2.12-x86_64/* /usr/local/mysql
#在mysql安装目录中创建文件夹data和auditlogs
cd /usr/local/mysql
mkdir data
#更改MySQL所属用户及用户组
cd /usr/local/
chown -R mysql:mysql ./mysql/
#初始化数据
cd /usr/local/mysql
#执行命令
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
#此处可能报错：bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
#解决方案
yum install -y libaio-devel.x86_64
#命令执行结果最后一行有一个临时密码要记住
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
#数据库表名大小写敏感0，不敏感1
lower_case_table_names=1
#限制server接受的数据包大小
max_allowed_packet=16M
sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
# 计划事件开启:
event_scheduler = on

#修改脚本权限
chmod 644 /etc/my.cnf

#创建启动脚本
vi /usr/lib/systemd/system/mysql.service
#添加一下内容
[Unit]
Description=MySQL Server
Documentation=man:mysqld(5.7.24)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
ExecStart=/usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000


#添加mysql命令链接，在任何地方都能直接使用mysql命令
ln -s /usr/local/mysql/bin/mysql /usr/bin
#添加mysqldump命令软连接在备份数据库使用
ln -s /usr/local/mysql/bin/mysqldump /usr/bin
#配置环境变量
vi /etc/profile
#修改PATH,在后面添加:/usr/local/mysql/bin:/usr/local/mysql/lib
export PATH=$PATH:$JAVA_HOME/bin:/usr/local/mysql/bin:/usr/local/mysql/lib
#重新加载环境变量
source /etc/profile

#启动MySQL
systemctl start mysql

#进入MySQL，使用临时密码
mysql  -uroot -p
Enter password: 
#系统提示必须先修改密码，现场开通密码不能设置的过于简单,root的密码设置为123456
mysql>set password=password('123456');
mysql>GRANT ALL PRIVILEGES ON *.* TO 'zouyaowen'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
#刷新设置
mysql>flush privileges;
#zouyaowen账号所有的IP都能访问数据库，root只能本机访问
#退出mysql
mysql>exit

#设置MySQL开机启动
systemctl enable mysql
#停止MySQL
systemctl stop mysql
#安装工具包
yum -y install net-tools
#查看监听的端口
netstat -ntpl
systemctl stop mysql
netstat -ntpl

firewall-cmd --zone=public --add-port=3366/tcp --permanent
#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示3366/tcp表示成功
firewall-cmd --list-ports

#select @@sql_mode
ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION

#解决方案、
set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

# MYSQL8.0.13

> 软件复制到远程命令
>
> scp mysql-8.0.25-linux-glibc2.12-x86_64.tar root@1.15.101.238:/tmp 
>
> 软件复制到本地命令
>
> scp -r root@42.192.150.54:/usr/local/nginx/conf/nginx.conf  ~/

```shell
#mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz安装包安装
#解压命令
xz -d mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz
tar -xf mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz
#解压后文件
mysql-8.0.15-linux-glibc2.12-x86_64

#卸载系统自带的Mariadb
rpm -qa|grep mariadb
#显示以下内容（CentOS7.6版本）
mariadb-libs-5.5.60-1.el7_5.x86_64
#卸载系统自带的数据库：mariadb-libs-5.5.60-1.el7_5.x86_64(此处自带的系统为系统上查出来的Mariadb)
rpm -e --nodeps mariadb-libs-5.5.60-1.el7_5.x86_64
#确认是否卸载成功，以下命令没有任何返回值即表示成功
rpm -qa|grep mariadb
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
#设置MySQL用户密码（密码不少于8个字符，密码要记住，此处密码设置为zouyaowen）
passwd mysql
更改用户 mysql 的密码。
新的 密码：
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。


#在/usr/local下创建mysql目录
mkdir /usr/local/mysql
#复制mysql安装包到/usr/local/mysql/
cp -r /home/software/mysql-8.0.15-linux-glibc2.12-x86_64/* /usr/local/mysql
#在mysql安装目录中创建文件夹data和auditlogs
cd /usr/local/mysql
mkdir data
#更改MySQL所属用户及用户组
cd /usr/local/
chown -R mysql:mysql ./mysql/
#初始化数据
cd /usr/local/mysql
#执行命令
bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
#命令执行结果最后一行有一个临时密码要记住

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
skip-name-resolve

#无操作超时退出：默认8个小时8*3600=28800秒
interactive_timeout=288000
#设置3366端口
port = 3366 
# 设置mysql的安装目录
basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放目录
#datadir=/usr/local/mysql/data
# 允许最大连接数
max_connections=200
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
#数据库表名大小写敏感0，不敏感1
lower_case_table_names=0
#限制server接受的数据包大小
max_allowed_packet=16M
# 计划事件开启:
event_scheduler = on

#修改脚本权限
chmod 644 /etc/my.cnf

#创建启动脚本
vi /usr/lib/systemd/system/mysql.service
#添加一下内容
[Unit]
Description=MySQL Server
Documentation=man:mysqld(5.8.15)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
ExecStart=/usr/local/mysql/bin/mysqld --defaults-file=/etc/my.cnf
LimitNOFILE = 5000


#添加mysql命令链接，在任何地方都能直接使用mysql命令
ln -s /usr/local/mysql/bin/mysql /usr/bin
#添加mysqldump命令软连接在备份数据库使用
ln -s /usr/local/mysql/bin/mysqldump /usr/bin
#配置环境变量
vi /etc/profile
#修改PATH,在后面添加:/usr/local/mysql/bin:/usr/local/mysql/lib
export PATH=$PATH:$JAVA_HOME/bin:/usr/local/mysql/bin:/usr/local/mysql/lib
#重新加载环境变量
source /etc/profile

#启动mysql
systemctl start mysql
#报错Data Dictionary initialization failed、表名大小写敏感修改参数
#注释掉datadir、lower_case_table_names改为0再次启动
systemctl start mysql

#进入MySQL，使用临时密码
mysql  -uroot -p
Enter password: 
#系统提示必须先修改密码，现场开通密码不能设置的过于简单,root的密码设置为123456
mysql>set password=password('123456');


#以上命令失效
#使用以下命令
mysql>alter user user() identified by '123456';
mysql>show databases;
#use命令可以不加分号
mysql>use mysql
mysql>CREATE USER zouyaowen IDENTIFIED BY '123456';
mysql>GRANT ALL PRIVILEGES ON *.* TO 'zouyaowen'@'%';
mysql>FLUSH PRIVILEGES;
mysql>exit

mysql>#以下命令不起作用：GRANT ALL PRIVILEGES ON *.* TO 'zouyaowen'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;

#设置MySQL开机启动
systemctl enable mysql
#停止MySQL
systemctl stop mysql
#安装工具包
yum -y install net-tools
#查看监听的端口
netstat -ntpl
systemctl stop mysql
netstat -ntpl

firewall-cmd --zone=public --add-port=3366/tcp --permanent
#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示3366/tcp表示成功
firewall-cmd --list-ports
```

## MySQL精进

> **可重复读**：两个事务进行数据操作他们是互不干扰的，事务先A进行数据查询，事务B进行一次事务修改并进行数据**提交**，事务A再进行一次查询，数据是不改变的。 **读已提交**：两个事务进行数据操作，事务先A进行数据查询，事务B进行一次事务修改并进行数据**提交**，事务A再进行一次查询，数据是B修改后的数据。
>
> 可重复读，事务中的一致性读取读取的是事务第一次读取所建立的快照。

```shell
https://serverfault.com/questions/395993/limit-number-of-connections-to-a-mysql-database

# 限制单个用户连接
grant usage on *.* to user1234@'%' with max_user_connections 10;




```





# Redis5.0.5安装

```shell
cd /home/software
#解压文件
tar -xzf redis-5.0.5.tar.gz
#复制安装包至/usr/local文件夹下
cp -r /home/software/redis-5.0.5 /usr/local/redis
#安装gcc
yum install -y gcc
#安装tcl，测试使用
yum install -y tcl
#进入安装目录
cd /usr/local/redis/
#编译
make MALLOC=libc
#安装
cd src && make install
#测试，这一步可以略过,如果测试最终显示：All tests passed without errors!  表示成功
#测试需要46步，第45步和第46步容易可能会出现差错，直接ctrl+C就行，不用管
#make test
#启动redis
cd /usr/local/redis/
./src/redis-server
#启动成功后Ctrl+C退出

cd ..

#修改redis配置文件
vi redis.conf
#修改daemonize no为daemonize yes
daemonize yes
#修改notify-keyspace-events ""为notify-keyspace-events "Ex"，修改完毕退出
notify-keyspace-events "Ex"
#修改端口6379为8679
port 8679
#允许远程连接，注释掉bind 127.0.0.1
#bind 127.0.0.1
#配置密码，放开requirepass foobared,设置密码为123456
requirepass 123456
#在配置文件目录创建文件夹redis
mkdir /etc/redis
#复制配置文件到/etc/redis中
cp /usr/local/redis/redis.conf /etc/redis/8679.conf

#编写redis启动脚本
vi /usr/lib/systemd/system/redis.service
#以下是启动脚本内容
[Unit]
Description=redis scripts 
After=network.target remote-fs.target nss-lookup.target syslog.target

[Service]
Type=forking
ExecStart=/usr/local/redis/src/redis-server /etc/redis/8679.conf

[Install]
WantedBy=multi-user.target

#启动redis
systemctl start redis
#关闭redis
systemctl stop redis
#查看redis状态
systemctl status redis
#设置开启自启
systemctl enable redis
#取消开机自启
systemctl disable redis
#开放redis端口
firewall-cmd --zone=public --add-port=8679/tcp --permanent
#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示80/tcp表示成功
firewall-cmd --list-ports
```

# RabbitMQ3.7.14

> Elang在线安装

```shell
vi /etc/yum.repos.d/rabbitmq_erlang.repo
#以下是下载源内容
# In /etc/yum.repos.d/rabbitmq_erlang.repo
[rabbitmq_erlang]
name=rabbitmq_erlang
baseurl=https://packagecloud.io/rabbitmq/erlang/el/7/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/rabbitmq/erlang/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300

[rabbitmq_erlang-source]
name=rabbitmq_erlang-source
baseurl=https://packagecloud.io/rabbitmq/erlang/el/7/SRPMS
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/rabbitmq/erlang/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300

#下载安装erlang
yum install -y erlang

#安装上传的依赖包
cd /home/software
#安装RabbitMQ
yum install -y rabbitmq-server-3.7.14-1.el7.noarch.rpm

#查看rabbitMQ安装目录
rpm -ql rabbitmq-server

#设置RabbitMQ开机启动，rabbitMQ启动脚本安装已经生成
systemctl enable rabbitmq-server
systemctl start rabbitmq-server
#systemctl stop rabbitmq-server
systemctl status rabbitmq-server
#启用插件
rabbitmq-plugins enable rabbitmq_management

#查看rabbitMQ的状态
rabbitmqctl status
#查看软件用户列表
rabbitmqctl list_users
#关闭防火墙访问端口:15672,guest只能本机访问
#添加用户并配置权限,第一个zouyaowen是用户名，123456是密码
rabbitmqctl add_user zouyaowen 123456
rabbitmqctl set_user_tags zouyaowen administrator
rabbitmqctl  set_permissions  -p  '/' zouyaowen '.' '.' '.'

#开启15672端口public(作用域)、15672/tcp(端口和访问类型)、permanent(永久生效)
firewall-cmd --zone=public --add-port=15672/tcp --permanent
#开启80端口public(作用域)、5672/tcp(端口和访问类型)、permanent(永久生效)
firewall-cmd --zone=public --add-port=5672/tcp --permanent

#重启防火墙
firewall-cmd --reload
#查看端口是否开启成功，显示15672/tcp、5672/tcp表示成功
firewall-cmd --list-ports

#rabbitMQ默认配置文件位置
vim /usr/lib/rabbitmq/bin/rabbitmq-env
```

# Kafka离线安装

# Zookeeper离线安装

```shell
#zookeeper软件安装版本3.4.14
tar -zxf zookeeper-3.4.14.tar.gz
mv zookeeper-3.4.14 /usr/local/zookeeper

#配置zookeeper环境变量
vi /etc/profile
#
export JAVA_HOME=/usr/local/java
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

#加载配置文件
source /etc/profile
#修改配置文件
cd /usr/local/zookeeper/
cd /conf
ls
configuration.xsl  log4j.properties  zoo_sample.cfg
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg
dataDir=/tmp/zookeeper修改为以下几行

#注释掉下一行，添加两行
#dataDir=/tmp/zookeeper
dataDir=/var/local/zookeeper/dataDir
dataLogDir=/var/local/zookeeper/dataLogDir
#以上两行为数据目录和日志目录配置

#创建数据和日志目录
cd ..
mkdir dataDir
mkdir dataLogDir


#编写zookeeper启动脚本
vi /usr/lib/systemd/system/zookeeper.service
#以下是启动脚本内容
[Unit]
Description=zookeeper service 
After=network.target remote-fs.target nss-lookup.target syslog.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/local/java
ExecStart=/usr/local/zookeeper/bin/zkServer.sh start
ExecStop=/usr/local/zookeeper/bin/zkServer.sh stop
ExecReload=/usr/local/zookeeper/bin/zkServer.sh restart
[Install]
WantedBy=multi-user.target


#启动zookeeper
systemctl start zookeeper
#查看zookeeper状态
systemctl status zookeeper
#关闭zookeeper
systemctl stop zookeeper
#重启zookeeper
systemctl restart zookeeper


#连接zookeeper
zkCli.sh
#查看命令
help
ZooKeeper -server host:port cmd args
        stat path [watch]
        set path data [version]
        ls path [watch]
        delquota [-n|-b] path
        ls2 path [watch]
        setAcl path acl
        setquota -n|-b val path
        history 
        redo cmdno
        printwatches on|off
        delete path [version]
        sync path
        listquota path
        rmr path
        get path [watch]
        create [-s] [-e] path data acl
        addauth scheme auth
        quit 
        getAcl path
        close 
        connect host:port
        
#查看节点数据:ls后面必须添加路径参数,默认有一个跟节点zookeeper
ls /
[zookeeper]
ls /zookeeepr/quota
[]
#默认的目录是：/zookeeepr/quota,且内部没有数据
#quit或者ctrl+C退出客户端

#ls与ls2命令，ls命令是查看节点，stat是查看路径信息，ls2是查看节点与节点对应的信息
#ls2相当于ls与stat命令的整合
stat /
#get命令是获取节点数据,ls只能查看节点，不能获取数据
get /

cZxid = 0x0                            #节点唯一标识
ctime = Thu Jan 01 08:00:00 CST 1970   #创建时间
mZxid = 0x0                            #修改后的唯一标识
mtime = Thu Jan 01 08:00:00 CST 1970   #修改时间，未修改创建时间与修改时间一致
pZxid = 0x0                            #子节点唯一标识
cversion = -1                          #子节点版本
dataVersion = 0                        #当前节点数据版本号，修改后会加一
aclVersion = 0                         #权限版本号，修改后会变化
ephemeralOwner = 0x0                   #判断是否为临时节点
dataLength = 0                         #数据长度
numChildren = 1                        #子节点数量

#添加节点及节点数据
create /zou zou-data
#获取节点数据及节点信息
get /zou
#创建临时节点
create -e /zou/tem zou-data
#创建顺序节点,产生的节点有序号标识
create -s /zou/seq seq
#修改数据
set /zou zou-data-new
#set命令set path data [version]中的版本号就是上一次获取数据的版本号，如果修改数据是数据已经被修改则修改失败——乐观锁
#删除命令delete path [version]:版本号与修改命令一致，与最新的版本号不匹配删除失败
delete /zou/seq0000000002 1
#增加命令create,删除命令delete，修改命令set,查找命令ls、stat、get

#针对每一个节点都会有一个监督者，也可以理解为触发器
#zk中的watcher是一次性的，触发后立即销毁
#父节点、子节点增删改都会触发watcher

#get path [watch]设置watcher
#父节点增删改触发watcher
#子节点增删改触发watcher
```



# RocketMQ离线安装

```shell
yum -y install wget
#下载二进制安装包
wget http://mirror.bit.edu.cn/apache/rocketmq/4.4.0/rocketmq-all-4.4.0-bin-release.zip

unzip rocketmq-all-4.4.0-bin-release.zip
cd rocketmq-all-4.4.0-bin-release

#Start Name Server
nohup sh bin/mqnamesrv &
tail -f /root/logs/rocketmqlogs/namesrv.log
#显示The Name Server boot success...表示成功

#Start Broker
nohup sh bin/mqbroker -n localhost:9876 &
#启动报错，物理内存不足
vi ./bin/mqbroker.xml

<-Xms512m>
</-Xms512m>
<-Xmx1g>
</-Xmx1g>
<-XX:NewSize>256M</-XX:NewSize>
<-XX:MaxNewSize>512M</-XX:MaxNewSize>
<-XX:PermSize>128M</-XX:PermSize>
<-XX:MaxPermSize>128M</-XX:MaxPermSize>

#以上八行修改为
<-Xms256m>
</-Xms256m>
<-Xmx512m>
</-Xmx512m>
<-XX:NewSize>128M</-XX:NewSize>
<-XX:MaxNewSize>128M</-XX:MaxNewSize>
<-XX:PermSize>64M</-XX:PermSize>
<-XX:MaxPermSize>64M</-XX:MaxPermSize>

#修改启动脚本runserver.sh和runbroker.sh启动参数
#runserver.sh
JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
#以上修改为
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"

#runbroker.sh
JAVA_OPT="${JAVA_OPT} -server -Xms8g -Xmx8g -Xmn4g"
#修改为
JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m"

#继续执行启动命令Start Broker
nohup sh bin/mqbroker -n localhost:9876 &

#启动mq（broker）按官网配置会出现无法找到topic的错误，需要使用命令：
#nohup sh mqbroker -n localhost:9876 autoCreateTopicEnable=true &

#使用命令创建topic
./bin/mqadmin updateTopic -n localhost:9876 -t stock -c DefaultCluster
```

# Node环境配置

```shell
wget https://nodejs.org/dist/v18.16.0/node-v18.16.0-linux-x64.tar.xz
cp node-v18.16.0-linux-x64.tar.xz /usr/local
cd /usr/local
tar -xf node-v18.16.0-linux-x64.tar.xz
cd node-v18.16.0-linux-x64
./node -v




# 配置环境变量
vi /etc/profile
export PATH=/usr/local/node-v18.16.0-linux-x64/bin:$PATH
# 重新加载配置
source /etc/profile

```



# SSH超时配置

```shell
echo export TMOUT=1000000 >> /root/.bash_profile
cat /root/.bash_profile
source .bash_profile
cp /etc/ssh/sshd_config /etc/ssh/sshd_config_bak
echo ClientAliveInterval=60 >> /etc/ssh/sshd_config
service sshd restart
cat /etc/ssh/sshd_config
service sshd restart
exit
```

# Python环境搭建

```shell
wget https://www.python.org/ftp/python/3.9.9/Python-3.9.9.tgz
```

# Docker基础命令

```shell
# 获取所有的容器
docker ps -a
# 删除某个容器
docker container rm cc3f2ff51cab cd20b396a061
# 删除所有已经停止的容器
docker system prune
```





# Docker构建CentOS7环境

```shell
# 拉取centos7镜像
[root@localhost]$ docker pull centos:7
[root@localhost]$ docker images
# 启动镜像centos7，如果不指定 /bin/bash，容器运行后会自动停止
# docker run -d -i -t <Image ID> /bin/bash
[root@localhost]$ docker run -d -i -t eeb6ee3f44bd /bin/bash
[root@localhost]$ docker ps
# 进入容器 docker exec -it <CONTAINER ID> bash
[root@localhost]$ docker exec -it dc3e38180b39 bash
# ifconfig命令不存在，下载安装
[root@localhost]$ ifconfig
[root@localhost]$ yum install -y net-tools
# 验证百度是否ping的通，docker默认没落模式是桥接，外网不通，删除容器重启按照host模式启动
# docker kill 容器ID  -->命令:停掉容器
# docker rm 容器ID  -->命令:删除容器
# 使用--net:host选项构建主机网络模型容器，默认是桥接
# 重新启动容器
[root@localhost]$ docker run --net host --name centos7 -dit eeb6ee3f44bd /bin/bash
# ping百度如果不通检查宿主机防火墙是否阻止了 ICMP 协议，如果是，请打开防火墙的相关端口
# 下载文件一般使用wget,命令不存在使用yum install wget
[root@localhost]$ yum install wget

# 安装编译软件
yum install -y gcc-c++
yum install -y pcre pcre-devel
yum install -y openssl openssl-devel
yum install -y zlib zlib-devel

# 下载nginx软件
wget https://nginx.org/download/nginx-1.22.0.tar.gz

# 解压软件
tar -zxf nginx-1.22.0.tar.gz
cd nginx-1.22.0
# 编译软件
#./configure --prefix=/usr/local/nginx/
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
# 安装软件   增加模块重新编译后make后替换编译产物nginx即可，make install 会重装
make && make install

# 配置环境变量
vi /etc/profile
export PATH=/usr/local/nginx/sbin:$PATH
# 重新加载环境变量
source /etc/profile

# 查看Nginx配置是否正确
nginx -t

# 启动Nginx
nginx

# 验证nginx 
curl 127.0.0.1


# 安装lua整合nginx
yum install -y git
git clone https://github.com/openresty/luajit2
cd luajit2
make && make install PREFIX=/usr/local/LuaJIT


# 配置包引用规避libluajit-5.1.so.2找不到错误
echo "/usr/local/LuaJIT/lib" > /etc/ld.so.conf.d/usr_local_lib.conf
ldconfig


vim /etc/profile
# 最后添加两行配置
export LUAJIT_LIB=/usr/local/LuaJIT/lib
# 此处路径/usr/local/LuaJIT/include下检查下最新的版本号码，可能会是最新的版本，以实际版本为准
export LUAJIT_INC=/usr/local/LuaJIT/include/luajit-2.1
# 重新加载配置文件
source /etc/profile
# 验证配置 显示 Lua 5.1.4  Copyright (C) 1994-2008 Lua.org, PUC-Rio
lua -v

# 添加编译模块
cd /usr/local/
# 下载ngx_devel_kit，链接不通可以使用wget https://codeload.github.com/vision5/ngx_devel_kit/tar.gz/refs/tags/v0.3.0 -O v0.3.0.tar.gz
wget https://github.com/simpl/ngx_devel_kit/archive/v0.3.0.tar.gz
# 使用此版本lua-nginx-module-0.10.14,其他版本会有问题，下载网速链接也可以使用wget https://codeload.github.com/openresty/lua-nginx-module/tar.gz/refs/tags/v0.10.14 -O v0.10.14.tar.gz
wget https://github.com/openresty/lua-nginx-module/archive/v0.10.14.tar.gz

tar -zxf v0.3.0.tar.gz 
tar -zxf v0.10.14.tar.gz

# 停止nginx
nginx -s stop
# 删除之前安装的nginx在/usr/local目录下
rm -rf nginx

# 回到nginx软件包下重新编译安装
cd /home/nginx-1.22.0

# 构建Makefile
./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-http_flv_module --with-http_stub_status_module --with-http_gzip_static_module --with-http_realip_module --with-pcre --add-module=/usr/local/lua-nginx-module-0.10.14 --add-module=/usr/local/ngx_devel_kit-0.3.0 --with-stream
# 重新编译并安装nginx
make & make install
# 查看Nginx配置是否正确
nginx -t

# 启动Nginx
nginx

# 验证LUA，nginx http server里添加配置
cd /usr/local/nginx/conf
vi nginx.conf

    location /hello_lua {
        default_type 'text/html';
        content_by_lua 'ngx.say("hello, lua!")';
    }
# nginx重新加载配置
nginx -s reload
# 访问验证lua是否配置成功
curl http://127.0.0.1/hello_lua









```


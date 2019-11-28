## 获取本地公钥

```shell
ssh-keygen -t rsa -b 4096 -C "1227701903@qq.com"
$ cd ~/.ssh

zyw@zyw-pc MINGW64 ~/.ssh
$ ls
id_rsa  id_rsa.pub  known_hosts

zyw@zyw-pc MINGW64 ~/.ssh
$cat id_rsa.pub

#讲以上内容粘贴到github官网中的setting中的ssh key中
```

## Nginx部署vue项目

```shell
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /opt/dist;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

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

#以上配置文件修改location中的root（vue打包后的文件）即可
```

## 负载均衡配置

```shell
#动态请求转发
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /opt/dist;
        index  index.html index.htm;
    }
    
    location /api/v2 {
        proxy_set_header Host 192.168.0.100;
        #proxy_pass以斜杠结尾匹配的的URL会整体替换，后面的拼接，不包含location
        proxy_pass http://192.168.0.100:81/new/;
    }
    
    location /api/v1 {
        proxy_set_header Host 192.168.0.100;
        #proxy_pass不以斜杠结尾（且最后为端口）会由proxy_pass拼接匹配到的URL之后的路径，包含location
        proxy_pass http://192.168.0.100:81;
        #proxy_pass不以斜杠结尾（最后不为端口）proxy_pass拼接匹配到的URL之后的路径，不包含location
        #proxy_pass http://192.168.0.100:81/new;
        #以上事例不拼接匹配到的URL
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

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



#配置负载均衡


upstream tomcat {  
    server 192.168.0.100:81;
    server 192.168.0.100:82;
}



server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /opt/dist;
        index  index.html index.htm;
    }
    
    location /api/v1 {
        proxy_set_header Host tomcat;
        #proxy_pass以斜杠结尾匹配的的URL会整体替换，后面的拼接，不包含location
        proxy_pass http://tomcat/new/;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

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


#启动SpringBoot
#nohup java -jar mark.jar --server.port=82 &
```


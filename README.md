# Using-Docker-to-Deploy-Web-Environment-nginx-PHP7.4
Linux系统使用 docker 来部署web环境 nginx+php7.4 并配置称 docker-compose-nginx.yml 文件
Docker是一个开源的容器化平台，旨在简化应用程序的创建、部署和管理。它基于OS-level虚拟化技术，通过将应用程序和其依赖项打包到一个称为容器的标准化单元中，使得应用程序可以在任何环境中快速、可靠地运行。

#### Docker的优势有以下几个方面：
轻量级和快速：Docker容器与主机共享操作系统内核，因此比传统虚拟化技术更轻量级且启动更快。

可移植性：Docker容器可以在任何支持Docker的主机上运行，无论是物理机、虚拟机还是云服务提供商。

灵活性：Docker容器可以隔离不同的应用程序及其依赖项，因此可以同时运行多个应用程序，而不会相互干扰。

所以 对于服务器部署应用 都使用 docker 来完成部署。下面 我分享自己制作的docker 镜像 和docker-compose-mysql.yml 文件 以后给服务器部署 可以直接使用 方便快捷 哈哈！

这个分享的镜像 里面集成了 nginx 和 php7.4 因为我的想法是 让nginx 和php 在一个镜像里运行 到时候把 前端代码 和php 代码放在一起

#### Dockerfile文件
![image](https://github.com/user-attachments/assets/56be8b3f-4086-4396-acfe-82ef0e3abb8d)
#### docker-compose-mysql.yml 文件
![image](https://github.com/user-attachments/assets/6b34a340-e384-4f00-9db6-be10e15ec99c)
如果你也需要 这样的文件 大家可以按需进行修改

文件获取地址：https://wwwoop.com/home/Index/projectInfo?goodsId=8&typeParam=3

如果myql 连上不 容器里 执行这个命令 mysql 依赖 没装
```
docker-php-ext-install pdo pdo_mysql
```
nginx 配置文件 如果 css js 加载不出来 可参考 下面配置

 nginx.conf
```
user  www-data;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
     include /etc/nginx/mime.types;
 
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}
```
某个网站的配置 例子：
 test.conf
```
server {
    listen 18000;
    server_name 192.168.31.133;
    root /home/wwwroot/test/public;

    location / {
        index index.html index.htm index.php;
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    error_log /etc/nginx/logs/test_error.log;
    access_log /etc/nginx/logs/test_access.log;
}
```

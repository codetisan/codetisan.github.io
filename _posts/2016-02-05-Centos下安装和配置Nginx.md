---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories:
  - nginx
tags:
  - nginx
title: Centos下安装和配置Nginx
---

### 准备工作
  
  1.  安装make：
    	yum -y install gcc automake autoconf libtool make
  2. 安装g++:
    	yum install gcc gcc-c++
  3. 安装pcre，zlib库：
    	yum install pcre pcre-devel zlib zlib-devel
  4. 安装ssl:
    	yum -y install openssl openssl-devel
  5. 下载Nginx：
  		wget http://nginx.org/download/nginx-1.8.1.tar.gz
  
### 安装Nginx
  
  1. 解压：
		tar zxvf nginx-1.8.1.tar.gz
  2. 进入nginx目录：
		cd nginx-1.8.1
  3. 配置：
~~~~
	  	./configure  \
	    --prefix=/etc/nginx \
	    --sbin-path=/usr/sbin/nginx \
	    --conf-path=/etc/nginx/nginx.conf \
	    --error-log-path=/var/log/nginx/error.log \
	    --http-log-path=/var/log/nginx/access.log \
	    --pid-path=/var/run/nginx.pid \
	    --lock-path=/var/run/nginx.lock \
	    --http-client-body-temp-path=/var/cache/nginx/client_temp \
	    --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
	    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
	    --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
	    --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
	    --user=nginx \
	    --group=nginx \
	    --with-http_ssl_module \
	    --with-http_realip_module \
	    --with-http_addition_module \
	    --with-http_sub_module \
	    --with-http_dav_module \
	    --with-http_flv_module \
	    --with-http_mp4_module \
	    --with-http_gunzip_module \
	    --with-http_gzip_static_module \
	    --with-http_random_index_module \
	    --with-http_secure_link_module \
	    --with-http_stub_status_module \
	    --with-http_auth_request_module \
	    --with-mail \
	    --with-mail_ssl_module \
	    --with-file-aio \
	    --with-http_spdy_module \
	    --with-ipv6
~~~~
  4. 编译:
		make && make install
  6. 创建相关子目录:/var/cache/nginx及其子目录
  7. 添加nginx的组和用户
	    groupadd nginx
	    useradd -g nginx nginx
  8. 确保nginx默认的80端口没有被占用：
    	netstat -ano|grep 80
    
  9. 启动nginx
    	/usr/sbin/nginx
  10. 打开浏览器，验证是否启动成功
    	看到"Welcome to nginx!" 证明nginx已经启动成功
  11. 设置开机启动
    	echo "/usr/sbin/nginx" >> /etc/rc.local
  12. nginx重启
  	nginx -s reload


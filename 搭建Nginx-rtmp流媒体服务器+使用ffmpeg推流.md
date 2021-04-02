# 搭建Nginx-rtmp流媒体服务器+使用ffmpeg推流

## 1 安装ffmpeg

### 1.1 下载安装包

ffmpeg --  https://www.ffmpeg.org/download.html

libx264 -- http://www.videolan.org/developers/x264.html

nasm -- https://www.nasm.us/

yasm -- https://yasm.tortall.net/Download.html

### 1.2 安装

```shell
cd yasm-1.3.0/
 ./configure 
 make && make install
 
cd nasm-2.13.03/
 ./configure 
 make && make install
 
cd x264/ 
./configure --enable-shared --enable-pthread --enable-pic
make && make install
 
cd ffmpeg-4.1
./configure --enable-libx264 --enable-gpl --enable-shared  --extra-cflags=-I/usr/local/include --extra-ldflags=-L/usr/local/lib --enable-pic
make && make install

ffmpeg
显示版本信息安装成功
```

### 1.3 遇到的问题

```
[root@centos ffmpeg-4.1]# ffmpeg
ffmpeg: error while loading shared libraries: libavdevice.so.58: cannot open shared object file: No such file or directory
```

解决方案：

```shell
vim /etc/ld.so.conf
添加以下内容
/usr/local/ffmpeg/lib
保存退出

ldconfig

再次运行ffmpge解决问题
```



## 2 安装Nginx

### 2.1 安装依赖

```shell
yum -y install gcc pcre-devel openssl openssl-devel
```

下载nginx-rtmp-module

```shell
git clone https://github.com/arut/nginx-rtmp-module.git
```

### 2.2 下载Nginx

nginx官网下载：http://nginx.org/en/download.html

```
wget http://nginx.org/download/nginx-1.16.1.tar.gz
```

### 2.3 解压nginx压缩包

```shell
tar -zxf nginx-1.16.1.tar.gz
```

### 2.4 安装

```shell
cd nginx-1.16.1
./configure --prefix=/usr/local/nginx  --add-module=../nginx-rtmp-module  --with-http_ssl_module
make && make install
```

### 2.5 修改nginx配置文件

```
vim /usr/local/nginx/conf/nginx.conf
```

在nginx.conf文件中添加如下代码

```
rtmp {    

    server {    

        listen 1935;  #监听的端口  

        chunk_size 4000;    

        application hls {  #rtmp推流请求路径  
            live on;    
            hls on;    
            hls_path /usr/local/nginx/html/hls;    
            hls_fragment 5s;    
        }    
    }    
} 
```

说明：这个路径，看你自己的实际情况的访问根目录，我的是`/usr/local/nginx/html/` ,然而`/usr/local/nginx/html/hls`肯定没有这个目录，所以需要建一个放流文件的目录hls,并且需要改权限可读可写的权限



修改http中的server模块

```
server {  
        listen       81;  
        server_name  localhost;  

        #charset koi8-r;  

        #access_log  logs/host.access.log  main;  

        location / {  
            root   /usr/local/nginx/html;  
            index  index.html index.htm;  
        }  

        #error_page  404              /404.html;  

        # redirect server error pages to the static page /50x.html  
        #  
        error_page   500 502 503 504  /50x.html;  
        location = /50x.html {  
            root   html;  
}
```

### 2.6 编辑mime.types.default配置文件，添加如下配置

```
application/x-mpegURL  m3u8;
```

## 2.7 启动/重启Nginx

```
启动
/usr/local/nginx/sbin/nginx

重启
/usr/nginx/sbin/nginx -s reload

注意，修改了配置文件后最好先检查一下修改过的配置文件是否正 确，以免重启后Nginx出现错误影响服务器稳定运行。

判断Nginx配置是否正确命令如下：
nginx -t -c /usr/nginx/conf/nginx.conf
或者
/usr/nginx/sbin/nginx -t -c /usr/nginx/conf/nginx.conf

nginx  reload重启
```

## 2.8 开启防火墙81端口

```
firewall-cmd --permanent --zone=public --add-port=81/tcp
firewall-cmd --reload
```


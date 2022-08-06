## nginx 搭建一个可用的静态资源web服务器

# 准备工作

- `centos`安装好`nginx`。
- 准备一个静态资源。



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    this is a nginx assrts demo.
    <img src="./avatar.jpeg" alt="avatar">
</body>
</html>
```



## 上传本地静态资源到远程服务器

### 获取nginx静态资源位置

```shell
whereis nginx
nginx: /usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx /usr/share/man/man3/nginx.3pm.gz /usr/share/man/man8/nginx.8.gz
```

我们发现默认资源在这里`/usr/share/nginx`。

现在我们就准备把自己准备好的静态资源上传过来。

```shell
scp -P 36000 -r /Users/aedanxu/Documents/nginx-assets/ root@ip
```

我们已经上上传成功。

```shell
root@aedanxu:/usr/share/nginx
▶ ll
total 12K
drwxr-xr-x 2 root root 4.0K Aug  6 12:13 html
drwxr-xr-x 2 root root 4.0K Aug  6 12:13 modules
drwx------ 2 root root 4.0K Aug  6 14:51 nginx-assets
```



## 修改nginx配置

找到`nginx.conf`配置文件路径。

```shell
nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

修改配置。

```nginx
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;

	access_log /etc/nginx/logs/geek.access.log main;

        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
          # 主要修改这里
	    		alias /usr/share/nginx/nginx-assets/;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }
```

如果我们会遇到 `403  Permission denied`。多半是由于启动用户跟nginx工作用户不一致所致。

```nginx
user root;
```

这个时候重启nginx应该可以正常访问了。`nginx -s reload`。



![截屏2022-08-06 15.20.32](https://raw.githubusercontent.com/NuoHui/typora-imgs/main/202208061520309.png)



这个时候我们发现图片资源是47.8kb。这个是没有压缩的，我们都知道静态资源是可以开启nginx的压缩来减少带宽。

```nginx
    gzip on;
    gzip_min_length 1;
    gzip_types text/plain application/x-javascript text/css application/xml image/jpeg image
/png;
```

这个时候我们再看，会发现图片响应头中多了。

```text
Content-Encoding: gzip
```

![截屏2022-08-06 15.26.30](https://raw.githubusercontent.com/NuoHui/typora-imgs/main/202208061526050.png)



如果有的时候我们需要限制用户访问大文件，优先加载小文件给用户，我们可以使用这个指令。

```nginx
location / {
	    alias nginx-assets/;
	    autoindex on;
      # 1kb的响应速度
	    set $limit_rate 1k;
	    index index.html index.htm;
 }
```

重启后你会发现我们图片资源就会按照`1kb/s`的速度加载了。


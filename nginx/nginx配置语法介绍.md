# nginx 配置语法

## 语法介绍

1. 配置文件由指令与指令块组成。
2. 每条指令以`;` 分号结尾，指令与参数间以空格符合分割。
3. 指令块以`{}`大括号将多条指令组织在一起。
4. `include`语句允许组合多个配置文件以提升可维护性。
5. 使用`#`符合添加注释，提高可读性。
6. 使用`$`符合使用变量。
7. 部分指令的参数支持正则表达式。

我们可以看一段语法示例。

```nginx
  server {
        listen       9999;
        server_name  localhost;

        #charset koi8-r;

        access_log  logs/host.access.log  main;

        location ~ \.php$ {
           root assets;
           autoindex on;
           set $limit_rate 1k;
           #  root   html;
           # index  index.html index.htm;
        }
	}
```



## nginx中的时间单位

- ms: milliseconds
- d: days
- s: seconds
- w: weeks
- m: minutes
- M: months、30days
- h: Hours
- y: years、365days



```nginx
location ~* \.(gif|jpg|jpeg)$ {
	proxy_cache my_cache;
  expires 3m;proxy_cache_key $host$uri$is_arg$args;
  proxy_cache_valid: 200 304 302 1d;
  proxy_pass: https://test.com;
}
```



## nginx中的空间单位

- 默认不带后缀表示字节：bytes

- k/K：kilobytes 千字节
- m/M: megabytes M
- g/G: gigabytes G



## nginx命令行



- 格式: `nginx -s reload`
- 帮助：`nginx - h`
- 使用指定的配置文件： `-c`
- 指定配置指令：`-g`
- 指定运行目录：`-p`
- 操作进程发生信号：`-s`
  - 立即停止服务：`stop`
  - 优雅的停止服务：`quit`
  - 重载配置文件：`reload`
  - 重新开始记录日志文件：`reopen`

- 测试配置文件是否有语法错误：`-t` `-T`
- 打印nginx的版本信息：`-v` ` -V`



```shell
# 查看进程
ps -df | grep nginx
# 查看nginx 配置文件
nginx -t
whereis nginx
```




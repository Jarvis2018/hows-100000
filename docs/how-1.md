# "十万个"怎么做：nginx的access.log自动分割

## 是什么

1. `access.log`是`Nginx`的日志文件，主要记录了访问`Nginx`的记录，比如当我们访问一个网页的时候，`Nginx`
会将指定格式的访问记录写入到`access.log`文件中。
   
2. `access.log`对我们排查错误时，提供思路和依据，所以强烈不建议生产环境关闭`access.log`。

## 为什么

那为啥我们要分隔`access.log`呢？主要是当网站的访问量比较大或者长时间没有备份该文件时，会导致此文件非常大，
可能会导致服务器性能降低，比如网页打开的速度比较慢。

## 怎么做

### 1. 手动方式（可以不用停止`Nginx`）

```shell
#在access.log目录下执行
echo > access.log
```

### 2. 自动方式（推荐）
   
#### 2.1 新建脚本文件：`auto_cut_nginx_access.sh`，内容如下：

```shell
#!/bin/bash

log_path=xxx/access.log # 绝对路径
back_path=xxx/back/access_$(date +%Y%m%d -d 'yesterday').log # 绝对路径
cp $log_path $back_path && echo > $log_path
```

#### 2.2 新建`crontab`任务：

```shell
# 执行该命令
cronttab -e
# 插入如下内容(脚本也需要绝对路径)，每日凌晨1点执行：
0 1 * * * /bin/bash xxx/auto_cut_nginx_access_log.sh
```

## 总结

当我们写好脚本后，一定要先手动执行下脚本，看看有没有报错之类的问题。

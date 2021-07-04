---
title: Linux系列目录
date: 2020-06-17 15:27:34
tags:  cmd
---

## CMD命令总结

### 进程类

#### netstat 命令

##### 查找某一端口所占用的进程

```
netstat -ano|findstr "端口号"
```

>命令解析：
>
>- netstat 
>
>netstat 命令用于显示与IP、TCP、UDP和ICMP协议相关的统计数据，一般用于检验本机各端口的网络连接情况。netstat是在内核中访问网络及相关信息的程序，它能提供TCP连接，TCP和UDP监听，进程内存管理的相关报告。
>
>参数说明：
>
>- -a 或 -all 显示所有连线中的Socket。
>- -n 直接使用ip地址，不通过域名服务器
>- -o 显示计时器
>
>- -an 当不附加"-n"选项时，它显示的是本地计算机的netbios名字+端口号。而加了"-n"选项后，它显示的是本地IP地址+端口号
>
>故 netstat -ano 表示打印所有连接的IP地址+端口号及链接时间



#### tasklist

##### 根据PID查找对应的程序

```
tasklist |findstr "PID"
```

##### 结束某一进程

```
taskkill /pid xxxx /F
```




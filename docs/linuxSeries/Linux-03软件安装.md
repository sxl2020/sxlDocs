

## Linux 环境下安装软件笔记

在Ubuntu下，使用源码编译源码安装工具，可以实现安装指定版本的需求，且安装后清除系统环境等也很有帮助。但是，源码编译安装笔使用apt等工具安装要复杂多了。为了积累经验，将日常学习和工作中安装软件的实践记录下来，以备将来用之。

使用源码安装linux相关工具的基本步骤如下：

>1. 在官网下载源码压缩包，一般为.tar.gz格式。
>2. 解压缩。一般使用 tar -zxvf xxx.xxx.xxx.tar.gz来解压缩。
>3. 编译安装源码：
>   - .configure --prefix opt/xxx/xxx  使用configure脚本来检查编译环境，并使用-prefix来指定安装目录。
>   - make 编译源码
>   - make install 安装
>



### 1. 源码编译方式安装curl

使用源码安装curl,为了实现最新版本的安装。

###### 1.1.1 curl 常用参数

```
curl -L --retry 5 --retry-delay 3 "${URL}" | tar xz || rc=$?
```

###### 1.2.1 -L参数

使用 `-L` 参数，curl 就会跳转到新的网址：

```
$ curl -L www.sina.com
```

键入上面的命令，结果自动跳转为www. sina. com.

###### 1.2.2 --retry 

--retry <num> 指定重试次数

###### 1.2.3 --retry-delay

--retry-delay <seconds> 传输出现问题时，设置重试间隔时间



##### 1.5 curl的错误总结

在使用curl来进行网络通信的时候，常常会出现各种错误。一下是对这些错误的总结。

###### 1.2.1 curl: (35) OpenSSL SSL_connect

BUG详细信息如下：

```
sxl@ubuntu18:~/bkapp$ curl -sSL https://bit.ly/2ysbOFE | bash -s -- 2.0.1 1.4.6 0.4.18
curl: (35) OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to bit.ly:443 
```



###### 1.2.2 curl: (56) Recv failure

BUG详细信息如下：

```
sxl@ubuntu18:~/bkapp$ curl -sSL http://bit.ly/2ysbOFE | bash -s -- 1.4.6 1.4.6 0.4.18
curl: (56) Recv failure: 连接被对方重设
```



### 2. OpenSSL的源码安装

1. 下载源码

```
wget https://www.openssl.org/source/openssl-1.1.1d.tar.gz --no-check-certificate
```

2. 解压缩

```
tar zxvf openssl-1.1.1d.tar.gz
```

3. 配置 & 编译 &安装

```
## 1.进入解压文件，对程序进行配置
cd openssl-1.1.1d/
./config shared --prefix=/usr/local/	# shared 的意思是创建共享库。
## 2.编译安装
make
make install
## 3.copy相关文件至系统文件夹
sudo cp libcrypto.so.1.1 /usr/lib/x86_64-linux-gnu/
sudo cp libssl.so.1.1 /usr/lib/x86_64-linux-gnu/
## 4. 创建软连接
ln -s /usr/local/bin/openssl /usr/bin/openssl
openssl version
```


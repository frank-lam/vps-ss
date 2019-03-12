CentOS 7 系统配置ss server - HelloMegatron的博客 - CSDN博客
https://blog.csdn.net/HelloMegatron/article/details/82927299





# 搬瓦工 CentOS 7 系统配置ss server

本文主要记录作者本人在搬瓦工VPS上配置ss server的过程。

由于技术需求，常常需要访问谷歌相关网站，之前一直用XX-net，但后来由于某些原因，XX-net项目不太好用了，只好自己搭建通道。VPS默认系统为CentOS_6。首先在个人中心的控制面板中重装系统，安装系统为CentOS 7 x86_64 bbr。可以在控制面板中打开Terminal，也可以使用SSH远程登录VPS。作者在自己的MacOS的Terminal中，使用ssh命令操作。



## 前置环境

- Python
- zip



## Python 与 pip 安装

1. [官网下载 pip 18.0](https://pypi.org/project/pip/18.0/#files)

   这里提供 18.0 的安装版本

```
   wget https://files.pythonhosted.org/packages/69/81/52b68d0a4de760a2f1979b0931ba7889202f302072cc7a0d614211bc7579/pip-18.0.tar.gz
```

2. 移动到 `/usr/local` 目录，并进入目录解压

```shell
mv pip-18.0.tar.gz /usr/local/
cd /usr/local/

#解压到当前目录，保留原文件
tar -zxvf pip-18.0.tar.gz
```

3. 进入解压后的 pip-18.0 目录，使用 Python 安装

```
cd pip-18.0
python setup.py install
```

发现没安装setuptools。同样的方法安装setuptools。在`/usr/local`目录下使用`wget`命令下载setuptools-40.2.0.zip，例如：

```
wget https://files.pythonhosted.org/packages/ef/1d/201c13e353956a1c840f5d0fbf0461bd45bbd678ea4843ebf25924e8984c/setuptools-40.2.0.zip
```

```shell
yum install -y unzip zip
unzip setuptools-40.2.0.zip
cd setuptools-40.2.0
# 安装 setuptools
python setup.py install
cd ../pip-18.0
python setup.py install
```

使用 pip show pip 查看 pip，发现 pip 终于成功安装。



### 安装 Shadowsocks server

使用 pip 命令安装 ss server：

```shell
pip install shadowsocks
```

### 配置 Shodowsocks server

使用配置文件设置 ss server 可以方便后面修改。创建一个 json 文件 `/etc/shadowsocks.json`，配置如下:

```shell
{
    "server": "104.194.93.37",
    "server_port":4443,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"biodwhu",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

可以针对不同的用户配置不同的用户名和密码，方法如下:

```shell
{
    "server": "0.0.0.0",
    "port_password": {
        "8381": "foobar1",
        "8382": "foobar2",
        "8383": "foobar3",
        "8384": "foobar4"
    },
    "timeout": 300,
    "method": "aes-256-cfb"
}
```

在前台运行ss server配置文件:

```shell
ssserver -c /etc/shadowsocks.json
```

在后台运行ss server配置文件:

```shell
ssserver -c /etc/shadowsocks.json -d start
ssserver -c /etc/shadowsocks.json -d stop
```


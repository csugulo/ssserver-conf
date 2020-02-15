# V2ray-config

V2ray服务配置以及Debian/Ubuntu客户端配置，**请勿通过该服务发送垃圾邮件、爬取网页**。

- [V2ray-config](#v2ray-config)
  * [1. V2ray客户端配置文件](#1-v2ray-------)
  * [2. 安装配置proxychains/V2ray](#2-----proxychains-v2ray)
    + [2.1 安装](#21---)
    + [2.2 配置V2ray客户端配置文件](#22---v2ray-------)
    + [2.3 配置proxychains](#23---proxychains)
    + [2.5 测试](#25---)
  * [3. git配置sock5代理](#3-git--sock5--)
  * [4. go get配置sock5代理](#4-go-get--sock5--)


## 1. V2ray客户端配置文件

|实例机房|实例配置文件|
|:-:|:-:|
|洛杉矶|[aliyun-hk.json](https://raw.githubusercontent.com/csugulo/v2ray-config/master/config/aliyun-hk.json)|
|香港|[cloudcone-la.json](https://raw.githubusercontent.com/csugulo/v2ray-config/master/config/cloudcone-la.json)|

## 2. 安装配置proxychains/V2ray

### 2.1 安装

```
sudo apt update
sudo apt install git proxychains -y

wget https://install.direct/go.sh
sudo bash go.sh
```

### 2.2 配置V2ray客户端配置文件

将配置文件复制到/etc/v2ray/config.json，启动V2ray
```
sudo systemctl start v2ray
```

### 2.3 配置proxychains

修改proxychains配置文件/etc/proxychains.conf
```
# 1. 注释dns转发
# Proxy DNS requests - no leak for DNS data
#proxy_dns
# 2. 将ProxyList中的sock4修改为sock5并修改端口
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
socks5  127.0.0.1 1080
```

### 2.5 测试

```
proxychains curl https://api.ipify.org/?format=json
curl https://api.ipify.org/?format=json
```

## 3. git配置sock5代理

```
git config --global http.proxy 'socks5://127.0.0.1:1080'
# 取消代理
git config --global --unset http.proxy
```

## 4. go get配置sock5代理

添加bash脚本~/bin/go-get-proxy

```
#!/bin/bash
export https_proxy=socks5://127.0.0.1:1080
export http_proxy=socks5://127.0.0.1:1080
go get -u -v $@
unset https_proxy
unset http_proxy
```

为脚本增加执行权限

```
chmod +x ~/bin/go-get-proxy
```

将~/bin文件夹路径加入PATH环境变量

```
# 若默认shell为bash，则修改~/.bashrc
echo 'export PATH=$HOME/bin:/usr/local/bin:$PATH' | tee -a ~/.zshrc
source ~/.zshrc
```

测试

```
go-get-proxy golang.org/x/net golang.org/x/crypto
```



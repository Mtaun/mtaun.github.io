# Tor over vpn, Burp Suite and Firefox

## 零

有比这个方案更好的选择，就是使用tor+redsocks+iptables搭建一个透明代理，用一台主机作为网关，在该主机上搭建Tor服务并做好流量转发，实现所有以该主机为网关的机器均可以直接访问Tor，我个人推荐使用这个方案，可以参考这篇文章：https://hujiekang.top/2021/04/15/Tor-transparent-proxy/

## 一	简要介绍

在kali虚拟机下，先连接vpn到国外结点，然后通过vpn连接Tor，最后再用Burp Suite和Firefox进行抓包

## 二	准备工作

主机挂不挂vpn无所谓，使用vmware准备一个kali虚拟机，网络适配器选择NAT模式，其他的都常规安装就行，以及vpn

## 三	具体步骤

### 1	配置使用v2ray
1	准备文件

```
# v2ray
wget https://github.com/v2ray/v2ray-core/releases/download/v4.27.0/v2ray-linux-64.zip 

# Qv2ray
wget https://github.com/Qv2ray/Qv2ray/releases/download/v2.6.3/Qv2ray.v2.6.3.linux-x64.AppImage
```
2	解压

```
unzip v2ray-linux-64.zip
```
3	运行

```
chmod +x Qv2ray.v2.6.3.linux-x64.AppImage

./Qv2ray.v2.6.3.linux-x64.AppImage
```

4	运行后如下

[<img src="https://s3.ax1x.com/2021/02/21/yTutXQ.jpg" alt="yTutXQ.jpg" style="zoom: 80%;" />](https://imgchr.com/i/yTutXQ)

5	选择左上角preference -> General Settings，可以更改语言为中文

[<img src="https://s3.ax1x.com/2021/02/21/yTuUmj.jpg" alt="yTuUmj.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTuUmj)

6	然后选择内核设置，将 V2Ray核心可执行文件路径 和 V2Ray资源目录 修改为 v2ray-linux-64.zip解压后文件夹中的可执行文件路径 及 所在目录（图中已对文件夹名进行修改）

[<img src="https://s3.ax1x.com/2021/02/21/yTuY6g.jpg" alt="yTuY6g.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTuY6g)

7	选择入站设置，调整为图中所示，记住端口号，例如8889，可以根据需求修改连接设置中的选项，其余设置不变

[<img src="https://s3.ax1x.com/2021/02/21/yTKKvF.jpg" alt="yTKKvF.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTKKvF)

8	返回主页面，选择分组，使用Default Group 或 创建新分组，勾选此分组是一个订阅，填入订阅地址，点击OK

[<img src="https://s3.ax1x.com/2021/02/21/yTK0DH.jpg" alt="yTK0DH.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTK0DH)

9	返回主页面，右键分组，点击更新订阅，双击分组即可看到节点进行连接

### 2	安装并使用Tor

1	根据实际情况决定需不需要换源

2	执行命令安装tor

```bsh
apt-get update
apt-get dist-upgrade(可选)
apt-get install tor
```

3	使用vim，leafpad，mousepad等修改配置文件 /etc/tor/torrc

此处使用leafpad打开文件

```
sudo leafpad /etc/tor/torrc
```
在文件开头添加下句后保存（端口号是刚刚记下的）
```

HTTPSProxy 127.0.0.1:8889

```

根据[官方文档](https://2019.www.torproject.org/docs/tor-manual.html.en)的解释
```
HTTPSProxy host[:port]

Tor will make all its OR (SSL) connections through this host:port (or host:443 if port is not specified), via HTTP CONNECT rather than connecting directly to servers. You may want to set FascistFirewall to restrict the set of ports you might try to connect to, if your HTTPS proxy only allows connecting to certain ports.
```
同样的，如果使用tor browser经过一次代理后连接tor，再翻看torrc也有一样的语句

其中tor默认端口为9050，如果需要修改，则更改

```
SocksPort 9050
```

[![yT15lV.jpg](https://s3.ax1x.com/2021/02/21/yT15lV.jpg)](https://imgchr.com/i/yT15lV)

4	启动tor

如图所示达到100%即成功启动

[![yTGGLT.jpg](https://s3.ax1x.com/2021/02/21/yTGGLT.jpg)](https://imgchr.com/i/yTGGLT)

### 3	配置Burp Suite

只需要修改user options中的socks proxy为tor的host和port，其余设置不变

[<img src="https://s3.ax1x.com/2021/02/21/yTNKBV.jpg" alt="yTNKBV.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTNKBV)

### 4	配置Firefox

进行如图所示修改，其中HTTP Proxy的端口为Burp Suite中Proxy的默认端口8080，可以根据需要进行修改，导入Burp Suite证书不再赘述

[<img src="https://s3.ax1x.com/2021/02/21/yTNLD0.jpg" alt="yTNLD0.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTNLD0)

### 5	检查ip和抓包测试

1	访问whatismyipaddress.com，tor代理成功

[<img src="https://s3.ax1x.com/2021/02/21/yTUlrt.jpg" alt="yTUlrt.jpg" style="zoom:80%;" />](https://imgchr.com/i/yTUlrt)

2	使用Burp Suite抓包，成功

[![yTUXQA.jpg](https://s3.ax1x.com/2021/02/21/yTUXQA.jpg)](https://imgchr.com/i/yTUXQA)

## 四	匿名性

此处给出一个[链接](https://gitlab.torproject.org/legacy/trac/-/wikis/doc/TorPlusVPN)

## 五	正确性

暂不保证hhhhhhhhhh
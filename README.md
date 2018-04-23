# linux-learning-notes
Linux学习笔记
## s*r

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log解释
* 用途  
将运行时的log写到shadowsocks-all.log文件中，同时也输出到标准输出。
 
* 解释  
2表示标准输出。
文件描述符：0 stdin，1 stdout，2 stderr
2>&1，表示标准错误重定向到标准输出，如果没有2>&1，只会有标准输出，没有错误；tee的作用同时输出到控制台和文件。  
make 2>log.txt 表示只将错误写到文件，其它的还是在标准输出。

## kcptun

```
wget --no-check-certificate https://github.com/kuoruan/shell-scripts/raw/master/kcptun/kcptun.sh
chmod +x ./kcptun.sh
./kcptun.sh
```
强制kill掉某端口
```
lsof -i :9000 | grep LISTEN | awk '{print $2}' | xargs kill -9
```

## live555

```
wget  http://www.live555.com/liveMedia/public/live555-latest.tar.gz
tar xzf live555-latest.tar.gz
cd live
./genMakefiles linux-64bit    // 注意后面这个参数是根据当前文件夹下config.<后缀>获取得到的
make && make install          // 在usr/local/include出现四个文件夹的头文件、/usr/local/lib下出现链接库
```

## yum命令

```
yum -y install wget   // 安装wget
yum -y install git    // 安装git
yum -y list lsof      // 列出包含lsof的软件包
yum -y install lsof   // 安装lsof
```

## netstat

```
netstat -ntlp     // 查看服务器运行的进程服务和监听端口
```

## lsof

```
lsof -i :8989     // 查看8989端口现在运行的情况
```

## java

```
yum -y list java*
yum -y install java-1.8.0-openjdk.x86_64
```

## go

```
yum -y list golang
yum -y install golang.x86_64
```

## redis

```
yum install redis
systemctl start redis.service
systemctl enable redis.service
```

* 设置redis密码  
打开文件/etc/redis.conf，找到其中的#requirepass foobared，去掉前面的#，并把foobared改成你的密码。

* 测试redis连接是否成功
redis-cli -h 198.xxx.xxx.xxx -p 6379

## frp

```
# frpc.ini
[common]
server_addr = 198.181.46.166
server_port = 5443
privilege_token = tIMrKiL94XZeXhb3

[web]
privilege_mode = true
type = http
local_port = 4000
custom_domains = www.clang.online
```

## MySQL数据库远程访问权限如何打开
### 1、改表法
可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"

```
mysql -u root -p 
mysql>use mysql; 
mysql>update user set host = '%' where user = 'root'; 
mysql>select host, user from user;

``` 
### 2、授权法
在安装mysql的机器上运行：

```
// 这样应该可以进入MySQL服务器
mysql -h localhost -u root
```

```
// 赋予任何主机访问数据的权限
mysql>GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION
```

例如，你想myuser使用mypassword从任何主机连接到mysql服务器的话。

```
mysql>GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%'IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 
```

如果你想允许用户myuser从ip为192.168.1.6的主机连接到mysql服务器，并使用mypassword作为密码

```
mysql>GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3'IDENTIFIED BY 'mypassword' WITH GRANT OPTION;
```
 
```
// 修改生效
mysql>FLUSH PRIVILEGES
```

```
// 退出MySQL服务器，这样就可以在其它任何的主机上以root身份登录
mysql>EXIT
```

## 防火墙配置
### firewall

```
# service firewalld status   // 查看防火墙状态（disabled 表明 已经禁止开启启动 enable 表示开机自启，inactive 表示防火墙关闭状态 activated（running）表示为开启状态）
# service firewalld start;  或者 #systemctl start firewalld.service;#开启防火墙
# service firewalld stop;  或者 #systemctl stop firewalld.service;#关闭防火墙
# service firewalld restart;  或者 #systemctl restart firewalld.service;  #重启防火墙
# systemctl disable firewalld.service   // 禁止防火墙开启自启
# systemctl enable firewalld   // 设置防火墙开机启动
# yum remove firewalld   // #卸载firewall
```

## iptables

```
#yum install iptables-services   // 安装iptables防火墙
#vi /etc/sysconfig/iptables   // 编辑防火墙配置文件，开放3306端口
添加配置：-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
#systemctl restart iptables.service   // 最后重启防火墙使配置生效
#systemctl enable iptables.service   // 设置防火墙开机启动
```

## CentOS 7 使用iptables 开放端口
CentOS 7.0默认使用的是firewall作为防火墙，这里改为iptables防火墙。
1、关闭firewall：

```
systemctl stop firewalld.service
systemctl disable firewalld.service
systemctl mask firewalld.service
```
 
2、安装iptables防火墙

```
yum install iptables-services -y
```

3.启动设置防火墙

```
# systemctl enable iptables
# systemctl start iptables
```
 
4.查看防火墙状态

```
systemctl status iptables
```

5编辑防火墙，增加端口

```
vi /etc/sysconfig/iptables #编辑防火墙配置文件
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
:wq! #保存退出
```
 
3.重启配置，重启系统

```
systemctl restart iptables.service #重启防火墙使配置生效
systemctl enable iptables.service #设置防火墙开机启动
```

# linux-learning-notes
Linux学习笔记
## s*
[一键脚本](https://doubibackup.com/6r9z6_wi-5.html)

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

* 设置远程访问
注释#bind 127.0.0.1

* 测试redis连接是否成功
redis-cli -h 198.xxx.xxx.xxx -p 6379

离线安装
```
$ wget http://download.redis.io/releases/redis-4.0.9.tar.gz
$ tar xzf redis-4.0.9.tar.gz
$ cd redis-4.0.9
$ make
```
The binaries that are now compiled are available in the src directory. Run Redis with:
```
src/redis-server
```

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

端口范围
```
# frpc.ini
[common]
server_addr = 198.181.46.166
server_port = 5443
privilege_token = tIMrKiL94XZeXhb3

[range:test_tcp]
type = tcp
local_ip = 127.0.0.1
local_port = 80,443
remote_port = 4080,8443
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

## Nginx将静态文件响应POST请求，提示405错误问题
在nginx.conf中，请求的静态数据路径中，添加如下语句error_page 405=200 $request_uri：
```
location ~ \.(action|jsp) {
    root $testDataFold;
    error_page 405 =200 $request_uri;
}
```
## Centos使用yum构建离线安装包
在Centos中我们安装软件的方式往往是使用yum进行在线安装，但是在某些情况下（公司服务器使用的是内网）我们就需要使用yum将安装包下载到本地，并进行离线安装。以下是离线安装docker步骤：

首先创建一个目录用于存放安装包：

```
mkdir /usr/local/docker
```

通过yum命令下载docker安装文件：

```
yum install --downloadonly --downloaddir=/usr/local/docker docker-io
```

使用rpm命令进行安装
```
rpm -Uvh --force --nodeps /usr/local/docker/*rpm
```

## 安装sz,rz命令
```
wget https://raw.githubusercontent.com/chenhu1001/linux-learning-notes/master/rzsz-3.48.tar.gz
tar zxvf  rzsz-3.34.tar.gz
cd src
// 修改Makefile文件第四行的"OFLAG= -O"为"OFLAG= -O -DREGISTERED"
make posix
cp rz sz /usr/bin
```

## 自动上传文件脚本
```
#!/bin/bash

#---------------------------------------------------------------------------------------------
dir="/Users/chenhu/Documents/ticketPlatform2/ticketManagementH5/target/"  #jar 生成路径
#---------------------------------------------------------------------------------------------

main(){
	echo "...............................jar包开始上传到服务器"
	expect -c "
	spawn scp $dir/ticketManagementH5.jar  root@192.168.129.16:/data/h5/ticketManagementH5.jar
	expect {
    \"*assword:\" {set timeout 300; send \"123456\r\";}
      }
	expect eof"
	echo -e "==>jar包上传到服务器完成"
}

main
```


## Maven自动打包
```
cd /Users/chenhu/Documents/ticketPlatform2/ticketManagementH5
/Users/chenhu/apache-maven-3.5.4/bin/mvn clean install
```

## 远程部署服务
```
#!/bin/bash

#查询项目进程
JAR_pid=`ssh root@192.168.129.16 ps -ef | grep "ticketManagementH5.jar" | grep -v "grep"|awk '{print $2}'`
echo "JAR_pid="
echo $JAR_pid
if [  "$JAR_pid" != "" ];then
    ssh root@192.168.129.16 kill -9 $JAR_pid     #杀死项目进程
    echo "杀死项目进程"
else
    echo "进程不存在可以继续部署"
fi

echo "...............................开始启动服务"
ssh root@192.168.129.16 "nohup /data/tools/jdk1.8.0_151/bin/java -jar /data/h5/ticketManagementH5.jar &"
echo -e "==>服务器部署完成"
```

## 安装nginx
安装yum-utils
```
yum install yum-utils
```
设置 yum仓库, 编辑 /etc/yum.repos.d/nginx.repo
```
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
```
默认使用 stable的nginx, 要使用最新的需要切换到mainline (可选)
```
yum-config-manager --enable nginx-mainline
```
安装nginx
```
yum install nginx
```

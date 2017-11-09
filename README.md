# linux-learning-notes
Linux使用笔记
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


Linux 常用命令
=============


- 环境变量配置方法
```
$ sudo  vim /etc/profile
$ export PATH=XXXX:$PATH
```

- 安装与卸载软件
```

安装
$ yum install XXX

下载不安装
$ yum install --downloadonly --downloaddir='./root/download' XXX

查看已经安装的包
$ yum list installed

卸载软件
$ yum remove XXX

升级包
$ yum update XXX

```

- 创建软连接
```
http://blog.csdn.net/zhangxinyu11021130/article/details/64125058
alias py27="/usr/bin/python2.7" 
alias pyana="/home/myname/anaconda2/bin/python2.7"
```


- 文件赋予全部权限的方法
```
chmod 777 *.*
```


- 添加用户权限
```
修改权限
sudo chmod -R 777 exapp/

修改所属组别， 如将testfile文件夹的所有者改为zhc
chgrp -R zhc testfile

修改文件所有者, 如将testfile文件夹的所有者改为zhc
chown -R zhc testfile

```


- 查询端口命令
```
netstat –ntlp
netstat -apn | grep port
ps -aux | grep python
```


- 杀死进程
```
kill -9 <进程号>
```

- 文件操作
```
# 创建文件夹
mkdir XXX
# 创建文件
touch XXX.txt
# 合并文件
cat *.txt <a.txt
```

- 文件打包
```
tar 打包命令的使用
1. 仅打包，不压缩
$ tar -cvf /etc.tar /etc
2. 打包后，以 gzip 压缩
$ tar -zcvf /etc.tar.gz /etc
3. 打包后，以 bzip2 压缩
$ tar -jcvf /etc.tar.gz /etc

- tar 解压

解压到当前目录
$ tar -zxvf etc.tar.gz

解压到指定目录(如test 目录, 必须预先存在)
$ tar -zxvf etc.tar.gz -C ./test


参考:[https://www.cnblogs.com/lijc1990/p/3545503.html](https://www.cnblogs.com/lijc1990/p/3545503.html)
```



-  服务器之间拷贝文件

```
scp [参数] <源地址（用户名@IP地址或主机名）>:<文件路径> <目的地址（用户名 @IP 地址或主机名）>:<文件路径> 
举例： 
scp /home/work/source.txt work@192.168.0.10:/home/work/  #把本地的source.txt文件拷贝到192.168.0.10机器上的/home/work目录下  
scp work@192.168.0.10:/home/work/source.txt /home/work/  #把192.168.0.10机器上的source.txt文件拷贝到本地的/home/work目录下
scp work@192.168.0.10:/home/work/source.txt work@192.168.0.11:/home/work/  #把192.168.0.10机器上的source.txt文件拷贝到192.168.0.11机器的/home/work目录下
scp -r /home/work/sourcedir work@192.168.0.10:/home/work/  #拷贝文件夹，加-r参数 
scp -r /home/work/sourcedir work@www.myhost.com:/home/work/  #使用主机名 
scp -r -v /home/work/sourcedir work@www.myhost.com:/home/work/  #显示详情，加-v参数
```


- linux 切换国内镜像 
```
参考: http://blog.csdn.net/qingguiyu/article/details/50721956
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup #备份原有yum
cd /etc/yum.repos.d/#切到源目录
下载阿里云源：
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
运行以下命令生成缓存：
yum clean all
yum makecache
```


- 查找包与文件
```
# 查找包
binstar search -t conda jieba
# 查找文件
locate file.txt
```

- 查询linux目录下内存使用情况
```
du -sh /home/*
清理var/log/cups/error_log
kill cups 进程
```

- 查询目录下文件物理内存占用情况
```
$ du -h --max-depth=1 /home
```

- pip 安装使用

```
1. 下载与安装包
(1) 下载
批量下载
$ pip download -d \home\packs -r requirement.txt
其中requirement.txt 内容认为 
django==1.8.11
simplejson==3.14.0

或者
$ pip download -d \home\packs django==1.8.11 simplejson==3.14.0

(2) 指定源安装
$ pip install web.py -i http://pypi.douban.com/simple
参考：[https://www.cnblogs.com/sunnydou/p/5801760.html](https://www.cnblogs.com/sunnydou/p/5801760.html)

2. 卸载文件
批量卸载
$ pip uninstall -r aa.txt

3. 查看已经安装的包
$ pip freeze
$ pip freeze > piplist.txt

```




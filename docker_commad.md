# Docker 常见命令及其含义

## 一、容器查看启动终止删除

### docker ps 查看现有容器状态

- 主要有些参数说明：
```
1. 不加参数，表示查看当前正在运行的容器 
2. -a，查看所有容器包括停止状态的容器 
3. -l，查看最新创建的容器 
4. -n=x，查看最后创建的x个容器 
```
- docker ps 的结果含义说明：
```
CONTAINER ID：容器ID，唯一标识容器 
IMAGE：创建容器时所用的镜像 
COMMAND：在容器最后运行的命令 
CREATED：容器创建的时间 
STATUS：容器的状态（你会看到UPXXX，表示运行状态） 
PORTS：对外开放的端口号 
NAMES：容器名（也具有唯一性，docker是不允许创建容器名相同的容器的） 
```

### docker 启动
```
docker start 容器名或容器ID
```

### docker 停止
```
# 二选一，建议使用kill
docker stop 容器名或容器ID
docker kill 容器名或容器ID
```

### docker 删除容器
```
第一步：停止正在运行的docker 容器
docker kill 容器名或容器ID

第二步：移除正在运行的docker 容器
# 删除某一个容器
docker rm 容器名或容器ID

# 删除多个容器
docker rm 容器名或容器ID 容器名或容器ID ...

# 删除全部容器
docker rm `docker ps -a -q`

```

### docker 容器备份

- 通过commit/save/load 备份
```
第一步：选择要备份的容器，然后创建该容器的快照，可以使用 docker commit 命令来创建快照
$ docker commit -p containID container_backup

第二步： 通过docker images 命令来查看创建的容器镜像快照
$ docker images

第三步： 将创建的容器镜像快照保存到本地，供后续使用
$ docker save -o ./container_backup.tar container_backup

第四步： 恢复本地镜像快照的容器
$ docker load -i ./container_backup.tar

最后, 加载镜像运行Docker容器
$ docker run -d -p 80:5000 --name container_name --restart=always -v $path container_backup

参考：https://www.cnblogs.com/boshen-hzb/p/6373549.html
```

- 通过export/import备份
```
第一步：export导出目标容器
$ docker export containerid > dockerexport.tar
第二步：使用import加载镜像
$ docker import dockerexport.tar imagename:version
第三步：启动容器
说明：需要根据容器的启动方式设置后面的启动命令尾缀，此处示例用/bin/bash
$ docker run -d -p port:port --name container_name --restart=always -v $path imagename:version  /bin/bash

```




### docker 进入容器操作
```
1. 进入容器
$ docker exec -it elastic_driscoll /bin/bash
其余操作同linux
```

## 二、外部访问容器

容器中可以运行一些网络应用，要让外部也可以访问这些应用，可以通过 -P 或 -p 参数来指定端口映射。当使用 -P 标记时，Docker 会随机映射一个 49000~49900 的端口到内部容器开放的网络端口。

1. 使用 -P 随机生成访问docker端口
```
# 例如：使用 docker container ls 可以看到，本地主机的 49155 被映射到了容器的 5000 端口。此时访问本机的 49155 端口即可访问容器内 web 应用提供的界面
$ docker run -d -P training/webapp python app.py
$ docker container ls -l
CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        PORTS                    NAMES
bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse

```

2. 使用 -p 指定映射端口
```
# -p 则可以指定要映射的端口，在一个指定端口上只可以绑定一个容器, 支持的格式：
# ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort
$ docker run -d -p hostPort:containerPort training/webapp python app.py

```

## 三、docker 离线安装
```
1. 下载docker 安装包
$ yum install --downloadonly --downloaddir=/root/docker-ce docker-ce
2. 安装RPM 包
$ rpm -ivh --force *.rpm
3. 启动服务
$ systemctl enable docker.service
$ systemctl start docker.service
$ docker images # 查看docker 镜像


```

## 四、docker 私有仓库搭建
```
# 仓库搭建
1. 从docker官方镜像pull 下registry镜像到本地
$ docker pull registry
2. 将本地镜像registry打包为文件
$ docker save -o registry.tar registry
3. 将文件拷到新的服务器，并加载镜像
$ docker load -i registry.tar
4. 执行以下命令，会启动一个registry容器，该容器用于提供私有仓库的服务
$ docker run --name docker-registry -d -p 5000:5000 registry
5. 执行命令curl -X GET http://127.0.0.1:5000/v2/_catalog，收到的响应如下，是个json对象，
   其中repositories对应的值是空的json数组，表示目前仓库里还没有镜像
$ curl -X GET http://127.0.0.1:5000/v2/_catalog
$ {"repositories":[]} # 此时无镜像，故为空

# 镜像相关操作
1. 删除本地镜像
$ docker rmi registry
2. 从官方拉镜像
$ docker pull registry
3. 上传镜像到私有仓库
docker push localhost:5000/XXX

```


## 其他

1. 在centos下更新docker 到最新版本
```
# Update to upgrade docker CentOS 7.4
$ yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux  docker-engine-selinux docker-engine
$ yum install -y yum-utils device-mapper-persistent-data lvm2
$ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
$ yum install docker-ce

# Start your docker service
$ systemctl start docker
$ systemctl enable docker
$ systemctl status docker

2 . 其他
进入镜像 
$ docker run -t -i ebc8bc6b0a20 /bin/bash

```

2. docker 配置中国镜像源
```
1. 使用vi修改 /etc/docker/daemon.json 文件并添加上”registry-mirrors”: [“https://registry.docker-cn.com“]：

vi /etc/docker/daemon.json 
{ 
“registry-mirrors”: [“https://registry.docker-cn.com“] 
}

2. 配置完之后重启docker,配置文件生效
$ systemctl daemon-reload
$ systemctl restart docker

3. 测试配置的结果, 对比速度
$ docker pull tensorflow/serving

提示：若部署tensorflow/serving后运行出现“No module named 'tensorflow_serving.util”, 
请使用“pip install tensorflow-serving-api”安装包解决。

```


## 常见问题
1. docker 安装完成无法正常启动
```
问题示例(1)： "bridge" network: failed to allocate gateway (11.90.20.0): Ad dress already in use
解决方案： 修改docker网络的网段
具体为：
$ vi /etc/docker/daemon.json
{
    "log-driver":"json-file",
    "bip":"11.90.20.0/24",
    "log-opts":{
        "max-size":"500M",
        "max-file":"5"
    }
}

修改daemon.json文件即可，具体为：
修改冲突的bip地址 11.90.20.0/24 改为 11.90.20.1/24 即可

```




# Reference

[https://blog.csdn.net/vevenlcf/article/details/82995838#/m/home/](https://blog.csdn.net/vevenlcf/article/details/82995838#/m/home/)
[https://blog.csdn.net/boling_cavalry/article/details/78818462](https://blog.csdn.net/boling_cavalry/article/details/78818462)
[https://blog.csdn.net/u013246898/article/details/52945884](https://blog.csdn.net/u013246898/article/details/52945884)
[https://yeasy.gitbooks.io/docker_practice/content/network/port_mapping.html](https://yeasy.gitbooks.io/docker_practice/content/network/port_mapping.html)

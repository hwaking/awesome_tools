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






# Reference

[https://blog.csdn.net/u013246898/article/details/52945884](https://blog.csdn.net/u013246898/article/details/52945884)
[https://yeasy.gitbooks.io/docker_practice/content/network/port_mapping.html](https://yeasy.gitbooks.io/docker_practice/content/network/port_mapping.html)

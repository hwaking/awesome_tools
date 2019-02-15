# Docker 常见命令及其含义

## 容器查看启动终止删除

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



# Reference

[https://blog.csdn.net/u013246898/article/details/52945884](https://blog.csdn.net/u013246898/article/details/52945884)

### service  docker status

查看docker的运行状态

### docker  run

run a command in a new container

- i 以交互模式运行容器
- t  为容器重新分配一个伪输入终端
- d  后台运行容器 ，并返回容器ID
- p 指定端口映射
- P 随机端口映射
- name 为容器指定一个名称

- v 容器数据卷    
```
docker - itd -v /data:/container_data centos
# 只读
docker - itd -v /data:/container_data:ro centos
```


### docker ps 

list containers

- a show all containers ( default show just running )

### docker create 

create a new container 
### docker images 

list images 


- a  show all images ( default hides intermediate images)

### docker stop 

stop one or more running containers 

### docker kill

kill one or more running containers 

### docker pause 
Pause all processes within one or more containers

### docker rm

remove one or more containers

### docker commit

#把一个容器封装为一个镜像
Create a new image from a container's changes

### docker exec 

在容器中打开新的终端，并且可以启动新的进程
Run a command in a running container

### docker attach 

直接进入容器启动命令的终端，不会启动新的进程
Attach local standard input, output, and error streams to a running container

### docker inspect 

Return low-level information on Docker objects

### docker restart 

restart one or more containers


### docker cp

宿主机和container 之间复制文件


### docker build

Build an image from a Dockerfile






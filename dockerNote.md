## 初识Docker
#### 概述图
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071616212269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)

```shell
docker version  	#显示基本信息
docker info			#显示详细信息
docker  --help   	#帮助命令
```
####  镜像命令
```shell
docker images
-a 		#列出所有
-q		#列出ID
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE

docker search mysql 	#搜索镜像
--filter=STARS=3000

docker pull	mysql:5.7 #下载镜像

docker rmi -f [ID] ][ID2]	#删除镜像
docker rmi -f ${docker images -aq} #删除全部镜像
```

#### 容器命令
```shell
#新建容器并启动
docker run image
#参数说明
--name="Name"  容器名字
-it #使用交互方式运行，进入容器
-P #指定端口
-p #随机指定端口

#测试
docker run -it /bin/bash #启动并进入容器
#退出
exit
docker ps        #列出运行中的容器
docker ps -a	 #查看当前运行+曾经运行的容器
-n=? 			#显示最近创建的容器
-q       		#只显示容器的编号
#退出容器
exit #直接退出并停止
ctrl + p + q #退出 不停止
#删除容器
docker rm 容器ID  #删除指定容器，不能删除正在运行的容器
docker rm -f$(docker ps -aq)  #删除所有容器，能删除正在运行的	
docker ps -a -q|xargs docker rm #删除所有容器
docker start ID
docker restart ID
docker stop ID
docker kill ID
# 查看日志
docker logs -t -f --tail 10 镜像名称
#查看容器中进程 
docker -top 容器ID
#查看镜像的元数据
docker inspect 容器ID
```
####  进入当前正在运行的容器
```shell
方式一
docker exet -it 容器ID /bin/bash   #进入容器后开启一个新的终端
方式二
docker attach 容器ID	 	#进入容器当前打开的终端，不会启动新的进程
```

#### 从容器拷贝文件到主机
```shell
docker cp 容器ID：容器内路径 目的地主机路径
拷贝是个手动过程，未来使用-v 卷的技术，可以实现自动同步
```
#### 可视化
```shell
protainer(先用这个）
dockers的图形化页面管理工具，提供个后台面板供我们操作。

Rancher（CI/CD再用）
```
## 镜像
>镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件。它包含运行某个软件所需的所有内容，包括代码、运行环境、库、环境变量和配置文件。
所有的应用，直接打包docker镜像，就可以运行了。
## Docker镜像加载原理
>UnionFS（联合文件系统）UnionFS是一种分层、轻量级并且高性能的文件系统。
## 分层理解
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721143411114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
```shell
docker inspect 容器ID
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721143445618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721144003419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
 
## 特点
>docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！---这一层就是我们通常所说的容器层，容器层之下都是镜像层。

如何提交一个自己的镜像
## commit 镜像
```shell
docker commit 提交容器为一个新的副本
# 命令和git相似
docker commit -m="提交描述信息" -a="作者" 容器ID 目标镜像名称：[TAG]
# 练习
# 启动默认tomcat，官方默认tomcat webapps下是没有文件的！
docker run -it -p 8080:8080 tomcat
docker ps
docker exec -it 容器ID /bin/bash/
# 自己copy进去基本文件
cp -r webapps.dist/* webapps
# 查看cp的文件
cd webapps 
exit
docker ps 
# 将操作过的容器提交为新的镜像
docker commit -a="lucio" -m="add webapps info" 容器ID tomcat02:1.0	 
```
## 容器数据卷
>容器可以删除，如果数据在容器中，那么镜像删除后，数据就丢失了，这是我们不希望看到的。
==需求：数据可以持久化==
Mysql 容器删除，数据没了，等同于删库跑路！！！
引出，容器间需要一个数据共享技术！

docker容器产生的数据，同步到本地--这就是卷技术！目录的挂载，将我们容器内的目录，挂载到Linux上面！
> 总结一句话：为了容器的持久化和同步操作！

方式1：直接用命令挂载 -v
```shell
docker run -it -v 主机目录地址:容器目录地址
#测试
docker run -it -v /home/test:home centos /bin/bash
docker inspect 容器ID

这样两个地址下的文件就会互相同步了，只要容器不删除，是否启用都会同步内容
好处：我们只需要本地修改，容器内就会同步

## 思考
mysql 数据持久化
docker pull mysql:5.7

#具名挂载、匿名挂载
#匿名挂载 -v 容器对应路径
docker run -P --name nginx01 -v /ect/nginx nginx
#查看所有卷的情况
docker volume ls
#这里发现我们，只写了容器内路径，没有指定路径，它会默认生成路径

#具名挂载 
docker run -P --name nginx02 -v juming-nginx:/ect/nginx nginx
docker volume juming-naginx
#没有指定路径的  都会生成到 /var/lib/docker/volumes/
```



####  初识DockerFile
>DokcerFile就是构建Docker镜像的文件！
（本质命令脚本）
方式一：docker commit
方式二：DockerFile
```shell
#文件内容
FROM centos

VOLUME ["volume01","volume02"]  #匿名挂载

CMD echo "---END---"

CMD /bin/bash


#测试
docker build -f /home/dockerfile1 -t lucio/centos

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d0f19d5dce8f46a0bf2951d7b88bc022.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)

## 数据卷容器
例：两个mysql同步数据
--volumes-from 
![在这里插入图片描述](https://img-blog.csdnimg.cn/e1e9675868a448eebc4fcdd0de15f5ea.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
结论：容器之间可以进行配置文件的传递，数据卷的生命周期一直持续到没有容器使用为止。
## DockerFile
#### DockerFile介绍
>构建步骤：
1.编写一个DockerFile
2.docker build 构建成为一个镜像
3.docker run 运行镜像
4.docker push 发布镜像（DockerHub、阿里云）
#### DockerFile构建过程
DockersFile是面向开发的，我们要发布项目，做镜像就需要编写DockerFile文件，这个文件很简单！

DokcerFile成为企业交付的标准，要掌握！
步骤：开发，部署，运维

>DokcerFile:构建文件，定义了步骤，相当于源代码
DockerImages：通过DockerFile构建的镜像，最终发布和运行的产品，原来是jar war 
Docker容器：镜像运行起来，提供服务
#### DockerFile指令
指令：
1.每个指令必须大写
2.从上到下执行
3. # 表示注释
4.每个指令都会提交一个镜像层，并提交

>FROM      		#基础镜像
>MAINTAINER  	#镜像作者，姓名+邮箱
>ADD             #添加内容
WORKDIR 		#镜像工作目录
VOLUME 			#挂载的目录
EXPOSE			#指定对外的暴露端口
RUN            	#镜像构建的时候需要运行的命令
CMD				#指定容器启动的时候要运行的命令，只有最后一个会生效，会被替代
ENTRTPOINT		#指定容器启动的时候要运行的命令,可以追加命令
ONBUILD			#当构建一个被继承的DockerFile，这时候就会运行ONBUILD指令（触发指令）
COPY			#类似ADD ，将我们的文件复制到目录中
ENV				#构建时候，设置环境变量

**PS: DockerHub中99%镜像都是FROM scratch,然后编写需要的软件和配置来进行构建的**

## Docker 网络
理解Docker0
>测试，查看IP地址
>ip addr
>#问题 dokcer是如何处理网络访问的？

```shell
docker run -d -P --name tomcat01 tomcat
#查看容器内部网络地址 ip addr,发现容器启动后会得到ech0@if262 ip地址
docker exec -it tomcat01 ip addr

```
>原理
>1.我们每启动一个docker容器，docker就会给容器分配一个ip，我们只要安装了docker，就会有一个docker0--桥接模式，使用的技术是veth-pair 技术！
>2.再启动个容器 ，发现又多了一对网卡
>evth-pair 就是一对虚拟设备接口，他们都是成对出现的，一端连接着协议，一段彼此相连
>正因为又这个特性，veth-pair充当一个桥梁作用，连接各种虚拟网络设备
>openStac、docker容器之间、ovs连接，都是使用veth-pair技术。
>3.两个容器之间 可以直接ping通的。
>docker  exec -it tomcat02 ping 172.18.0.2
![在这里插入图片描述](https://img-blog.csdnimg.cn/86934eced9244a709edd990626987282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
>结论：tomcat01、tomcat02公用路由器Dokcer0
>所有的容器，在不指定网络的情况下，都是由Docker0分配默认IP

>小结：docker使用的是linux的桥接
>![在这里插入图片描述](https://img-blog.csdnimg.cn/b6556d3dce0c4723b3a01f98c0cbb916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
>Docker中所有网络都是虚拟的，虚拟的转发效率高！
## --link
> 思考一个场景，我们编写一个微服务，database url:ip,项目不重启，数据库IP变了，我们希望处理这个问题，可以通过名字访问容器吗？
>如何解决呢?通过【--link】
>docker run -d -P --name tomcat03 --link tomcat02 tomcat
> docker exec -it tomcat03 ping tomcat02
> 反向可以ping通吗？【不能】 

探究：inspect 
本质探究： --link 就是在hosts中配置了 172.18.03 tomcat02
>PS: 现在已经不建议使用--link
>Docker0 问题不支持容器名访问

### 自定义网络
```shell
docker network --help
# 查看所有docker网络
docker network ls
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/40a2b7856906423bac85a0bfde57a49f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
### 网络模式
1.bridge:桥接
2.none:不配置网络 
3.host:和宿主机共享网络
4.container:容器网络连接（用的少！局限很大）


```shell
#测试
#docker0特点，是默认的，域名不可以访问！--link可以打破

docker nerwork create --hellp
#自定义网络,PS：这个就是家里路由器的配置
docker network create --driver bridge --subnet 192.168.0.0/16 --getway 192.168.0.1 mynet
#查看自定义网络配置
docker inspect mynet
#启动容器配置网络为mynet
docker -run -d -P --name tomcat01 --net mynet tomcat
docker -run -d -P --name tomcat02 --net mynet tomcat
docker exec -it tomcat01 ping tomcat02
#现在不使用--link 也通过容器名访问了
```
>我们自定义的网络docker已经帮我们维护好关系了，推荐我们使用这样的网络
>好处
>redis,不同集群使用不同网络，保证集群是健康安全的
>mysql，不同集群使用不同网络，保证集群是健康安全的

### 网络连通
```shell
#测试打通 tomcat01 与网络mynet
#connect a container to a network 将一个容器连接到网络
docker network connect mynet tomcat01
#连通之后，将tomcat01 放到了mynet下
```
>结论：docker network connect 实现得是’容器‘跨网络操作‘容器’

### 实战：部署Redis集群
分片 + 高可用 + 负载均衡

```shell
 	# 创建网卡
    docker network create redis --subnet 172.38.0.0/16
    
    # 通过脚本创建六个redis配置
    for port in $(seq 1 6);\
    do \
    mkdir -p /mydata/redis/node-${port}/conf
    touch /mydata/redis/node-${port}/conf/redis.conf
    cat << EOF >> /mydata/redis/node-${port}/conf/redis.conf
    port 6379
    bind 0.0.0.0
    cluster-enabled yes
    cluster-config-file nodes.conf
    cluster-node-timeout 5000
    cluster-announce-ip 172.38.0.1${port}
    cluster-announce-port 6379
    cluster-announce-bus-port 16379
    appendonly yes
    EOF
    done
    
    # 通过脚本运行六个redis
    for port in $(seq 1 6);\
    docker run -p 637${port}:6379 -p 1667${port}:16379 --name redis-${port} \
    -v /mydata/redis/node-${port}/data:/data \
    -v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
    -d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
    
    #redis默认没有bash
    docker exec -it redis-1 /bin/sh 
    redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379  --cluster-replicas 1
#docker 搭建redis集群完成！
```
### 


实战
Docker Compse
Docker Swarm

CI/CD Jenkins流水线

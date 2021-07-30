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

实战
Docker Compse
Docker Swarm

CI/CD Jenkins流水线

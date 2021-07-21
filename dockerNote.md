概述图
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021071616212269.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)

```shell
docker version  	#显示基本信息
docker info			#显示详细信息
docker  --help   	#帮助命令
```
镜像命令
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

容器命令
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
进入当前正在运行的容器
```shell
方式一
docker exet -it 容器ID /bin/bash   #进入容器后开启一个新的终端
方式二
docker attach 容器ID	 	#进入容器当前打开的终端，不会启动新的进程
```

从容器拷贝文件到主机
```shell
docker cp 容器ID：容器内路径 目的地主机路径
拷贝是个手动过程，未来使用-v 卷的技术，可以实现自动同步
```
可视化
```shell
protainer(先用这个）
dockers的图形化页面管理工具，提供个后台面板供我们操作。

Rancher（CI/CD再用）
```
## 镜像
镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件。它包含运行某个软件所需的所有内容，包括代码、运行环境、库、环境变量和配置文件。
所有的应用，直接打包docker镜像，就可以运行了。
## Docker镜像加载原理
UnionFS（联合文件系统）UnionFS是一种分层、轻量级并且高性能的文件系统。
## 分层理解
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721143411114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
```shell
docker inspect 容器ID
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721143445618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210721144003419.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NzZG4xMDUwMDU2MDIy,size_16,color_FFFFFF,t_70)
 
## 特点
docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！---这一层就是我们通常所说的容器层，容器层之下都是镜像层。

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

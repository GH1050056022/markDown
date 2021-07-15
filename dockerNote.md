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
```

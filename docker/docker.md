- 概述

  文档地址：https://docs.docker.com/

  仓库地址：



#### 安装

```shell
需要的安装包：yum install -y yum-utils
设置镜像仓库：
    yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
更新yum软件包索引：
    	yum makecache fast
安装docker
    yum install docker-ce docker-ce-cli containerd.io
启动docker：
    systemctl start docker
查看版本：docker version
		docker run hello-world
#docker重启
	systemctl restart docker
#查看镜像：
docker images
/var/lib/docker  docker的默认工作路径
```

阿里云镜像加速



#### docker是怎么工作的

​	docker是一个Client-Server结构的系统，Docker的守护运行在主机上，通过Socker从客户端访问

​	DockerServer接收到client命令

![image-20200613141255016](images\image-20200613141255016.png)

​	docker为什么比VM快

​		docker有着比vm更少的抽象层

​		docker利用的是宿主机的内核

​		

#### docker的常用命令

```shell
docker inof	#显示docker的系统信息，包括镜像和容器的数量
docker --help #万能命令
```

帮助文档地址：https://docs.docker.com/reference/

##### 镜像命令

**docker images  查看所有镜像**

```shell
[root@localhost apply]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              bf756fb1ae65        5 months ago        13.3kB
#解释
REPOSITORY	镜像的仓库源
TAG			镜像的标签
IMAGE ID	镜像的ID
CREATED		镜像的创建时间
SIZE		镜像的大小
# 可选项
-a,--all #列出所有镜像
-q,--quiet #只显示镜像的Id
```

**docker search搜索镜像**

```shell
docker search mysql

[root@localhost apply]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9621                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3495                [OK]                
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   702                                     [OK]
#可选项 通过搜索来过滤

```

**docker pull 下载镜像**

```shell
[root@localhost docker]# docker pull mysql
Using default tag: latest #如果不写 tag默认是latest
latest: Pulling from library/mysql
8559a31e96f4: Pull complete #分层下载，docker images的核心 联合文件系统
d51ce1c2e575: Pull complete 
c2344adc4858: Pull complete 
fcf3ceff18fc: Pull complete 
16da0c38dc5b: Pull complete 
b905d1797e97: Pull complete 
4b50d1c6b05c: Pull complete 
c75914a65ca2: Pull complete 
1ae8042bdd09: Pull complete 
453ac13c00a3: Pull complete 
9e680cd72f08: Pull complete 
a6b5dc864b6c: Pull complete 
Digest: sha256:8b7b328a7ff6de46ef96bcf83af048cb00a1c86282bfca0cb119c84568b4caf6 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #真实地址

docker pull mysql #等价于
docker pull docker.io/library/mysql:latest


docker  pull 镜像名[:tag]
[root@localhost docker]# docker pull mysql:5.7

```

**docker rmi 删除镜像**

```shell
# 按ID删除
docker rmi -f bf756fb1ae65
doker rmi -f ID ID ID   #删除多个
#删除所有
docker rmi -f $(docker iamges -aq)
```

##### 容器命令

 先下载镜像

```shell
docker pull centos
```

**新建容器并启动**

```shell
docker run [可选参数] image
#参数说明
--name="Name"	容器名称  tomcat01  tomcat02  用来区分容器
-d				后台方式运行
-it				使用交互式方式运行，进入容器查看内容
-p				指定容器的端口 -p 8080:8080
		-p 主机端口：容器端口  【常用】
		-p 容器端口
-P				随机指定端口

#测试 启动并进入容器
[root@localhost docker]# docker run -it centos /bin/bash
[root@56e0a2aa9829 /]# ^C
#退出容器
exit
```

**docker ps列出所有的运行的容器**

```shell
#docker ps 
	#列出当前正在运行的容器
-a #列出当前正在运行的容器+带出历史运行过的容器
-n=数字  #显示最近创建的容器 列出几个
-q 		#列出只显示容器ID
```

**删除容器**

```shell
exit #指定容器停止，并推出
ctrl+p+q  #容器不停止退出
```

**删除容器**

```shell
docker rm 容器ID #删除指定容器 不能删除正在运行的容器，如果要强制删除 rm -f
docker rm -f $(docker ps -aq) #所有
```

**启动和停止容器操作**

```shell
docker start 容器Id	#启动容器
docker restart 容器Id	#重启容器
docker stop 容器Id	#停止当前正在运行的容器
docker kill 容器Id	#强制停止当前容器
```

**常用的其他命令**

``` shell
#后台启动 docker run -d 镜像Id
	docker run -d centos
#问题docker ps 发现centos停止了
#常见的坑，docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
#nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了

#查看日志命令
docker logs
docker logs -tf --tail 10条数 容器Id #显示指定行数日志
docker logs -tf 容器Id#显示所有日志

#查看容器中进程信息
docker top 容器Id

#查看镜像源数据
docker inspect 容器Id

#进入当前正在运行的容器
方式一：docker exec -it 容器Id bashShell （/bin/bash）
方式二：docker attach 容器Id    

docker  exec 进入容器后开启一个新的终端，可以在里面操作
docker attach  进入容器正在执行的终端，不会启动新的进程

#从容器内拷贝文件到宿主机
docker cp 容器Id:容器内路径 目的主机路径

```



实战操作

Docker安装nginx

```shell
#搜索nginx镜像
docker search nginx
#下载镜像
docker pull nginx
#启动
docker run -d --name=nginx01 -p 3344:80 nginx
#-d 后台启动
#--name 给容器命名
#-p  端口映射

#进入nginx容器
[root@localhost docker]# docker exec -it 5c /bin/bash

#思考问题 每次改动nginx配置文件，都需要进入容器内部十分的麻烦，我们要是可以在容器外部提供一个映射路径，达到在容器修改文件名，容器内部就可以自动修改  --数据卷

```



Docker安装tomcat

```shell
docker run -it --rm tomcat:9.0
#我们之前的启动都是后台，停止了容器之后，容器还是可以查到，    docker run -it --rm 一般用来测试，用完即删除


docker pull tomcat:9.0
#启动运行
docker run -d -p 3355:8080 --name=tomcat01 tomcat
#进入容器
[root@localhost docker]# docker exec -it tomcat01 /bin/bash
#发现问题：1.linux命令少了	2wenapps是空的	阿里云镜像的原因，默认是最小的镜像，所有不必要的
```

我们要是可以在容器尾部提供一个映射路径，webapps我们在外部放置项目，就自动同步到内部



Docker 部署es和kibana

```shell
#es 暴露的端口很多
#es 十分耗内存
#es 的数据一般需要放置到安全目录，挂载
#--net somenetwork  网络配置
#启动es
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag

#启动之后  卡顿


#docker stats  查看状态

#增加内存的限制，修改配置文件 -e 环境配置修改
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms512 -Xmx512m" elasticsearch:tag


```

##### 可视化

portainer

Rancher(CI/CD再用)



portainer是什么

Docker图形化管理工具，提供一个后台面板供我们操作

```shell
docker run -d -p  8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```



##### Docker镜像讲解

镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需要的所有内容，包括代码、运行时库、环境变量和配置文件。

所有的应用，直接打包docker镜像，就可以直接跑起来 

如何得到镜像:

​	1.从远程仓库下载

​	2.拷贝

​	3.自己制作一个镜像Docker



##### Commit 镜像

```shell
docker commit #提交容易成为一个新的副本
docker commit -m="提交信息" -a="作者"  容器Id  目标镜像名:[tag]

[root@localhost ~]# docker commit -a="i-leizh" -m="add webapps" d08f2b0154f6 mytomcat:1.0

```

##### 容器数据卷

​	docker的理念

​		将应用和环境打包

方式一：直接使用命令来挂载：-v

```shell
docker run -it -v 主机目录：容器内目录
#测试
docker run -it -v /home/ceshi:/home centos /bin/bash

#查看详细信息 docker inspect 容器Id   Mounts节点

```

##### Mysql数据实战

```shell
#运行容器 挂载数据  需要配置mysql密码  -e MYSQL_ROOT_PASSWORD=my-secret-pw
docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Zh199609! --name=mysql01 mysql:5.7
```

##### 具名和匿名挂载

```shell
# 匿名挂载
-v 容器内路径
docker run -d -p --name nginx0001 -v /etc/nginx nginx
# 查看所有的
docker volume ls

DRIVER              VOLUME NAME
local               b03164a297ab2507d3df05990180a86a989fc11496c326bf101b928da9f40767

#这里发现 这种就是匿名挂载，我们在 -v 只写了容器内路径，没有写容器外的路径


#具名挂载
docker run -d -P --name nginx0002 -v juming-ngxin:/etc/nginx nginx

#通过 -v 卷名:容器内路径
[root@localhost data]# docker volume ls
DRIVER              VOLUME NAME
local               b03164a297ab2507d3df05990180a86a989fc11496c326bf101b928da9f40767
local               juming-ngxin

#查看卷
[root@localhost data]# docker volume inspect juming-ngxin
[
    {
        "CreatedAt": "2020-06-29T23:00:48+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-ngxin/_data",
        "Name": "juming-ngxin",
        "Options": null,
        "Scope": "local"
    }
]




```

所有docker容器内的卷，没有指定目录的情况下都是在 /var/lib/docker/volumes/xxxx/_data

我们通过具名挂载可以方便的找到我们的一个卷，大多数情况在使用的具名挂载



<<<<<<< HEAD
##### 初始Dockerfile

DockerFile就是用来构建docker镜像的文件命令参数脚本

构建步骤：、

​	1.编写一个dockerfile文件

​	2.docker build 构建一个镜像

​	3.docker push 发布镜像



###### 基础知识

每个保留关键字（指令）都是必须是大写字母

执行从上到下

#表示注释

每一个指令都会创建提交一个新的镜像层，并提交

##### Dockerfile的指令

```shell
FROM		#基础镜像，一切从这里开始构建
MAINTAINER	#镜像是谁写的
RUN			#镜像构建的时候需要运行的命令
ADD			#步骤：tomcat镜像，这个tomcat压缩包，添加内容
WORKDIR		#镜像的工作目录
VOLUME		#挂载的目录
EXPOSE		#暴露端口
CMD			#指定这个容器要启动时要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT	#指定这个容器启动的时候要运行的命令
ONBUILD		#当构建一个被继承DockeFile 这个时候就会运行ONBUILD的指令  触发指令
COPY		#类似，将我们的文件拷贝到镜像中
ENV			#构建的时候设置环境变量
```
=======




**初始Dockerfile**
Dockerfile就是用来构建docker镜像的构建文件，命令脚本

通过这个脚本可以生成镜像,镜像是一层一层，脚本是一个一个的

```shell
FROM centos
VOLUME ["volume01","volume02"]
CMD echo "---end---"
CMD /bin/bash
```




>>>>>>> 47471e84b54c639d0f944e85ae05403e02e6d016


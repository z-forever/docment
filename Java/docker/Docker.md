# Docker

### Docker 概述

##### Docker和虚拟机技术的不同

● 传统虚拟机,虚拟出一条硬件,运行-个完整的操作系统,然后在这个系统上安装和运行软件

● 容器内的应用直接运行在宿主机的内容,容器是没有自己的内核的,也没有虚拟我们的硬件,所以就轻便了

● 每个容器间是互相隔离,每个容器内都有一个属于自己的文件系统,不影响



> DevOps ( 开发, 运维)

**应用快速交付和部署**

> Docker : 打包镜像发布测试 ,一键运行

**便捷的升级和扩缩容**

> Docker : 项目打包为一个镜像 ,扩展服务器 a,b,c

**更简单的系统运维**

> 在容器化之后 ,开发 测试环境高度一致

**更高效的计算资源利用**

> Docker 是内核级别的虚拟化, 一个物理机可以运行多个容器



##### Docker的组成

**镜像 ( image )**

> docker镜像就好比是一个模板 ，可以通过这个模板来创建容器服务，tomcat镜像===> run ==> tomcat01容器( 提供服务器)，
> 通过这个镜像可以创建多个容器(最终服务运行或者项目运行就是在容器中的)。

**容器 ( container )**

> Docker 利用容器技术, 独立运行一个或多个组应用 ,通过镜像创建
>
> 启动,停止,删除,基本命令

**仓库 ( repository )**

> 存放镜像的地址



##### 环境准备

> Linux 基础
>
> Centos 7
>
> Xshell

##### 环境查看

```shell
# 查看系统内核
uname -r
# 系统环境配置
cat /etc/os-release

```

##### CentOS安装Docker

> **官方文档地址 :** https://docs.docker.com/engine/install/centos/

```shell
# 1. 卸载旧的版本
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2. 需要的安装包
sudo yum install -y yum-utils

# 3. 设置镜像仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo		# 官网
    
# 4. 更新 yum 软件包索引
sudo yum makecache fast
    
# 4. 安装Docker相关客户端,服务端 ce社区版 ,ee企业版
sudo yum install docker-ce docker-ce-cli containerd.io		# 默认安装最新
sudo yum install docker-ce docker-ce-cli containerd.io --allowerasing	# centos8 冲突

# 5. 启动Docker
systemctl start docker

# 6. 查看是否安装成功
docker version

# 7. 测试 hello-world
docker run hello-world

# 8. 查看下载的hello-world镜像
docker images

```

##### CentOS卸载Docker

```shell
# 1. 卸载依赖
sudo yum remove docker-ce docker-ce-cli containerd.io

# 2. 删除安装环境
sudo rm -rf /var/lib/docker		# 默认资源路径
```

##### CentOS阿里云镜像加速

```shell
# 1. 创建一个文件
sudo mkdir -p /etc/docker

# 2. 配置aliyun地址
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1qmf3n68.mirror.aliyuncs.com"]
}
EOF
# 3. 重启服务/docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```





### Docker 命令

##### 帮助命令

```shell
docker version			# 显示docker版本信息
docker info				# 显示docker系统信息,包括镜像和容器数量
docker images --help	# 查询docker镜像帮助命令

```



##### 镜像命令

**docker images**  查看所有本地主机上的镜像

```shell
docker images
# 1. 可选项
-a, --all				# 列出所有镜像
-f, --filter filter 	# 根据提供的条件过滤输出
	--fprmat string		Pretty-print images using a Go template
	--no trunc			Do not truncate output
-q, --quiet				# 只显示镜像id
```

**docker search**  搜索镜像

**官方仓库地址 :** https://hub.docker.com/

```shell
# 1. 搜索mysql的docker镜像
docker search mysql

# 可选项,通过搜索镜像过滤
--filter=STARS=1000		# 限定大于一千
docker search mysql --filter=STARS=1000
```

**docker pull** 下载镜像

```shell
# 下载镜像:	docker pull (images-name镜像名)[:tag]
docker pull mysql		# 不写tag下载最新版本
docker pull mysql:8.0	# 下载指定版本(要Docker Hub上有的版本)

```

**docker rmi** 删除镜像

```shell
# 删除指定的镜像
docker rmi -f IMAGE_ID				# IMAGE_ID 镜像ID
# 删除多个镜像
docker rmi -f IMAGE_ID IMAGE_ID		# 多个镜像用空格分隔开
# 删除全部镜像
docker rmi -f $(docker images -aq)	
```



##### 容器命令

**说明 : 有镜像才可以创建容器, Linux, 下载一个 centos 镜像为例**

```shell
docker pull centos
```

**新建容器并运行**

> **docker run [可选参数] images_name**

```shell
docker run [可选参数] images_name

# 参数说明
--name="Name" 		# 容器名字 tomcat1,tomcat2来区分
-d 					# 以后台运行
-it					# 以交互方式运行,进入容器查看内容
-p					# 指定容器端口 -p 8080:8080
	-p	# ip:主机端口:容器端口
	-p	# 主机端口:容器端口(常用
	-p	# 容器端口
	# 容器端口
-P					# 随机指定端口


# 测试,启动并进入容器
docker run it centos /bin/bash

# 退出容器到主机
exit
```

**运行Mysql容器**

```powershell
# 运行mysql容器
docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:8.0.16

# 映射容器服务的 3306 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3306 访问到 MySQL 的服务
-p 3306:3306
# 设置 MySQL 服务 root 用户的密码
MYSQL_ROOT_PASSWORD=root

# 查看mysql安装成功
mysql -h localhost -u root -p
```

**查看所有运行的容器**

```shell
docker ps

-a 		# 列出当前正在运行的容器 + 历史运行过的容器
-n=?	# 显示最近创建的容器
-q		# 只显示容器的编号

```

**退出容器**

```shell
exit			# 直接退出容器
Ctrl + P + Q	# 容器不停止退出
```

**删除容器**

```shell
docker rm 容器id		# 根据容器id删除容器,不能删除运行的容器
docker rm -f $(docker ps -aq)	# 删除全部容器
docker ps -a -q|xargs docker rm # 通过管道删除所有的容器
```

**启动和停止**

```shell
docker start 容器Id		# 启动容器
docker restart 容器Id		# 重启容器
docker stop 容器Id		# 停止容器
docker kill 容器Id		# 杀死容器服务
```



##### 常用命令

**后台启动容器**

```shell
# docker run -d 镜像名
docker run -d centos

# 问题docker ps，发现centos停止了
# 常见的坑: docker 容器使用后台运行，就必须要有要一个前台进程，docker发现没有应用，就会自动停止
# nginx， 容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

**查看日志**

```shell
# 查看所有的日志
docker logs -ft
-tf		# 显示日志
--tail number	# 显示当前显示数目
--timestamps	# 显示时间戳


# 查看限定条数日志
docker logs -ft --tail 10 容器id		# 查看容器最新10条日志

# 若没有日志可以 写一段shell脚本,每两秒打印一句
docker run -d centos /bin/sh -c "while true;do echo allende;sleep 2;done"
```

**查看容器进程信息**

```shell
# 查看容器的进程信息
docker top 容器id
```

**查看镜像的元数据**

```shell
# 查看容器内部信息
docker inspect 容器id
```

**进入当前正在运行的容器**

```shell
# 容器通常是使用后台方式运行,当修改配置是需要进入容器
# 进入容器后开启一个新的终端,可以在里面操作(常用)
docker exec -it 容器id bashShell 		# bashShell ==> /bin/bash

# 进入容器正在执行的终端,不会启动新的进程
docker attach 容器id 				
```

**从容器内拷贝文件到主机上**

```shell
docker cp 容器id:容器内路径 目的的主机路径

# 进入docker容器内部
docker attach 容器id

# 将文件allen.java拷贝到主机home上
docker cp b78453025116:home/allen.java /home

```



##### 命令大全

![avatar](https://gitee.com/wsallende/EV2020/raw/master/static/img/docker-commands-diagram.png)

**命令大全**

```shell
attach      Attach to a running container						#当前shell下 attach连接指定运行的镜像
build       Build an image from a Dockerfile 					# 通过Dockerfile定制镜像
commit      Create a new image from a container's changes 		#提交当前容器为新的镜像
cp          Copy files/folders between a container and the local filesystem #拷贝文件
create      Create a new container 								#创建一个新的容器
diff        Inspect changes to files or directories on a container's filesystem #查看docker容器的变化
events      Get real time events from the server 				# 从服务获取容器实时时间
exec        Run a command in a running container 				# 在运行中的容器上运行命令
export      Export a container's filesystem as a tar archive 	# 导出容器文件系统作为一个tar归档文件[对应import]
history     Show the history of an image 						# 展示一个镜像形成历史
images      List images 										# 列出系统当前的镜像
import      Import the contents from a tarball to create a filesystem image #从tar包中导入内容创建一个文件系统镜像
info        Display system-wide information 					# 显示全系统信息
inspect     Return low-level information on Docker objects 		# 查看容器详细信息
kill        Kill one or more running containers 				# kill指定docker容器
load        Load an image from a tar archive or STDIN 			# 从一个tar包或标准输入中加载一个镜像[对应save]
login       Log in to a Docker registry 						# 注册或登录一个docker源服务器
logout      Log out from a Docker registry						# 从当前 docker registry 退出
logs        Fetch the logs of a container						# 输出当前容器日志消息
pause       Pause all processes within one or more containers	# 暂停一个或者多个容器
port        List port mappings or a specific mapping for the container# 查看映射端口对应的容器内部源端口
ps          List containers										# 列出容器列表
pull        Pull an image or a repository from a registry		# 从docker镜像源服务器拉取指定镜像或者库镜像
push        Push an image or a repository to a registry			# 推送指定镜像或者库镜像至docker源服务器
rename      Rename a container									# 重命名容器
restart     Restart one or more containers						# 重启运行的镜像
rm          Remove one or more containers 						# 移除一个或者多个容器
rmi         Remove one or more images 							# 移除一个或者多个镜像
run         Run a command in a new container 					# 创建一个新的容器并运行一个命令
save        Save one or more images to a tar archive 			# 保存一个镜像为一个tar包(对应load)
search      Search the Docker Hub for images					# 在docker hub 搜索镜像
start       Start one or more stopped containers				# 启动一个或者多个容器
stats       Display a live stream of container(s) resource usage statistics# 显示容器运行状态
stop        Stop one or more running containers 				# 停止一个或者多个容器
tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE # 给源中镜像打标签
top         Display the running processes of a container		# 查看容器中运行的进程信息
unpause     Unpause all processes within one or more containers	#取消暂停容器
update      Update configuration of one or more containers		# 更新一个或者多个容器
version     Show the Docker version information					# 查看 docker 版本
wait        Block until one or more containers stop, then print their exit codes# 截取容器停止时的退出状态
```



### 部署环境

##### 部署nginx

```shell
# 搜索镜像
docker search nginx
# 下载指定版本镜像
docker pull nginx:1.19
# 启动镜像(以3344端口后台启动nginx)
docker run -d --name nginx01 3344:80 nginx
# 查看docker进程
docker ps
# 本机自测
curl localhost:3344
[root@butterfly ~]# curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

# 进入容器
[root@butterfly ~]# docker exec -it nginx01 /bin/bash
root@36eea8b43795:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@36eea8b43795:/# cd /etc/nginx
root@36eea8b43795:/etc/nginx# ls
conf.d  fastcgi_params  koi-utf  koi-win  mime.types  modules  nginx.conf  scgi_params  uwsgi_params  win-utf

# 退出容器
exit
# 
```



##### 部署tomcat

```shell
# 用完及删除(用来测试)
docker run -it --rm tomcat:9.0

# 下载
docker pull tomcat:9.
# 启动
docker run -d 3355:8080 --name tomecat01 tomecat
```

##### 部署mysql

```powershell
# 获取指定版本的mysql
docker pull mysql:8.0.16
# 运行mysql容器
docker run -itd --name mysql-test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:8.0.16
# 进入容器
docker exec -it mysql bash

#登录mysql
mysql -u root -p
```

##### 部署redis

```powershell
# 搜索nacos镜像

# 下载镜像

# 查看本地镜像，看看拉到本地没有
docker images
# 启动镜像

# 验证是否成功

```

##### 部署nacos:

##### [nacos官方文档](https://link.segmentfault.com/?url=https%3A%2F%2Fnacos.io%2Fzh-cn%2Fdocs%2Fquick-start-docker.html)

 ```powershell
# 搜索nacos镜像
docker search nacos
# 下载镜像
docker pull nacos/nacos-server
# 查看本地镜像，看看拉到本地没有
docker images
# 启动镜像
docker run -d -p 8848:8848 --env MODE=standalone  --name nacos  nacos/nacos-server
# 验证是否成功
# http://localhost:8848/nacos 默认用户名密码都是：nacos

# nacos停止后重新启动 容器ID
docker start [container ID]
 ```










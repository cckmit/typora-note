# docker的使用

### 1.安装docker

卸载旧版本

```shell
yum remove docker
```

安装需要的安装包

```shell
yum install -y yum utils
```

设置镜像仓库

```shell
yum-config-manager  #默认是国外的镜像

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #阿里云镜像(推荐)
```

更新软件包索引

```shell
yum makecache fast
```

安装docker  docker-ce 社区版 docker-ee企业版

```shell
yum install docker-ce docker-ce-cli containerd.io
```

启动docker

```shell
systemctl start docker
```

测试docker：helloworld

```shell
docker run hello-world
```

查看是否安装成功

```shell
docker version
```

删除docker镜像命令

```shell
docker rmi -f imageid
```

### 2.docker基本命令

#### 容器常用命令

以下命令使用 ubuntu 镜像启动一个容器，参数为以命令行模式进入该容器：

```shell
$ docker run -it ubuntu /bin/bash
```

查看所有的容器命令如下：

```shell
$ docker ps -a
```

使用 docker start 启动一个已停止的容器：

```shell
$ docker start b750bbbcfd88 #containerid
```

停止容器的命令如下：

```shell
$ docker stop <容器 ID>
```

停止的容器可以通过 docker restart 重启：

```shell
$ docker restart <容器 ID>
```

进入容器命令：

```shell
docker exec -it 243c32535da7 /bin/bash
```

删除容器命令：

```shell
docker rm -f containerid
```

#### 镜像常用命令

查找镜像

```shell
 docker search httpd
```

拉取镜像

```shell
docker pull httpd
```

下载完成后，我们就可以运行这个镜像了。

```shell
docker run httpd
```

镜像删除使用 **docker rmi** 命令，比如我们删除 hello-world 镜像：

```shell
$ docker rmi hello-world #imageid
```



### 3.部署tomcat

获取tomcat镜像

```shell
#直接启动
docker run -it tomcat:9.0

#官方定义 --rm 即用完就删，通产用于测试
docker run -it --rm tomcat:9.0

#先下载，再手动启动
docker pull tomcat:9.0

#启动运行
docker run -d -p 8080:8080 --name tomcat01 tomcat

#进入tomcat容器
docker exec -it tomcat001 /bin/bash

#把webapps。dist目录的所有文件复制到webapps
cp -r webapps.dist/* webapps
```

运行效果图：

![1601215101247](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601215101247.png)

### 4.部署nignx

这里我们拉取官方的最新版本的镜像：

```shell
$ docker pull nginx:latest
```

使用以下命令来查看是否已安装了 nginx：

```shell
$ docker images
```

安装完成后，我们可以使用以下命令来运行 nginx 容器：

```shell
$ docker run -d --name nginx001 -p 3355:80 nginx #使用-d即后台运行
#-d 后台运行
#--name 给容器命名
#-p 宿主主机端口，内部端口
```

效果图：

![1601216677792](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601216677792.png)

### 5.容器数据卷的使用

容器的持久化合同步操作！容器间也可以数据共享

数据卷使用命令 -v

```shell
docker run -it -v /home/ceshi:/home centos /bin/bash
#-v 挂载，使用数据卷
#/home/ceshi 在home目录创建一个ceshi目录
#：/home 在容器内部的home目录下共享并同步数据
#centos  镜像名，若本地没有，则从网上拉取一个新的镜像centos
```

![1601218424451](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1601218424451.png)
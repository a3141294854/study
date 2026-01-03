# docker

## 基础概念

在镜像里配好环境，用容器运行，dockerfile，一种文本，用来自动化构建镜像

## 容器

1启动容器   docker run 镜像                             -d 后台运行容器 -it交互型 --rm容器停止自动删除，-it控制台进入容器(也可以后面docker exec -it 容器  /bin/sh 进入)   --restart always 自动重启

2查看容器  docker ps -a

3停止容器 docker stop 容器

4启动已停止的容器 docker start 容器

5映射端口和挂载卷(就是共享文件夹)  docker run -d -p 外部端口:内部端口   -v 外部文件夹:内部文件夹 镜像 

- **-P：**是容器内部端口**随机**映射到主机的端口。
- **-p：**是容器内部端口绑定到**指定**的主机端口。

 6命名卷挂载，让docker创建一个空间，我们用名字进行挂载   docker volume create 名字 

查询用 docker volume inspect 名字

## 镜像

1列出本地镜像，docker images

REPOSITORY：**表示镜像的仓库源

TAG：**镜像的标签

IMAGE ID：**镜像ID

CREATED：**镜像创建时间

SIZE：**镜像大小

2主动获取镜像 docker pull 镜像+版本

3查找镜像，一是去网址，二是docker search

4删除镜像,docker rmi 镜像名

5更新镜像，首先用镜像创建一个容器，apt-get update 更新系统，exit 退出 然后提交这个容器副本 docker commit -m="描述信息" -a="作者" 容器id 创建的镜像名字

6创建镜像，docker build -t 文件所在目录

7设置标签，docker tag 镜像id 镜像名:标签

## Dockerfile

1,FROM  一个基础镜像

2 WORKDIR /app 到工作目录，可以自己设置

3COPY  . .   复制代码文件，第一个点代码主机的当前目录，第二个点代表容器的当前目录，即前文的/app

4RUN 执行命令行命令，一般用来安装依赖

5EXPOSE说明自己暴露的端口

6 CMD []容器启动时的命令，自己加

docker build -t 名字 .(表示在当前文件夹)



```

```


镜像是类，容器是对象

// WorkFlow, shown in image "hello-world"

To generate this message, Docker took the following steps:

\1. The Docker client contacted the Docker daemon.

\2. The Docker daemon pulled the "hello-world" image from the Docker Hub.

  (amd64)

\3. The Docker daemon created a new container from that image which runs the

  executable that produces the output you are currently reading.

\4. The Docker daemon streamed that output to the Docker client, which sent it

  to your terminal.

// 检查版本

docker version

// 下载centos镜像

docker pull centos

// 启动镜像

docker run -d <image ID>  // -it 表示运行在交互模式，是-i -t的缩写，即-it是两个参数：-i和-t。前者表示打开并保持stdout，后者表示分配一个终端（pseudo-tty）一般这个模式就是可以启动bash，然后和容器有命令行的交互

// 提交容器保存为新的镜像

docker commit <container ID><new image name> 

// 发布镜像

docker push

// 启动容器

docker start

// 停止容器

docker stop 

// 重启容器

docker restart

// 启动后再进入一个容器

docker attach <container ID>

// 退出容器

exit 或 ctrl+d

// 退出容器，保持容器运行

ctrl+p q

 // 查看安装的镜像

docker images

// 查看运行的容器

docker ps

// 安装Vim

apt-get update

apt-get install vim
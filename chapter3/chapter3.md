# 第三章   镜像

> 镜像是容器的基石，容器是镜像的运行实例，有了镜像才能启动容器

## 3.1 镜像的内部结构

> 1. base镜像：基本的操作系统环境，用户可以根据需要安装和配置软件。
>
> base镜像的两层含义:(1).不依赖其他镜像 从scratch构建 （2).其他镜像可以以之为基础进行扩展，所以为各种linux发行版的docker镜像比如ubuntu、centos

> 普及一下linux最基本的知识:Linux 操作系统由内核空间和用户空间组成
>
> 内核空间是kernel linux刚启动会加载bootfs文件系统 之后bootfs会被卸载掉
>
> 用户空间的文件系统是rootfs 包含我们熟悉的/dev /proc /bin等目录
>
> 对于base镜像来说,底层直接用Host的kernel,自己只需要提供用户空间的文件系统rootfs就行了
>
> 而对于一个精简的os rootfs(用户空间的文件系统)可以很小，只需要包括最基本的命令，工具和程序库就可以了。

> 2.base镜像提供的是最小安装的Linux发行版 
>
> 不同linux发行版的区别主要就是rootfs 内核kernel差别不大

普及一个命令：查看内核版本 uname -r

> 3  镜像的分层结构
>
> Docker 支持通过扩展现有镜像 创建新的镜像

##  3.2 构建镜像

> 一Docker提供了两种构建镜像的方法 docker commit 命令与Dockerfileg偶见文件
>
> 1. docker commit 三个步骤  运行容器 修改容器 将容器保存为新的镜像
>    1.  运行容器 docker run -it ubuntu  -it 参数的作用是以交互模式进入容器

>       2 . 安装想要的软件

> 3. 保存为新镜像 
>    1. 查看当前运行容器 docker ps
>    2. docker commit 原容器的names名 新的名字

> 二Docker是一个文本文件，记录着镜像的构建过程
>
>     1. 第一个Dockerfile
>    
>         用dockerfile创建上节
>    
>         1.在某处文件夹中创建 touch Dockerfile 文本文件 
>    
>         2.修改文本文件 vim Dockerfiel  
>    
>        ​      FROM ubuntu
>    
>        ​      RUN apt-get update && apt-get install -y vim 保存退出
>    
>         3.docker build -t  镜像的新名字 -f  Dockerfile文件路径 

>    2.查看镜像分层结构
>
> ​      新建的镜像是通过在base镜像的顶部添加一个新的镜像层而得到的
>
> ​      docker history 镜像名字

> ​    3.镜像的缓存特性
>
> ​     Docker 会缓存已有镜像的镜像层  构建新镜像 如果某镜像层已经存在 就直接使用 无须重新创建
>
>    在别的Dockerfile文本文件中 添加一个COPY  testfile  构建镜像的过程中不使用缓存 --no-cache参数
>
>   Dockefile中每一个指令都会创建一个镜像层 上层依赖下层。无论什么时候 只要某一层发生改变 其上边的缓存		   都会失效

> 4. 调试Dockerfile 
>
>      总结一下通过Dockerfile构建镜像的过程:
>
>    ​	1.从base镜像运行一个容器
>
>    ​	2.执行一条指令 对容器修改
>
>    ​        3.执行docker commit的操作 生成一个新的镜像层
>
>    ​	4.Docker会基于刚刚提交的镜像 生成一个新的容器
>
>    ​	5.重复2~4 直到Dockerfile中所有的命令执行完毕	

>    	当Dockerfile 某一个构建步骤的失败 可以利用上一步的创建的镜像进行调试 接下来通过docker run -it 启动镜像的一个容器

>    5.Dockerfile常用指令
>
>  - FROM 指定base镜像
>  - MAINTAINER 设置镜像的作者 可以是任意字符串
>  - COPY 将文件从build context复制到镜像  支持两种形式COPY src dest 与COPY ['src','dest'] src 只能指定build context中的文件或目录
>  - ADD 将文件从build context复制到镜像 不同的是src是归档文件 文件会被自动解压到dest
>  - ENV 设置环境变量 环境变量可以被后面的指令使用
>  - EXPOSE 指定容器中的进程监听某个端口 DOCKER可以将该端口暴露出来 我们会在容器网络部分详细讨论
>  - VOLUME 将文件或者目录说明为volume 我们会在容器存储部分讨论
>  - WORKDIR 为后面的RUN CMD ENTRYPOINT ADD COPY指令设置镜像中的当前工作目录
>  - RUN 在容器中运行指定的命令
>  - CMD 容器启动时运行指定的命令 Dockerfile中可以有多个CMD指令，但只有最后一个生效 CMD可以被run之后的参数代替
>  - ENTRYPOINT 设置容器启动时运行的命令

> 注：Dockerfile 支持以 # 开头的注释

## 3.3 RUN vs CMD vs ENTRYPOINT

> RUN 执行命令并创建新的镜像层 RUN经常用于安装软件包
>
> CMD 设置容器启动后默认执行的命令及其参数 但CMD能够被docker run后面跟的命令行参数替换
>
> ENTRYPOINT设置容器启动时运行的命令

> Shell 和 Exec 格式
>
> shell格式 <instruction> <command>
>
> 例如：RUN apt-get install python3
>
> Exec格式<instruction>["executable","param1","param2"]
>
> 例如：RUN ["apt-get","install","python3"]

## 3.4 分发镜像

> 如何在多个Host上使用镜像
>
> 1.Dockerfile
>
> 2.共有 docker hub
>
> 3.搭建私有的Registry供本地使用

> docker build -t 镜像的名字 
>
> docker images 可以查看镜像的信息
>
> 实际上一个特定镜像的名字由两部分组成 registry 和 tag
>
> [image name] = [registry]:[tag]  如果执行docker build时没指定tag 会使用默认值latest 效果相当于
>
> docker build -t registrty:版本 tag


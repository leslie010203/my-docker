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

> Docker提供了两种构建镜像的方法 docker commit 命令与Dockerfileg偶见文件
>
> 1. docker commit 三个步骤  运行容器 修改容器 将容器保存为新的镜像
>    1.  运行容器 docker run -it ubuntu  -it 参数的作用是以交互模式进入容器

>    2 . 安装想要的软件

> 3. 保存为新镜像 
>    1. 查看当前运行容器 docker ps
>    2. docker commit 原容器的names名 新的名字


#  Chapter 4 容器

### - 构建镜像  通过镜像运行容器

### - 学习容器的各种操作 容器的各种状态 容器的底层技术

> ### 4.1 运行容器

> > docker run 是启动容器的方法 在讨论Dockerfile时我们已经学习到 可以三种方式容器运行时启动执行的命令
> >
> > 1. CMD 指令
> > 2. ENTRYPOINT指令
> > 3. 在docker run 命令行中指定

> 执行docker ps 或者docker container ls 可以查看Docker Host中正在运行的容器

> docker run -d ubuntu /bin/bash -c "while true ;do sleep1 ;done"

> 当没有容器显示的时候   docker ps -a 或者 docker container ls -a  [-a 会显示所有容器的运行状态]

> 4.1.1 让容器长期运行
>
> 容器的生命周期 依赖于启动时的执行的命令 只要该命令不结束 容器就不会推出
>
> 可以添加一个-d 以后台方式启动容器
>
> 容器启动的时候 可以用--name 为容器命名 如果不指定 docker会自动为容器命名

> 4.1.2进入容器的方法
>
> 我们需要到容器中做一些调试 有两种方法进入容器 attach 和 exec
>
> - attach  docker attach 可以attach 到启动容器的终端
>
>   注意：ctrl + p 然后ctrl + q推出attach 终端

> - exec 进入相同的容器docker exec -it  ID bash
>
>   - -it以交互的模式打开pseudo-TTY 执行bash 其结果就是打开一个bash终端
>
>   - 进入容器中容器的hostname就是器短ID
>
>   - 然后可以像操作Linux一样操作容器
>
>   - 执行exit推出容器 回到docker host
>
>     docker exec  -it <container> bash | sh 这是exec最常用的方式

> - attach vs exec 
>
>   - attach 直接进入容器启动命令的终端 不会启动新的进程
>
>   - exec 则是在容器中打开新的终端 并且可以启动新的进程
>
>   - 如果想直接在终端中查看启动命令的输出 用attach 其他情况使用exec
>
>     如果只为了查看启动命令的输出 可以使用docker log -f   [-f 的作用时能够持续打印]

> ## 4.2 Stop /start /restart

> docker stop 可以停止运行的容器
>
> docker stop 可以停止运行的容器
>
> 容器在host中运行其实是一个进程 docker stop 发送SIGTERM信号 如果快速停止容器 docker kill其作用是向进程发送SIGKILL信号

> 停止的容器 可以通过start重新启动 docker start 会保留容器的第一次启动时的所有参数
>
> docker 可能会因某种错误而停止运行 对于服务类的 我们希望能够自动重启 启动的时候设置--restart 就可以达到这个效果
>
> --restart=always意味着无论容器因何种原因退出 都立即重启 包括正常退出
>
> --restart=on-failure:3 意思是如果启动进程退出代码非0 则重启容器最多重启3次

> ### 4.3 pause / unpause容器
>
> 如果对容器的文件打个快照 或者宿主机需要使用cpu 这时可以执行docker pause
>
> 知道 docker unpause 回复运行docker  重新占用cpu 

> ## 4.4 删除容器
>
> docker rm Id 直接可以删除 
>
> 提醒: docker rm 删除容器 
>
> ​          docker rmi 删除镜像

> ## 4.5 State Machine

![运行状态](E:\github-repo\my-docker\imgs\chapter4-02.png)

> ## 4.6资源限制
>
> docker -m 或者 --memory 设置内存的使用限额
>
> docker --memory-swap 设置内存+swap 的使用限额 默认为 -m的两倍

下面我们将使用progrium/stress镜像来学习如何为容器分配内存 该镜像可用于对容器执行压力测试 执行如下命令

docker run -it -m 200M --memory-swap=300M progrium/stress --vm 1 --vm-bytes 280M

> 默认设置下所有容器平等的使用CPU资源并灭有限制
>
> Docker 可以使用-c 或者 --cpu-shares设置容器使用cpu的使用权重
>
> docker run --name     -c 1024
>
> docker run --name     -c 512
>
> 上面就是下面的2倍

> Block io 磁盘的读写 docker可以设置权重 限制bps和iops的方式控制容器的读写磁盘的带宽
>
> docker run -it --name --blkio-weight
>
> 限制bps [bytes per seconds  每秒读写的数据量]
>
> 限制iops[io per seconds 每秒IO的次数]
>
> --device-read-bps
>
> --device-write-bps
>
> --device-read-iops
>
> --device-write-iops
>
> 例子: docker run -it -name --device-write-bps /dev/sda:30M  ubuntu

> ##  4.7 容器底层技术

> 为了更好的理解容器的特性 我们将讨论容器的底层实现技术
>
> cgroup 和 namespace是最重要的技术 
>
> cgroup 实现资源限额
>
> namespace 实现资源隔离

> cgroup 全称 Control Group  linux 通过cgroup设置使用进程CPU 内存和IO

> ## 4.8 总结

> - create 创建容器
> - run 运行容器
> - pause 暂停容器
> - unpause 取消暂停 继续运行容器
> - stop 发送sigterm停止容器
> - kill 发送sigkill快速停止容器
> - start 启动容器
> - restart 重启容器
> - attach  attach到容器的启动进程的终端
> - exec 在容器中启动新的进程 通常使用-it参数
> - logs 显示容器启动进程的控制台输出 用-f
> - rm 从磁盘中删除容器


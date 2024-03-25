## Docker命令大全
### docker镜像命令
~~~sh
# 搜索镜像：docker search + 镜像名字
$ docker search nginx

# 从registry拉取镜像：docker pull + 镜像名字:版本号
$ docker pull nginx:latest

# 从registry仓库提交镜像：docker push + 仓库名:标签
$ docker push repro1:v1.0

# 查看本地镜像: docker images
$ docker images

# 使用Dockerfile创建镜像: docker build + 目录，.代表当前目录，-t表示加标签
$ docker build -t mynginx:1.0 .

# 删除一个或多个镜像: docker rmi + 镜像1 + 镜像2
$ docker rmi mynginx:1.0 mynginx:2.0

# 删除未标记或未用过的镜像
$ docker image prune

# 删除未使用过的镜像
$ docker image prune -a

# 给镜像加标记： docker tag 镜像标签 新镜像标签名
$ docker tag mynginx:1.0 nginx1

# 把镜像保存为.tar文件: docker save 镜像 > 文件
$ docker save mynginx:1.0 > mynginx_v1.tar

# 从.tar文件载入镜像: docker load -i .tar文件
$ docker load -i mynginx_v1.tar

# 根据容器创建镜像：docker commit 容器名 镜像名
$ docker commit 
~~~
### docker容器命令
~~~sh
# 创建容器: docker create + 选项(-i, -t, -d, -p, -v, -e) + 镜像
$ docker create --name mynginx_1 -it -p 8080:80 mynginx:1.0

# 各选项含义
# -i:以交互模式运行容器，通常与-t 同时使用；
# -d:后台运行容器，并返回容器ID；
# -p:端口隐射, 宿主机在前，容器在后
# -P:随机映射宿主机端口
# -t:为容器重新分配一个伪输入终端，通常与-i 同时使用；
# -v:目录挂载
# --entrypoint: 指定进入点
# --restart=always: 服务重启

# 启动容器：docker start + 容器名
$ docker start mynginx_1

# 创建 + 运行容器: docker run + 选项 + 镜像 + 命令
$ docker run --name mynginx_1 -it -p 8080:80 mynginx:1.0
$ docker run -it ubuntu /bin/bash

# 查看正在运行中的容器：docker ps
$ docker ps

# 查看所有容器，包括停止运行的容器: docker ps -a
$ docker ps -a

# 停止一个正在运行的容器: docker stop 容器
$ docker stop mynginx_1

# 重启容器：docker restart + 容器名
$ docker restart mynginx_1

# 容器重命名：docker rename 老名字 新名字
$ docker rename mynginx_1 mynginx_2

# 删除一个容器：docker rm 容器名
$ docker rm mynginx_1

# 强制删除一个正在运行的容器：docker rm -f 容器名
$ docker rm -f mynginx_1

# 删除已停止运行的所有容器: docker container prune
$ docker container prune

# 拷贝文件，从容器到宿主机：docker cp 容器名:容器内路径 宿主机文件路径
$ docker cp myweb_1:/index.html index.html

# 拷贝文件，从宿主机到容器：docker cp 宿主机文件路径 容器名:容器内路径
$ docker cp index.html myweb_1:/index.html 

# 进入运行的容器，执行命令: docker exec + 选项 + 容器名 + 命令 + 参数
# 推荐大家使用 docker exec命令，使用此命令即使exit容器终端，也不会导致容器的停止
$ docker exec -it mynginx_1 /bin/bash
$ docker exec -it mynginx_1 /bin/bash start.sh

# 查看容器端口映射：docker port 容器名
$ docker port mynginx_1

# 查看容器内已修改文件：docker diff 容器名
$ docker diff mynginx_1

# 查看容器日志：docker logs + 容器名
$ docker logs web

# 查看容器内运行进程：docker top + 容器名
$ docker top web

# 查看容器的底层信息：docker inspect + 容器名
$ docker inspect web

# 利用inspect命令查看容器的IP地址
$ docker inspect web | grep "IPAddress"

# 查看运行容器的统计数据：docker stats
$ docker stats
~~~
#### docker ps 详细参数
~~~sh
-a :显示所有的容器，包括未运行的。

-f :根据条件过滤显示的内容。

--format :指定返回值的模板文件。

-l :显示最近创建的容器。

-n :列出最近创建的n个容器。

--no-trunc :不截断输出。

-q :静默模式，只显示容器编号。

-s :显示总的文件大小。
~~~
#### docker images 详细参数
~~~sh
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；

--digests :显示镜像的摘要信息；

-f :显示满足条件的镜像；

--format :指定返回值的模板文件；

--no-trunc :显示完整的镜像信息；

-q :只显示镜像ID。
~~~
#### docker删除
- docker rm 删除容器
  >docker rm 详细参数
  >~~~sh
  >-f : 强制删除一个运行中的容器
  >-l : 移除容器间的网络连接，而非容器本身
  >-v : -v 删除与容器关联的卷
  >~~~
- docker rmi 删除镜像
  >docker rmi 详细参数
  >~~~sh
  >-f : 强制删除；
  >--no-prune : 不移除该镜像的过程镜像，默认移除
  >~~~

  >~~~sh
  ># 删除镜像tag为latest的且是laladder/self-use仓库的
  >docker rmi laladder/self-use:latest
  >~~~
#### docker exec 详细参数
~~~sh
-d :分离模式: 在后台运行

-i :即使没有附加也保持STDIN 打开

-t :分配一个伪终端

# 实例
docker exec -it nginx /bin/bash
~~~
#### docker run 详细参数
~~~sh
-i, --interactive=false   打开STDIN，用于控制台交互
-t, --tty=false            分配tty设备，该可以支持终端登录，默认为false
-d, --detach=false         指定容器运行于前台还是后台，默认为false
-u, --user=""              指定容器的用户
-a, --attach=[]            登录容器（必须是以docker run -d启动的容器）
-w, --workdir=""           指定容器的工作目录
-c, --cpu-shares=0        设置容器CPU权重，在CPU共享场景使用
-e, --env=[]               指定环境变量，容器中可以使用该环境变量
-m, --memory=""            指定容器的内存上限
-P, --publish-all=false    指定容器暴露的端口
-p, --publish=[]           指定容器暴露的端口
-h, --hostname=""          指定容器的主机名
-v, --volume=[]            给容器挂载存储卷，挂载到容器的某个目录    顺序：主机：容器
--volumes-from=[]          给容器挂载其他容器上的卷，挂载到容器的某个目录
--cap-add=[]               添加权限，权限清单详见：http://linux.die.net/man/7/capabilities
--cap-drop=[]              删除权限，权限清单详见：http://linux.die.net/man/7/capabilities
--cidfile=""               运行容器后，在指定文件中写入容器PID值，一种典型的监控系统用法
--cpuset=""                设置容器可以使用哪些CPU，此参数可以用来容器独占CPU
--device=[]                添加主机设备给容器，相当于设备直通
--dns=[]                   指定容器的dns服务器
--dns-search=[]            指定容器的dns搜索域名，写入到容器的/etc/resolv.conf文件
--entrypoint=""            覆盖image的入口点
--env-file=[]              指定环境变量文件，文件格式为每行一个环境变量
--expose=[]                指定容器暴露的端口，即修改镜像的暴露端口
--link=[]                  指定容器间的关联，使用其他容器的IP、env等信息
--lxc-conf=[]              指定容器的配置文件，只有在指定--exec-driver=lxc时使用
--name=""                  指定容器名字，后续可以通过名字进行容器管理，links特性需要使用名字
--net="bridge"             容器网络设置:
                            bridge 使用docker daemon指定的网桥
                            host    //容器使用主机的网络
                            container:NAME_or_ID  >//使用其他容器的网路，共享IP和PORT等网络资源
                            none 容器使用自己的网络（类似--net=bridge），但是不进行配置
--privileged=false         指定容器是否为特权容器，特权容器拥有所有的capabilities
--restart="no"             指定容器停止后的重启策略:
                            no：容器退出时不重启
                            on-failure：容器故障退出（返回值非零）时重启
                            always：容器退出时总是重启
--rm=false                 指定容器停止后自动删除容器(不支持以docker run -d启动的容器)
--sig-proxy=true           设置由代理接受并处理信号，但是SIGCHLD、SIGSTOP和SIGKILL不能被代理
~~~
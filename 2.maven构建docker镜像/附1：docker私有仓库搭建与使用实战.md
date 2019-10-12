## docker私有仓库搭建与使用实战

原创： 程序员欣宸 [程序员欣宸](javascript:void(0);) *9月24日*

> 亲测有效.

hub.docker.com上可以保存镜像，但是网速相对较慢，在内部环境中搭建一个私有的公共仓库是个更好的方案，今天我们就来实战搭建私有docker仓库吧；

### 环境规划

需要两台机器：docker私服仓库的server和使用docker的普通机器，这两个机器都是ubuntu16版本的server，ip信息如下：

| 机器名          | ip              | 功能                       |
| --------------- | --------------- | -------------------------- |
| docker-registry | 192.168.119.148 | docker私有仓库服务器       |
| docker-app      | 192.168.119.155 | 运行docker服务的普通服务器 |

### 准备机器

本次实战中，上述两台机器是vmware上创建的两个虚拟机，都安装了docker服务，记得关闭防火墙，并且在vmware中给两个镜像把名字分别改成“docker-registry”和“docker-app”，以免后面搞错了；

虚拟机启动后，请先修改/etc/hostname文件，将两个机器的hostname分别修改成“docker-registry”和“docker-app”,然后用reboot命令重启；

### 安装私有仓库

- 登录docker-registry机器(推荐使用SecureCRT);
- 执行以下命令，会启动一个registry容器，该容器用于提供私有仓库的服务：

```
docker run --name docker-registry -d -p 5000:5000 registry
```

- 执行docker ps命令看一下容器情况，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0vovyyEtt9S4jfElDhcUtic8icFqKuiawl3LCderplEeeicCJ65jqO1Ct5A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)容器正常启动，对外提供服务通过5000端口映射到docker-registry的5000端口；
- 执行命令curl -X GET http://127.0.0.1:5000/v2/_catalog，收到的响应如下，是个json对象，其中repositories对应的值是空的json数组，表示目前仓库里还没有镜像：

```
{"repositories":[]}
```

OK，私有仓库已经创建和启动完毕了，接下来试试如何使用吧；

### 支持http协议推送

正常情况下，应用服务器推送镜像到仓库用的是https，此处我们通过命令行来测试推送用的是普通的http，所以需要修改docker的启动参数，使之允许以http协议工作；

- 执行推送镜像的机器是docker-app，所以登录到此机器(推荐使用SecureCRT);
- 修改/etc/default/docker文件，加入以下红框内容：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0uStF4h94fKMOIcFuzeWPplmT0KlYE96I55C2x0lOgzLGytAWs3Ozcg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 再修改 /lib/systemd/system/docker.service，以下红框中的内容，第一行为新增，第二行为修改：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0wXQGEvRk4aJFY9L5u1k6IoYPtLYJztzJr155LY0Vw859bJ2XOg4bKw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 执行以下命令，重新加载配置信息再重启docker服务：

```
systemctl daemon-reload;service docker restart
```

### 推送镜像到私有仓库

- 接下来我们在docker-app先下载一个镜像，再将这个镜像推送到私有仓库中去；
- 登录docker-app机器(推荐使用SecureCRT);
- 执行命令docker pull tomcat，从hub.docker.com下载最新版本的tomcat镜像，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0I2Xfqpw1ibicgRL37ssN7g6YDwB9R9JycicG7WzPBOYTX8mvKmoQcjmcw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 下载完毕后，执行docker images查看镜像的信息，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0zsGCuJxHDlq4rPgicOvXLQ6XicsG1kWy6FzQfEM9Stukmvrlu7GeCudQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 如上图红框所示，这个镜像的ID是3dcfe809147d，所以我们执行以下命令，给这个镜像添加一个带有私有仓库IP的TAG，这样后面才能成功推送到私有仓库：

```
docker tag 3dcfe809147d 192.168.119.148:5000/tomcat
```

- 再执行docker images查看镜像的信息，如下图，出现了一个新的镜像，REPOSITORY是192.168.119.148:5000/tomcat：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0usJ2ou4d1d9MKr9eW7HicRqJ0p6T8whjmu0pBZognZ6o1xPStkHmv1g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 执行以下命令进行推送：

```
docker push 192.168.119.148:5000/tomcat
```

可以看到顺利进行中，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0ZAqsgeQp38cOwFgqMS4ugMt5xQpqIsTBzwgcgNMDOe8PYedD208nkQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- 推送成功后，在docker-app和docker-registry上分别执行curl -X GET http://192.168.119.148:5000/v2/_catalog，查看私有仓库的镜像信息，都能见到如下内容：

![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0Zx9p5p9DImoOArz86jl2d4Ec5iaicibltm2RXZ4418BfwIwxDSOEJ3hyA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)这里写图片描述

### 使用私有仓库的镜像

- 在docker-app机器上，先执行以下命令将本地镜像删掉：

```
docker rmi 192.168.119.148:5000/tomcat tomcat
```

- 再执行以下命令，用私服上的镜像来创建一个容器，映射8080端口：

```
docker run --name tomcat001 -p 8080:8080 -idt 192.168.119.148:5000/tomcat
```

- 本地没有镜像就去私服下载，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct0hK7zYPxicDMRPGRI1LYGibZH8nNJpnGaroibVYEiaZVia82tibJiavQbGooAw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- docker-app的IP是192.168.119.155，所以在当前电脑上打开浏览器，输入：192.168.119.155:8080，可以看到下图熟悉的tomcat欢迎页：

![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolwRwklnaSMHwCOIlh2toct00WTVcsAA4JFxpCU4fp1DlNh41HblHrl624A3yQkl2caP6RMb7hZheQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)这里写图片描述

至此本次实战就结束了，希望能对您的私有仓库搭建有所帮助。
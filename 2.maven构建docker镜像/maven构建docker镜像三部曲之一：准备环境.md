## maven构建docker镜像三部曲之一：准备环境

原创： 程序员欣宸 [程序员欣宸](javascript:void(0);) *2019-09-25*

> 本人已在本地亲测,可用.

### 更简单的部署

之前的实战中，如果要在docker环境中运行java的web工程，通常先运行一个支持在线部署的tomcat容器，然后通过maven的tomcat7-maven-plugin插件把工程在线部署到tomcat中，有没有更简便的方法呢？有，利用docker-maven-plugin插件不但能将工程构建成镜像，还能将此镜像推送到镜像仓库中去，从本章开始，我们就通过实战来熟悉这个插件吧；

### 环境信息

本次实战是在win10环境下，在"VMware Workstation 14 Player"这个虚拟机工具下运行ubuntu16 server的虚拟机，在此虚拟机上完成本次实战的；

### 实战步骤总览

整体上分为以下三步，分三篇文章完成：

1. 准备环境；(即本章)
2. 开发spring boot的web工程，构建镜像；
3. 将镜像推送到局域网的docker私服，以及阿里云的私服上去；

本章我们的任务是将环境准备好，接下来就开始吧；

### 准备操作系统和docker

1. 操作系统：Ubuntu 16
2. Docker：19.03.2

准备完成后，请用SecureCRT登录上去，为了后面的操作方便，请使用root账号；

### ubuntu安装jdk8

- 执行以下命令添加ppa:

```
add-apt-repository ppa:webupd8team/java
```

会见到下图的信息，此时直接按回车键继续：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZT2CTtQCplD03yx03HEszHs4dIbKjYcmicn4pbjXXEw7tw4lzcD7LIxwA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

- apt更新：

```
apt-get update
```

- 执行以下命令开始安装jdk8：

```
apt-get install oracle-java8-installer
```

稍后会弹出如下信息，按回车继续：

![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZTWG0T0UAiaUn4gFtmU61wh2q3nACJvlAGYPxV2jz2LVULHkTfib0oA0SQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

然后会弹出如下信息，选择"Yes"，然后回车继续：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZT6atvgTEcEeaBYmqZfZMDsn0GJOibmlwOvbhpzKG0yo6HjNkz8ibGW8TQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

漫长的等待后安装成功，执行java -version看到信息如下，jdk8安装成功：

![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZTEHuzzVUia6SdWjRz6xdQhNFoUvzZakbTMTSYf63wk4xiaDGMicaC9R3mA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)这里写图片描述

### ubuntu安装maven

- 去maven官网下载maven安装包，例如apache-maven-3.5.2-bin.tar.gz；
- 用SecureCRT的SFTP功能将maven安装文件从win10系统上传到虚拟机中，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZTzB1u7icewy0QK3Cgzk4xHrU6JqOIFAibRibRibKhBf2uGFbvbJlr062j0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
- 将maven安装文件解压后，整个目录复制到/opt目录下，复制后的路径是：/opt/apache-maven-3.5.2；
- 执行cd /bin来到/bin目录下；
- 执行以下命令创建软链接：

```
    ln -s /opt/apache-maven-3.5.2/bin/mvn mvn
```

- 编辑/etc/profile文件，在末尾新增以下两行：

```
export M2_HOME=/opt/apache-maven-3.5.2
export PATH=${M2_HOME}/bin:$PATH
```

编辑完保存推出，执行source /etc/profile或者关闭窗口重新连接登录，都能使刚才的配置生效；

- 执行mvn -version，看到的信息如下图，说明jdk和maven都安装成功了：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolygC0UTEqjgicicKB1McDqXZTlLJqbrEelZxkw8jzTyLxhWuG8UTKtsUThaIydII3B5t9h1jlSaOx2w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

以上就是我们是实战前的准备工作，在下一章我们开发出spring boot的web应用，再打包成本地docker环境的镜像；
## maven构建docker镜像三部曲之二：编码和构建镜像

原创： 程序员欣宸 [程序员欣宸](javascript:void(0);) *9月26日*

> 本人已在本地亲测,可用.

在《[maven构建docker镜像三部曲之一：准备环境](http://mp.weixin.qq.com/s?__biz=Mzg5MDIyODcyOQ==&mid=2247483942&idx=1&sn=a8fae44fc7a071f8b753a1e31621fe31&chksm=cfde98b0f8a911a6f280103492e0911f4bde73271f8284f1f4c99851980e072b080d7b9570de&scene=21#wechat_redirect)》中，我们了ubuntu16机器，并且装好了docker、jdk8、maven等必备工具，现在我们来开发一个java web工程，再用docker-maven-plugin插件来构建本地的docker镜像；

### web工程

我们采用spring boot的web工程作为实战的应用，这样的好处是简单快速的创建和部署项目，这只是个最简单的、基于maven构建的spring boot web工程，源码我已经上传到github上，地址是：https://github.com/zq2599/blog_demos ，这里面有多个工程，本次实战用到的是mavendockerplugindemo，如下图红框所示：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYfOtwzmpMDB4schqG5QKafm5wLdEClDRib4bgIy1fEJMqYpRDsNENckw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### java代码

mavendockerplugindemo工程的代码非常简单，只有一个controller，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYmzpvLgL4uphypcDPUTWZBcdx1JibnQv6HCbTFxWQkSgNE88af4sbfJQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### pom.xml

整个工程的pom.xml也很简单，依赖spring-boot-starter-web，构建的时候使用spring-boot-maven-plugin插件，如下：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.bolingcavalry</groupId>
	<artifactId>mavendockerplugindemo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>mavendockerplugindemo</name>
	<description>maven docker plugin demo</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/>
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

正常情况下，工程运行起来后在浏览器访问http://x.x.x.x:8080，就会显示如下信息(x.x.x.x代表执行工程的机器ip)：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYoJmg1OHDaANe3rrhCiafPc7Nq7XRMhkrGsSWTiawPibCIBRJBoPHepk4g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 将工程复制到Ubuntu上

在上一章在《[maven构建docker镜像三部曲之一：准备环境](http://mp.weixin.qq.com/s?__biz=Mzg5MDIyODcyOQ==&mid=2247483942&idx=1&sn=a8fae44fc7a071f8b753a1e31621fe31&chksm=cfde98b0f8a911a6f280103492e0911f4bde73271f8284f1f4c99851980e072b080d7b9570de&scene=21#wechat_redirect)》我们将Ubuntu的docker、jdk、maven等环境都准备好了，现在将spring boot工程放到Ubuntu上，然后就能用maven来构建了；

1. 在windows电脑上，我将工程压缩成mavendockerplugindemo.zip文件，存放在D:\blog目录下；
2. 在Ubuntu新建目录/usr/local/work；
3. 在Ubuntu执行命令apt-get install -y unzip，安装解压工具；
4. 复制文件，推荐使用SecureCRT的SFTP工具，先用SecureCRT登录虚拟机，再建立SFTP连接，如下图红框所示：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYaaYicPTsYzBVAYX57erqdfndxYIcavUyOZG6ftVEU7w7ebUCZC9ibPag/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
5. 在SFTP窗口执行以下命令：

```
#进入本机的d:/blog/目录
lcd d:/blog/
#进入linux的/usr/local/work/目录
cd /usr/local/work/
#将本机d:/blog/目录下的mavendockerplugindemo.zip文件上传到linux的/usr/local/work/目录下
put mavendockerplugindemo.zip
```

这样就能将文件从windows电脑传到Ubuntu上，如果想把Ubuntu上的xxx文件下载到windows电脑，执行get xxx命令即可； 

\6. 关闭SFTP窗口，在ssh窗口进入/usr/local/work/，执行命令unzip mavendockerplugindemo.zip将工程解压缩； 

\7. 请注意：接下来的操作都在Ubuntu上进行；

### 镜像构建方式

docker-maven-plugin插件构建docker镜像有两种方式：

1. 指定参数，由docker-maven-plugin插件根据这些参数来制作镜像；
2. 指定Dockerfile，这和我们用docker build命令来构建镜像的过程一样，不过docker-maven-plugin帮我们把工程构建和镜像构建两件事串起来了；

接下来我们将上述两种方式都实践一下；

**第一种构建方式**：

通过参数构建 在mavendockerplugindemo工程目录下新建文件pom_1_by_param.xml，内容和pom.xml一样，然后我们再去<plugins>节点添加以下内容，放在原有的<plugin>节点后面，如下所示：

```
<plugins>
			<!--这是原有的spring boot插件-->
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<!--新增的docker maven插件-->
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>0.4.12</version>
				<!--docker镜像相关的配置信息-->
				<configuration>
					<!--镜像名，这里用工程名-->
					<imageName>${project.artifactId}</imageName>
					<!--TAG,这里用工程版本号-->
					<imageTags>
						<imageTag>${project.version}</imageTag>
					</imageTags>
					<!--镜像的FROM，使用java官方镜像-->
					<baseImage>java:8u111-jdk</baseImage>
					<!--该镜像的容器启动后，直接运行spring boot工程-->
					<entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
					<!--构建镜像的配置信息-->
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
		</plugins>
```

上面的每个参数都已加了注释，就不多说了，在此文件所在目录执行以下命令，指定pom_1_by_param.xml作为pom文件执行maven构建：

```
mvn -f pom_1_by_param.xml clean package -DskipTests docker:build
```

执行成功后输出以下信息：

```
[INFO] --- maven-jar-plugin:2.6:jar (default-jar) @ mavendockerplugindemo ---
[INFO] Building jar: /usr/local/work/mavendockerplugindemo/target/mavendockerplugindemo-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.5.9.RELEASE:repackage (default) @ mavendockerplugindemo ---
[INFO]
[INFO] --- docker-maven-plugin:0.4.12:build (default-cli) @ mavendockerplugindemo ---
[INFO] Copying /usr/local/work/mavendockerplugindemo/target/mavendockerplugindemo-0.0.1-SNAPSHOT.jar -> /usr/local/work/mavendockerplugindemo/target/docker/mavendockerplugindemo-0.0.1-SNAPSHOT.jar
[INFO] Building image mavendockerplugindemo
Step 1/3 : FROM java:8u111-jdk
 ---> d23bdf5b1b1b
Step 2/3 : ADD /mavendockerplugindemo-0.0.1-SNAPSHOT.jar //
 ---> 74f201b46c92
Removing intermediate container cbc9e456d139
Step 3/3 : ENTRYPOINT java -jar /mavendockerplugindemo-0.0.1-SNAPSHOT.jar
 ---> Running in 256a09be033d
 ---> ad342e51021e
Removing intermediate container 256a09be033d
Successfully built ad342e51021e
[INFO] Built mavendockerplugindemo
[INFO] Tagging mavendockerplugindemo with 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 11.617 s
[INFO] Finished at: 2017-12-23T00:00:06-08:00
[INFO] Final Memory: 35M/84M
[INFO] ------------------------------------------------------------------------
```

执行docker images命令可以看到如下信息，新的镜像已经创建好了：

```
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
mavendockerplugindemo   0.0.1-SNAPSHOT      ad342e51021e        10 minutes ago      658 MB
mavendockerplugindemo   latest              ad342e51021e        10 minutes ago      658 MB
java                    8u111-jdk           d23bdf5b1b1b        11 months ago       643 MB
```

为什么会有两个名为mavendockerplugindemo的镜像呢？看一下maven的构建日志，有下面这么一句：

```
[INFO] Tagging mavendockerplugindemo with 0.0.1-SNAPSHOT
```

原来是以构建好的latest镜像的基础，按照我们的配置上做了一次TAG操作，本身镜像是同一个(IMAGE ID相同)；

### 验证第一种方式构建的镜像

执行以下命令，使用刚刚构建的镜像创建一个容器：

```
docker run --name demo001 -p 8080:8080 mavendockerplugindemo:0.0.1-SNAPSHOT
```

启动信息如下所示：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYXrOMVtKK4mY5oibz405da8kx9stzxibJ9eMaAPDBBJXAWJhFapJFAjMw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

我这里Ubuntu的IP是192.168.119.155，所以在windows上打开浏览器，输入地址：192.168.119.155:8080，看到如下效果，web项目正常启动：

![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYHKcl5H1uaWG4UUfIOgpicQibvKJKwwbQpzVz2jd9O9dmcdwEhzIZRgJA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)这里写图片描述

**第二种构建方式：**

指定Dockerfile 这种方式要我们自己写Dockerfile，好处是可以按照自己的需要在Dockerfile中添加更多内容，而不像第一种方式那样只能按照插件的参数规则来配置；

- 先把之前的容器停掉，现在虚拟机的控制台应该还是刚刚我们启动的容器的输出，键入“Ctrl + c”退出此容器，这样才会解除8080端口的占用；
- 在mavendockerplugindemo工程目录下新建文件pom_2_by_dockerfile.xml，内容和pom.xml一样，然后我们再去<plugins>节点添加以下内容，放在原有的<plugin>节点后面，如下所示：

```
<!--新增的docker maven插件-->
			<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>0.4.12</version>
				<!--docker镜像相关的配置信息-->
				<configuration>
					<!--镜像名，这里用工程名-->
					<imageName>${project.artifactId}</imageName>
					<!--Dockerfile文件所在目录-->					<dockerDirectory>${project.basedir}/src/main/docker</dockerDirectory>
					<!--TAG,这里用工程版本号-->
					<imageTags>
						<imageTag>${project.version}</imageTag>
					</imageTags>
					<!--构建镜像的配置信息-->
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
```

和之前的pom_1_by_param.xml相比有如下变动：

1. 新增<dockerDirectory>节点用来表示自定义Dockerfile文件的位置；
2. <baseImage>和<entryPoint>节点用不上了，在此删掉;

- 为了和第一种构建结果区分开，把pom_2_by_dockerfile.xml中定义的工程版本号从0.0.1-SNAPSHOT改为0.0.2-SNAPSHOT，如下所示：

```
	<groupId>com.bolingcavalry</groupId>
	<artifactId>mavendockerplugindemo</artifactId>
	<version>0.0.2-SNAPSHOT</version>
	<packaging>jar</packaging>
```

- 在工程的src/main/docker/目录下新建Dockerfile文件，如下图：![img](https://mmbiz.qpic.cn/mmbiz_png/VvRocjzrolzd3TJicopwAtibXViaQegoLmYYiaQXn7IVj4ibZl2OATcQDwicjkL4dSlJY7vyMm3ZGcVulvX5fm7OLByA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

Dockerfile的内容如下，就是将工程构建完毕后的jar包复制到home目录，然后构建镜像：

```
ENV ARTIFACTID mavendockerplugindemo
ENV ARTIFACTVERSION 0.0.2-SNAPSHOT
ENV HOME_PATH /home

ADD /$ARTIFACTID-$ARTIFACTVERSION.jar $HOME_PATH/mavendockerplugindemo.jar

WORKDIR $HOME_PATH

ENTRYPOINT ["java","-jar","mavendockerplugindemo.jar"]
```

- 可以开始构建了，在pom_2_by_dockerfile.xml所在目录执行以下命令：

```
mvn -f pom_2_by_dockerfile.xml clean package -DskipTests docker:build
```

会看到如下输出信息：

```
[INFO] --- docker-maven-plugin:0.4.12:build (default-cli) @ mavendockerplugindemo ---
[INFO] Copying /usr/local/work/mavendockerplugindemo/target/mavendockerplugindemo-0.0.2-SNAPSHOT.jar -> /usr/local/work/mavendockerplugindemo/target/docker/mavendockerplugindemo-0.0.2-SNAPSHOT.jar
[INFO] Copying /usr/local/work/mavendockerplugindemo/src/main/docker/Dockerfile -> /usr/local/work/mavendockerplugindemo/target/docker/Dockerfile
[INFO] Building image mavendockerplugindemo
Step 1/7 : FROM java:8u111-jdk
 ---> d23bdf5b1b1b
Step 2/7 : ENV ARTIFACTID mavendockerplugindemo
 ---> Using cache
 ---> bf895524c43e
Step 3/7 : ENV ARTIFACTVERSION 0.0.2-SNAPSHOT
 ---> Using cache
 ---> 1837d1412fae
Step 4/7 : ENV HOME_PATH /home
 ---> Using cache
 ---> 6220811f7777
Step 5/7 : ADD /$ARTIFACTID-$ARTIFACTVERSION.jar $HOME_PATH/mavendockerplugindemo.jar
 ---> 9f174f1e3c65
Removing intermediate container 9a8bc49917f4
Step 6/7 : WORKDIR $HOME_PATH
 ---> a34f05e5a272
Removing intermediate container 4bd7c136607d
Step 7/7 : ENTRYPOINT java -jar mavendockerplugindemo.jar
 ---> Running in 3122df8b7121
 ---> 5914eaeb88ab
Removing intermediate container 3122df8b7121
Successfully built 5914eaeb88ab
[INFO] Built mavendockerplugindemo
[INFO] Tagging mavendockerplugindemo with 0.0.2-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 11.848 s
[INFO] Finished at: 2017-12-23T02:42:11-08:00
[INFO] Final Memory: 35M/84M
[INFO] ------------------------------------------------------------------------
```

输入docker images命令，看到的信息如下，镜像已经在本地了：

```
REPOSITORY              TAG                 IMAGE ID            CREATED              SIZE
mavendockerplugindemo   0.0.2-SNAPSHOT      5914eaeb88ab        About a minute ago   658 MB
mavendockerplugindemo   latest              5914eaeb88ab        About a minute ago   658 MB
mavendockerplugindemo   0.0.1-SNAPSHOT      ad342e51021e        2 hours ago          658 MB
```

### 验证第二种方式构建的镜像

执行以下命令，使用刚刚构建的镜像创建一个容器：

```
docker run --name demo002 -p 8080:8080 mavendockerplugindemo:0.0.2-SNAPSHOT
```

再去windows的浏览器上访问http://192.168.119.155:8080/，可以看到和之前一样的信息，如果您不放心，也可以自己修改工程的controller源码，再构建验证是否生效；

至此，我们通过两种方式构建本地docker镜像的实战就结束了，但镜像停留在本地，只能手工push到公有或者私有仓库才能给其他人使用，在下一章我们就来体验docker-maven-plugin插件的推送能力，将本地镜像推送到私服，这样就能将项目编译构建、镜像构建、镜像推送等环节集成在一次构建中完成；
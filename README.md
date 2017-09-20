# docker
docker learn
#hello world

docker学习和使用
1. docker 安装和卸载
  a) sudo apt-get install \
	apt-transport-https \
	ca-certificates \
	curl \
	software-properties-common
	由于官方源使用 HTTPS 以确保软件下载过程中不被篡改。因此，我们首先需要添
	加使用 HTTPS 传输的软件包以及 CA 证书。
   b) 为了确认所下载软件包的合法性，需要添加软件源的 GPG 密钥。
       curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
      然后，我们需要向 source.list 中添加 Docker 软件源
	   sudo add-apt-repository \
	   "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu \
	   $(lsb_release -cs) \
	   stable"
	   
     以上命令会添加稳定版本的 Docker CE APT 镜像源
   c) 安装Docker CE
	更新 apt 软件包缓存，并安装 docker-ce
	sudo apt-get update
	sudo apt-get install docker-ce
	
   d)建立docker用户组
     sudo groupadd docker
	 sudo usermod -aG docker $USER
	 
   e) 配置镜像加速器 因为国内访问Docker Hub有时会遇到困难，注册用户并申请加速器，会获得
      我们的阿里云账户中获取的：https://7nz21707.mirror.aliyuncs.com,配置给docker引擎即可。
	  
	  vim /etc/default/docker
	  DOCKER_OPTS="--registry-mirror=https://7nz21707.mirror.aliyuncs.com
	  
	f)
	  
	  
2. 编写Dockerfile
   vim Dockerfile
   
	ENV JAVA_HOME=/usr/share/jdk1.7.0_80/
	ENV PATH=$JAVA_HOME/bin:$PATH
	ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	ENV CATALINA_HOME=/root/apache-tomcat-8.0.22
   
    CMD  ["echo","hello world"]
	虽然也可写成CMD echo hello word 方式，但这样docker会在指定的命令前加  /bin/sh -c 执行，
	有时有可能会出问题。 所以推荐采用数据结构的方式来存放命令。
	然而使用 ENTRYPOINT 时是不能被覆盖掉, docker run后面加的命令覆盖不了。
	
	指定容器启动时要执行的命令，但如果dockerfile中也有CMD指令，CMD中的参数会被附加到ENTRYPOINT 指令的后面。
	如果这时docker run命令带了参数，这个参数会覆盖掉CMD指令的参数，并也会附加到ENTRYPOINT 指令的后面。
	
	如果想要覆盖掉Dockerfile中的 ENTRYPOINT 指令，则可以使用如下命令：docker run -it --entrypoint=/bin/bash csphere/ent:0.1
	方便调试ENTRYPOINT 中写错的指令
	
	
3. docker login命令
   docker -u kenbeyond -p Soho*H3c&1206 daocloud.io
   docker -u kenbeyond -p Soho*H3c&1206 hub.docker.com或者不输入服务器域名，默认到docker的hub登录
   
 
其他命令：
   查看系统版本信息 cat /etc/os-release 
   
使用 docker commit 意味着所有对镜像的操作都是黑箱操作，生成的镜
像也被称为黑箱镜像，换句话说，就是除了制作镜像的人知道执行过什么命令、怎
么生成的镜像，别人根本无从得知。

对于docker build -t nginx:v3 .
Dockerfile 所在路径，这么理解其实是不准确的。如果对应上面的命令格式，
你可能会发现，这是在指定上下文路径。那么什么是上下文呢？
Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具。Docker 的引擎提供了一组 REST API，被称为 Docker Remote API.
因此，虽然表面上我们好像是在本机执行各种 docker 功能，但实际上，一切都是使用的远程调用形式在服务端
（Docker 引擎）完成。
docker build 命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎
中构建的。


Dockerfile例子， 基于一个已经安装好的tomcat-ubuntu版本，构建一个tomcat自启动的镜像（需要先pull该ubuntu镜像）
FROM ubuntu:14.04
MAINTAINER Kai "837599439@qq.com"

#set environment variable
ENV JAVA_HOME=/usr/share/jdk1.7.0_80/ 
ENV PATH=$JAVA_HOME/bin:$PATH 
ENV CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar 
ENV CATALINA_HOME=/root/apache-tomcat-8.0.22 
ENV PATH $JAVA_HOME/bin:$PATH 

EXPOSE 8080

ENTRYPOINT /root/apache-tomcat-8.0.22/bin/startup.sh && tail -f /root/apache-tomcat-8.0.22/logs/catalina.out


用dockerfile构建一个Java的web环境,主要分为2步，
第一步是在镜像中安装jdk并配置环境变量，
第二步是安装tomcat。
先贴一下完整的dockerfile

FROM ubuntu:14.04 
MAINTAINER Kai "837599439@qq.com"
RUN apt-get update 
ADD jdk-8u77-linux-x64.tar.gz /usr/local/java
ENV JAVA_HOME /usr/local/java/jdk1.8.0_77 
ENV PATH $JAVA_HOME/bin:$PATH 
ENV CLASSPATH .:$JAVA_HOME/lib
COPY apache-tomcat-8.0.33/ /usr/local/tomcat/apache-tomcat-8.0.33/ 
#RUN unzip /usr/local/tomcat/apache-tomcat-8.0.33.zip 
RUN chmod +x /usr/local/tomcat/apache-tomcat-8.0.33/bin/*.sh 
EXPOSE 8080 
ENTRYPOINT /usr/local/tomcat/apache-tomcat-8.0.33/bin/startup.sh && /bin/bash




	

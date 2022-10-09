FROM ubuntu:14.04

RUN sed -i "s@http://.*archive.ubuntu.com@http://repo.huaweicloud.com@g" /etc/apt/sources.list && \
    sed -i "s@http://.*security.ubuntu.com@http://repo.huaweicloud.com@g" /etc/apt/sources.list

    
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && \
    LC_ALL=C DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    git-core gnupg flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 libncurses5 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev libgl1-mesa-dev libxml2-utils xsltproc unzip libffi-dev libssl-dev openssh-client wget python2.7 rsync \ 
    ca-certificates-java java-common liblcms2-2 libjpeg8 libnss3 libfreetype6 libpcsclite1 libxi6 libxrender1 libxtst6 libgtk2.0-0 libxrandr2 libxinerama1 libatk-wrapper-java-jni libasound2 libgif4 
RUN dpkg-reconfigure -f noninteractive tzdata
RUN locale-gen en_US.UTF-8
RUN dpkg-reconfigure -f noninteractive locales
COPY openjdk-8-jdk_8u45-b14-1_amd64.deb /tmp/
COPY openjdk-8-jre_8u45-b14-1_amd64.deb /tmp/
COPY openjdk-8-jre-headless_8u45-b14-1_amd64.deb /tmp/

RUN dpkg -i /tmp/openjdk-8-jre-headless_8u45-b14-1_amd64.deb
RUN dpkg -i /tmp/openjdk-8-jre_8u45-b14-1_amd64.deb
RUN dpkg -i /tmp/openjdk-8-jdk_8u45-b14-1_amd64.deb
  
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /var/tmp/*
    
ARG home=/home/user 

# download from https://storage.googleapis.com/git-repo-downloads/repo
# other info see: https://source.android.com/docs/setup/download#installing-repo
COPY repo /tmp/repo
RUN install -m 755 /tmp/repo /usr/bin/repo

# download from https://www.python.org/ftp/python/3.6.10/Python-3.6.10.tgz
ARG v=3.6.10
RUN wget -q https://www.python.org/ftp/python/${v}/Python-${v}.tgz && mv Python-${v}.tgz /tmp/
COPY Python-${v}.tgz /tmp/Python-${v}.tgz
RUN cd /tmp
RUN tar -xvf /tmp/Python-${v}.tgz && \
	cd Python-${v} && \
	./configure && \
	make && \
	make install	
RUN cd /
RUN ln -s /usr/bin/python2.7 /usr/bin/python

# curl https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/
# 报错，应该是docker环境里不信任清华的证书，强制去掉https证书校验
# 目前没有搞清楚为什么，本地环境是没有问题的
ENV PYTHONHTTPSVERIFY=0


COPY .vimrc ${home}/.vimrc

ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8

WORKDIR /aosp

ARG uid
ARG gid

RUN groupadd -g ${gid} user && \
    useradd -l -u ${uid} -g ${gid} -M user && \
    #添加用户到sudo用户组
    usermod -aG sudo user
COPY .bashrc ${home}/.bashrc
RUN chown -R user:user ${home}
ENV USER=user
ENV HOME=/home/user

# 目前用的是清华源的镜像，暂时还没有加入源码下载的过程
# 请参考 https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/

# 快速备忘
# 初始化仓库
# repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest
# or 需要某个特定的版本
# repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest -b android-8.1.0_r10
# 同步源代码，会花一定的时间
# repo sync -j4

    
       
	

      

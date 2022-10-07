---
title: docker环境下编译android源码
---



### 1 安装docker

+ 快速备忘

  + ubuntu环境下安装docker [Install Docker Engine]( https://docs.docker.com/engine/install/ubuntu/)

  + [non-root user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

     ```sh
      $ sudo groupadd docker
      $ sudo usermod -aG docker $USER
     ```

   记得要退出当前用户，重新登录

### 2 目录结构

	|-- aospInDocker
	
	       |-- aosp (如果不存在需要自行创建)    
	
	       |-- docker 
	
	       |-- AospMake 
	
	+ aosp 安卓源码存放的位置
	+ docker Dockerfile相关的文件
	+ AospMake 工具脚本

### 3 编译DockerFile

```shell
$ cd aospInDocker
$ chmod u+x AospMake
$ ./AospMake docker 
# 等待docker镜像创建成功...

```



### 4 下载和编译安卓源码

```
$ ./AospMake aosp 
# 进入docker交互环境
# 以下所有都在docker环境中执行
# 下载过程可参考 https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/ 
user@liulixin:~/aosp$ repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest
user@liulixin:~/aosp$ repo sync -j4
# 漫长的同步过程...
user@liulixin:~/aosp$ source build/envsetup.sh
user@liulixin:~/aosp$ lunch
```



### 5 FQ

+ python源码安装从官网下载[Python-3.7.10.tgz](https://www.python.org/ftp/python/3.7.10/Python-3.7.10.tgz)执行会报错_ssl not imported，回退到[Python-3.6.10.tgz](https://www.python.org/ftp/python/3.6.10/Python-3.6.10.tgz)解决
+ 在docker环境里curl https://mirrors.tuna.tsinghua.edu.cn/help/AOSP/ 提示证书验证错误，但是在本地环境没有问题，这个没有定位到准确的原因，但是可以通过设置环境变量 PYTHONHTTPSVERIFY=0来解决
+ repo sync -j4 结束会报错误 在[这里](https://blog.csdn.net/tkwxty/article/details/121333540)找到了解决方案

​	    <img src="docker环境下编译android/1.png" alt="docker环境下编译android" style="zoom:50%;" />

+ 目前Dockerfile同时安装了python2.7 和 python3.4 python2.7版本的用来编译8.1.0以下的版本，python3.4以上的用来编译8.1.0以上的版本，但是尚未实际测试。切换python版本可以按照如下步骤来操作

       ```shell
       $ docker container ps # 查找当前运行aosp的容器
       CONTAINER ID   IMAGE                  COMMAND       CREATED          STATUS          PORTS     NAMES
       3284edd4b8a8   aosp-liulixin:v0.0.2   "/bin/bash"   11 minutes ago   Up 11 minutes             liulixin
       $ docker docker exec -u 0 -it 3284edd4b8a8 bash # 连接当前运行的docker容器
       
       # ln -s /usr/bin/python3.4 /usr/bin/python # 改变当前的python版本为3.4
       # ln -s /usr/bin/python2.7 /usr/bin/python # 改变当前的python版本为2.7
       ```

  






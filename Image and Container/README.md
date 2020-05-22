## 1 深入探讨Image

>  说白了，image就是由一层一层的layer组成的。

### 1.1 官方image

>  https://github.com/docker-library

### 1.2 Dockerfifile

详见下列Dockerfifile文件中常见语法

#### 1.2.1 FROM

> 指定基础镜像，比如FROM ubuntu:16.04

````dockerfile
FROM ubuntu:14.04
````

#### 1.2.2 RUN

在镜像内部执行一些命令，比如安装软件，配置环境等，换行可以使用""

```dockerfile
RUN groupadd -r mysql && useradd -r -g mysql mysql
```

#### 1.2.3 ENV

设置变量的值，`ENV MYSQL_MAJOR 5.7`，可以通过`docker run --e key=value`修改，后面可以直接使

用`${MYSQL_MAJOR}`

````dockerfile
ENV MYSQL_MAJOR 5.7
````

#### 1.2.4 LABEL

设置镜像标签

````dockerfile
LABEL email="albertini_peter@163.com"
LABEL name="albertini_peter"
````

#### 1.2.5 VOLUME

指定数据的挂在目录

````dockerfile
VOLUME /var/lib/mysql
````

#### 1.2.5 COPY

将主机的文件复制到镜像内，如果目录不存在，会自动创建所需要的目录，注意只是复制，不会提取和

解压

````dockerfile
COPY docker-entrypoint.sh /usr/local/bin/
````

#### 1.2.6 ADD

将主机的文件复制到镜像内，和COPY类似，只是ADD会对压缩文件提取和解压

````dockerfile
ADD application.yml /opt/itcrazy2016/
````

#### 1.2.7 WORKDIR

指定镜像的工作目录，之后的命令都是基于此目录工作，若不存在则创建

```dockerfile
WORKDIR /usr/local 
WORKDIR tomcat 
RUN touch test.txt
```

> 会在/usr/local/tomcat下创建test.txt文件

```dockerfile
WORKDIR /root 
ADD app.yml test/
```

>  会在/root/test下多出一个app.yml文件

#### 1.2.8 CMD

容器启动的时候默认会执行的命令，若有多个CMD命令，则最后一个生效

````dockerfile
CMD ["mysqld"] 
或
CMD mysqld
````

#### 1.2.9 ENTRYPOINT

与CMD的不同, docker run执行时，会覆盖CMD的命令，而ENTRYPOINT不会

```dockerfile
ENTRYPOINT ["docker-entrypoint.sh"]
```

#### 1.2.10 EXPOSE

指定镜像要暴露的端口，启动镜像时，可以使用-p将该端口映射给宿主机

````dockerfile
EXPOSE 3306
````

### 1.3 Dockerfifile实战Spring Boot项目

- 创建一个Spring Boot项目

- 写一个controller

  ````java
  @RestController 
  public class DockerController { 
      @GetMapping("/dockerfile") 
      @ResponseBody 
      String dockerfile() { 
          return "hello docker" ; 
      } 
  }
  ````

- mvn clean package打成一个jar包，在target下找到`demo-0.0.1-SNAPSHOT.jar` ，上传到docker环境的宿主机里指定目录【例如:springboot-demo】下，并在改目录下创建Dockerfile

  ````dockerfile
  FROM openjdk:8 
  MAINTAINER superalbertini 
  LABEL name="dockerfile-demo" version="1.0" author="superalbertini" 
  COPY demo-0.0.1-SNAPSHOT.jar dockerfile-image.jar 
  CMD ["java","-jar","dockerfile-image.jar"]
  ````

- 基于Dockerfile构建镜像 

  ```cmd
  docker build -t test-docker-image .
  ```

- 基于image创建container

  ```cmd
  docker run -d --name demo01 -p 6666:8080 test-docker-image 
  ```

- 查看启动日志

  ````cmd
  docker logs demo01
  ````

- 宿主机上访问`curl localhost:6666/dockerfile`

  ```tex
   hello docker 
  ```



### 1.4 镜像仓库

#### 1.4.1 docker hub

> hub.docker.com
>
> superalbertini登录

- 在docker机器上登录

  ````cmd
  docker login
  ````

- 输入用户名和密码

- `docker push superalbertini/ubuntu-bigdata-cluster-base`

  > 注意镜像名称要和docker id一致，不然push不成功

- 给image重命名，并删除掉原来的

  ````cmd
  docker tag ubuntu-bigdata-cluster-base superalbertini/ubuntu-bigdata-cluster-base
  docker rmi -f superalbertini/ubuntu-bigdata-cluster-base
  ````

- 再次推送，刷新hub.docker.com后台，发现成功

- 别人下载，并且运行

  ```cmd
  docker pull superalbertini/ubuntu-bigdata-cluster-base
  docker run -d --name demo01 -p 6661:8080 superalbertini/ubuntu-bigdata-cluster-base
  ```

#### 1.4.2 阿里云docker hub

> 阿里云docker仓库
>
> https://cr.console.aliyun.com/cn-hangzhou/instances/repositories

- 登录阿里云Docker Registry

```cmd
$ sudo docker login --username=superalbertini registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

- 从Registry中拉取镜像

```cmd
$ sudo docker pull registry.cn-hangzhou.aliyuncs.com/docker-registry-peter/k8s-repo:[镜像版本号]
```

- 将镜像推送到Registry

```cmd
$ sudo docker login --username=superalbertini registry.cn-hangzhou.aliyuncs.com

$ sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/docker-registry-peter/k8s-repo-images:[镜像版本号]

$ sudo docker push registry.cn-hangzhou.aliyuncs.com/docker-registry-peter/k8s-repo-images:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

#### 1.4.3 搭建自己的Docker Harbor

- 访问github上的harbor项目 

   https://github.com/goharbor/harbor 

- 下载版本，比如[v2.0.0]

  https://github.com/goharbor/harbor/releases 

- 找一台安装了docker-compose，上传并解压`tar -zxvf xxx.tar.gz` 

- 进入到harbor目录 

  > 修改harbor.cfg文件，主要是ip地址的修改成当前机器的ip地址 
  >
  > 同时也可以看到Harbor的密码，默认是Harbor12345 

- 安装harbor，需要一些时间

  ```shell
  sh install.sh
  ```

- 浏览器访问服务器ip，输入用户名和密码即可 

#### 1.5 Image常见操作

- 查看本地image列表 

  ```shell
  $ docker images 
  $ docker image ls 
  ```

- 获取远端镜像

  ````shell
  $ docker pull
  ````

- 删除镜像

  ````shell
  $ docker image rm imageid 
  $ docker rmi -f imageid 
  $ docker rmi -f $(docker image ls) 删除所有镜像 
  ````

- 运行镜像 

  ````shell
  $ docker run image
  ````

- 发布镜像

  ````shell
  $ docker push
  ````

### 2 深入探讨Container

> 既然container是由image运行起来的，那么是否可以理解为container和image有某种关系？
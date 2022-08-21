# SpringBoot

## 一、概述

1. 只要SpringBoot父工程中帮助我们管理当前你需要使用的jar的依赖版本，开发人员则无需添加版本。
2. SpringBoot官方提供了大量stater场景启动器的目的：快速进入对应的服务，让开发人员将重点放在**业务逻辑**上。

<div style="background-color:orange">步骤1:导入SpringBoot起步依赖</div>

```xml
<!--springboot工程需要继承的父工程-->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.3.10.RELEASE</version>
</parent>

<dependencies>
  <!--web开发的起步依赖   场景启动器依赖-->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
</dependencies>
```

<div style="background-color:orange">步骤2:编写引导类</div>

```java
/**
 * 引导类。 SpringBoot项目的入口
 */
@SpringBootApplication
public class HelloApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloApplication.class, args);
    }
}
```

<div style="background-color:orange">步骤3:定义HelloController</div>

```java
package com.itheima.sh.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello(){
        return " hello Spring Boot !";
    }
}
```

<div style="background-color:orange">步骤4:启动测试，访问浏览器: http://localhost:8080/hello</div>



## 二、配置文件

### 1、读取配置数据

<font color="red">步骤：</font>

1、定义实体类 Man和Woman

``` java
@Data
@ConfigurationProperties(prefix = "man")
public class Man {

   private String userName;
   private Boolean boss;
   private Date birth;
   private Integer age;
   private String[] address;
   private List<String> addressList;
   private Map<String, Object> hobbies;  // 爱好
   private Woman woman;
   private List<Woman> wifes;
}

@Data
class Woman {

    private String userName;
    private Integer age;
    private List<String> addresses;
}
```

2、添加配置文件

``` yaml
server:
  port: 8082

company2: itcast
company1: itheima


man:
  userName: laowang
  boss: true
  birth: 1980/09/09 01:01:01
  age: 41
  address: [beijing, shanghai]
  addressList:
    - 北京
    - 上海
    - 深圳
  hobbies:
    sports:
      - badminton
      - basketball
    musics:
      - dj
      - 23
  woman:
    userName: xiaohua
    age: 20
    addresses:
      - 北京
      - 上海
  wifes:
     - userName: xiaosan1
       age: 25
       addresses:
         - 北京

     - userName: xiaosan2
       age: 20
       addresses:
         - 上海
```

3、依赖注入测试

**方式1：引导类开启注解支持(推荐)**

@ConfigurationProperties(prefix = "man”)+ @EnableConfigurationProperties(Man.class)

![image-20210503001542063](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220015426.png)

**方式2：@ConfigurationProperties(prefix = "man”)+@Component**

![image-20220822001548916](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220015956.png)



### 2、多环境切换

profile配置方式：

​	多profile文件方式：提供多个配置文件，每个代表一种环境。

​		application-dev.properties/yml 开发环境

​		application-test.properties/yml 测试环境

​		application-pro.properties/yml 生产环境

**profile激活方式**

- **配置文件： 再配置文件中配置：spring.profiles.active=dev**
- 虚拟机参数：在VM options 指定：-Dspring.profiles.active=dev
- **命令行参数：java -jar XXX.jar --spring.profiles.active=dev**



### 3、项目内部配置文件加载顺序

**优先级：由高到底**

- file:./config/：当前项目下的/config目录下
- file:./           ：当前项目的根目录
- classpath:/config/：classpath的/config目录
- classpath:/  ：classpath的根目录

### 4、项目外部配置文件加载顺序

1. 命令行

```cmd
java -jar app.jar --name="Spring“ --server.port=9000
```

2. 指定配置文件位置

```cmd
 java -jar myproject.jar --spring.config.location=e://application.properties
```

3. 外部不带profile的properties文件

```java
classpath:/config/application.properties
classpath:/application.properties
```

<font color="red">小结：</font>

* **整体加载优先级： jar包外  到  jar包内**

* 如果jar和配置文件在同一级，配置文件会生效，会被自动的加载

* 打包的时候 只会去打包classpath(src|main/resources)的配置文件，项目的根目录下的配置文件不会被打包到jar包中。







# git

``` bash
git init

git add * #添加所有文件进入暂存区

git reset #将暂存区的文件取消暂存活着是切换到制定版本

git reset --hard 3e83300001236f435942434a262e7bd04b345696#回滚到某个版本

git commit -m "注释信息" hello.txt #提交hello.txt到版本库

git status #查看状态

git log #查看日志 按q退出
-----------------------------------------------#下面是远程仓库的操作

git clone https://github.com/f0qw/kkktest.git

git remote -v #查看远程仓库

git remote add origin http://github.com/f0qw/kkktest.git#添加远程仓库

git clone #从远程仓库克隆

git pull origin master#从远程仓库拉取

git push origin master#推送到master分支  origin远程仓库


----------------------------------------------#下面是分支的操作
git branch #查看本地分支
git branch -r #查看远程仓库的分支
git branch -a #列出本地和远程的所有分支

git branch [name]  #创建分支
git checkout [name] #切换分支
git push [远程仓库别名][分支名称]  #推送至远程仓库分支
git merge [name]  #合并分支
#遇到合并分支冲突时可能会有 cannot do a partial comit during merge
#此时加个-i就可以解决问题
git comit -m "注释" a.txt -i


------------------------------------------------#标签的操作
git tag #列出已有的标签
git tag [name] #创建标签
git push [远程仓库名][分支名称] #将标签推送至远程仓库
git checkout -b [branch][name] #创建新分支检出标签
```







# docker

## 一、基本概念

**镜像（Image）**：Docker将应用程序及其所需的依赖、函数库、环境、配置等文件打包在一起，称为镜像。

**容器（Container）**：镜像中的应用程序运行后形成的进程就是**容器**，只是Docker会给容器进程做隔离，对外不可见。

Docker是一个CS架构的程序，由两部分组成：

- 服务端(server)：Docker守护进程，负责处理Docker指令，管理镜像、容器等

- 客户端(client)：通过命令或RestAPI向Docker服务端发送指令。可以在本地或远程向服务端发送指令。

如图：

![image-20210731154257653](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220031938.png)

## 二、Docker的基本操作

### 1、镜像命令

常见的镜像操作命令如图：

![image-20210731155649535](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220032066.png)

注意：可以利用docker xx --help命令查看docker命令

例如，查看save命令用法，可以输入命令：

```sh
docker save --help
```

结果：

![image-20210731161104732](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220034373.png)

命令格式：

```shell
docker save -o [保存的目标文件名称] [镜像名称]
```



### 2、容器操作

容器操作的命令如图：

![image-20210731161950495](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220035781.png)

容器保护三个状态：

- 运行：进程正常运行
- 暂停：进程暂停，CPU不再运行，并不释放内存
- 停止：进程终止，回收进程占用的内存、CPU等资源

``` bash
docker run 
--name nacos \
-e MODE=standalone \
--link mysql:mysql \   
-e SPRING_DATASOURCE_PLATFORM=mysql \    
-e  MYSQL_SERVICE_HOST=mysql \
-e MYSQL_SERVICE_PORT=3306 \     
-e MYSQL_SERVICE_DB_NAME=nacos \    
-e MYSQL_SERVICE_USER=root \ 　　　　
-e MYSQL_SERVICE_PASSWORD=root \　　
-p 8848:8848 -d nacos/nacos-server:latest
```

其中：

- docker run：创建并运行一个容器，处于运行状态
- docker pause：让一个运行的容器暂停
- docker unpause：让一个容器从暂停状态恢复运行
- docker stop：停止一个运行的容器
- docker start：让一个停止的容器再次运行

- docker rm：删除一个容器

### 3、数据卷

**数据卷（volume）**是一个虚拟目录，指向宿主机文件系统中的某个目录。

![image-20210731173541846](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220038290.png)

一旦完成数据卷挂载，对容器的一切操作都会作用在数据卷对应的宿主机目录了。

这样，我们操作宿主机的/var/lib/docker/volumes/html目录，就等于操作容器内的/usr/share/nginx/html目录了

### 4、数据卷基本操作

数据卷操作的基本语法如下：

```dockerfile
docker volume [COMMAND]
```

docker volume命令是数据卷操作，根据命令后跟随的command来确定下一步的操作：

- docker volume create：创建数据卷
- docker volume ls：查看所有数据卷
- docker volume inspect：查看数据卷详细信息，包括关联的宿主机目录位置
- docker volume rm：删除指定数据卷
- docker volume prune：删除所有未使用的数据卷

### 5、挂载数据卷

我们在创建容器时，可以通过 -v 参数来挂载一个数据卷到某个容器内目录，命令格式如下：

```sh
docker run \
  --name mn \
  -v html:/root/html \
  -p 8080:80
  nginx \
```

这里的-v就是挂载数据卷的命令：

- `-v html:/root/htm` ：把html数据卷挂载到容器内的/root/html这个目录中



### 6、创建mysql容器实例

**创建并运行一个MySQL容器，将宿主机目录直接挂载到容器**

步骤如下：

1）获取mysql镜像

2）创建目录/tmp/mysql/data

3）创建目录/tmp/mysql/conf

4）去DockerHub查阅资料，创建并运行MySQL容器，要求：

① 挂载/tmp/mysql/data到mysql容器内数据存储目录

② 挂载/tmp/mysql/conf/hmy.cnf到mysql容器的配置文件

③ 设置MySQL密码

```bash
拉取镜像:
	docker pull mysql:5.6
进入tmp目录,创建/tmp/mysql/data和/tmp/mysql/conf
  mkdir -p /tmp/mysql/data   // 存放生成的数据信息
  mkdir -p /tmp/mysql/conf   // 存放共享的配置文件信息
将配置文件拷贝到/tmp/mysql/conf
创建mysql容器,并绑定对应的文件夹和文件:
  docker run \
  --name mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  -p 3306:3306 \
  -d \
  -v /tmp/mysql/data:/var/lib/mysql \
  -v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/my.cnf \
  --privileged \
  mysql:5.6
---------------------
  docker run \   // 创建容器
  --name mysql \ // 给容器起名
  -e MYSQL_ROOT_PASSWORD=root \ // 设置root账户密码
  -p 3306:3306 \ // 端口映射
  -d \ // 后台运行
  -v /tmp/mysql/data:/var/lib/mysql \ // 绑定宿主机上的文件夹
  -v /tmp/mysql/conf/hmy.cnf:/etc/mysql/conf.d/my.cnf \ // 绑定宿主机上的文件
  --privileged \ // 设置超级管理员远程访问权限
  mysql:5.6 // 镜像名称
```

## 三、Dockerfile自定义镜像

镜像是将应用程序及其需要的系统函数库、环境、配置、依赖打包而成。

我们以MySQL为例，来看看镜像的组成结构：

![image-20210731175806273](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220044335.png)

简单来说，镜像就是在系统函数库、运行环境基础上，添加应用程序文件、配置文件、依赖文件等组合，然后编写好启动脚本打包在一起形成的文件。

我们要构建镜像，其实就是实现上述打包的过程。

### 1、Dockerfile语法

构建自定义的镜像时，并不需要一个个文件去拷贝，打包。

我们只需要告诉Docker，我们的镜像的组成，需要哪些BaseImage、需要拷贝什么文件、需要安装什么依赖、启动脚本是什么，将来Docker会帮助我们构建镜像。

而描述上述信息的文件就是Dockerfile文件。

**Dockerfile**就是一个文本文件，其中包含一个个的**指令(Instruction)**，用指令来说明要执行什么操作来构建镜像。每一个指令都会形成一层Layer。

![image-20210731180321133](https://tuchuangf0qw.oss-cn-fuzhou.aliyuncs.com/typora_images/202208220045874.png)

更新详细语法说明，可以参考官网文档： https://docs.docker.com/engine/reference/builder

### 2、构建Java项目

``` dockerfile
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，JDK的安装目录
ENV JAVA_DIR=/usr/local

# 拷贝jdk和java项目的包
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar

# 安装JDK
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8

# 配置环境变量
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin

# 暴露端口
EXPOSE 8090
# 入口，java项目的启动命令
ENTRYPOINT java -jar /tmp/app.jar
```

运行命令：

``` bash
docker build -t javaweb:1.0 .
```










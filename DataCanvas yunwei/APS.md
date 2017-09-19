# APS运维管理

DataCanvas APS作为分析应用的快速实现，支持机器学习、行业算法和分析逻辑的积累。APS的底层架构使用Docker来实施部署规划，这种方式部署起来比较简单，后期管理也比较容易。以下是两种后期维护方案：

* 方案一：Docker shell

  直接登录系统，在系统终端直接通过Docker shell 进行查看、管理以及后期的维护。该方案比较适合运维技术人员。

* 方案二：Dockerfly

  对Docker不是特别熟练的技术人员，可以使用Dockerfly在Web端进行管理。

### Docker shell

#### Docker shell常用操作

![](/DataCanvas yunwei/docker shell常用操作.PNG)

#### 容器相关命令举例
* **开启一个容器**


```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
* **关闭一个容器**


```
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```
* **删除一个容器**


```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```
* **查看一个容器的详细信息**


```
Docker  inspect  [OPTIONS]  CONTAINER|IMAGE|TASK [CONTAINER|IMAGE|TASK...]
```

* **在docker index中搜索image（search）**


```
docker search [OPTIONS] TERM
```
* **从docker registry server 中下拉image或repository（pull）**


```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```
* **推送一个image或repository到registry（push） **


```
docker push [OPTIONS] NAME[:TAG]
```
* **将一个容器固化为一个新的image **


```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```
**查看服务状态命令举例：**

* **查看系统上的docekr服务是否开启**

```
ps	-ef	| grep docker
```

* **查看所有容器的运行状态**


```
docker	ps  -a
```
* **查看容器中运行的进程信息**


```
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```
* **查看本地所有images**


```
docker	images
```
####关于nmon 
在维护APS的同时，也需要对系统CPU、内存、磁盘、网络等进行管理，此时可以使用nmon这个工具，该工具操作简单、清楚明了。

#####安装nmon 
nmon 是一个二进制可执行文件，无需安装，解压后直接运行可执行文件就可以使用了。使用时请先执行`chmod +x nmon`命令，然后执行` ./nmon`命令。如果将nmon文件上传到服务器的/usr/bin目录，它就能在任意目录执行，直接执行nmon目录即可使用。

![](/DataCanvas yunwei/nmon工具使用界面.png)

##### nmon的使用
nmon有两种使用方法。一是进入nmon界面，通过按键来查看性能情况；二是生成nmon文件，然后利用nmon_analyser进行性能分析。通过这两种方式，都可以了解系统资源的基本使用情况，以此来判断是否符合APS的运行。

###### 方法一
执行`nmon`命令进入nmon界面，通过按键来查看性能参数信息。各个按键的含义如下：

![](/DataCanvas yunwei/按键含义.PNG)

在nmon信息图中查看CPU、内存和磁盘输入输出的统计情况。

![](/DataCanvas yunwei/nmon信息图.png)

###### 方法二

利用nmon_analyser进行性能分析，需要执行以下命令生成nmon文件：


```
nmon -fT -s 30 -c 36
```
**命令含义：** 
* -f ：按标准格式输出文件<hostname>_YYYYMMDD_HHMM.nmon 。
* -s ：每隔n秒抽样一次。
* -c：取样的次数，默认为36次，即监控时间为30*36=1080s=15min。

命令执行后，会自动生成一个xxx.nmon文件来保存性能数据，然后可以利用nmon_analyser工具进行分析。nmon_analyser是一个有特殊功能的excel文件，在window系统下免安装使用。该命令可以进行长时间数据捕捉。用户还可以通过设置`crontab`命令实现定时性能监控任务。 	

### Dockerfly

APS维护也就是对Docker容器的管理。用户可以通过多种监控管理工具来管理Docker容器，但是这些工具各有优缺点。

常见的Docker监控管理工具如下：

* **Kubernetes**

  功能：可跨计算机集群部署容器，不需要开启网络端口或执行其他操作，容器可相互联系。
* **Dockersh**

  功能：让多个用户可以连接到某个主机，每个用户都运行自行选择的单独的Docker容器所生成的外壳。

* **DockerUI**

  功能：通过Web浏览器的命令行来管理的任务。

* **Shipyard**

  功能：简化对横跨多个主机的Docker容器集群进行管理。

  通过Web用户界面，您可以浏览相关信息，例如您的容器正在使用处理器和内存资源的情况、正在运行的容器列表等，还可以检查所有集群上的事件日志。

* **Kitematic**

  功能：成为基于OS X的编程员们手里一款实用的桌面环境开发工具。

* **Logspout**

  功能：管理容器中应用的日志。

* **Autodock**

  功能：自动化Docker。

* **Docker-in-Docker**

* **Heroku-Docker**

  功能：用命令行将其Heroku项目转换成Docker镜像。

* **Docker Node Tester**

  功能：测试Docker。

我们选择管理工具时，会从以下角度来考虑：

* Docker监控方案实现
* 自己动手 + 开源软件
* 功能
* 信息详细程度
* 查询的灵活程度
* 报警 + API
* 灵活性
* 维护
* 运维
* 部署复杂程度
* 高可用

由于DockerUI不支持多机器，Shipyard不支持批量操作，而Dockerfly对上述功能都支持，且具有部署简单、操作简单、开源的特点，因此我们选用了Dockerfly。

Dockerfly是基于 Docker1.12+ (Docker API 1.24+) 开发出的 Docker 管理工具，它提供了最基本的基于 Docker 的管理功能，目的是能够方便广大 Docker 初学者以及 Docker 管理员能够快速的进行 Docker 容器的管理和维护。通过 Dockerfly 的 swarm 管理，用户可以轻松的构建起一个基于 Docker 原生的集群系统。

#### Dockerfly的使用
通过Dockerfly 可以实现APS 容器运行状态的Web管理以及维护。如需使用Dockerfly，请先在APS所在的主机上以容器的形式安装Dockerfly，然后设置物理主机端口到该容器端口的端口映射，以便用户可以在外网通过浏览器成功访问Dockerfly。

系统基本指标（如系统CPU、内存、磁盘、网络等）监控管理可采用方案一中的nmon工具。

##### 登录主界面

在浏览器地址栏中输入“http://APS_IP：映射后的物理端口号” 并按回车键，打开Dockerfly登录界面。用户名和密码请咨询我们的工程师，用以登录。

![](/DataCanvas yunwei/登录主界面.png)

##### 主界面内容
左侧导航栏中包括Swarm、Node、Service、Task、Container、Network、Volume、Images和Setting等标签页。

首页中可看到当前主机的基本配置。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](/DataCanvas yunwei/主界面.png)

##### 查看所有容器

点击左侧Container标签页，右侧页面会将这台主机上所有的container显示出来。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](/DataCanvas yunwei/Container.png)

##### 查看所有image

点击左侧Image标签页，右侧页面会将这台主机上所有的image显示出来。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](/DataCanvas yunwei/image.png)

##### 设置（Setting）
Setting页面中主要包括用户管理、添加主机、修改密码和registry（即添加SaaS用户）功能。 

![](/DataCanvas yunwei/Setting.png)



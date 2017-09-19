# 运维指导

本部分主要介绍DataCanvas APS智能数据分析平台需要进行的维护任务，帮助用户了解相关的维护工具及其使用方法。

## APS运维管理简介

DataCanvas APS是分析平台工具，用户可通过该平台快速进行数据清洗、数据挖掘和数据建模分析等，从而生成行业大数据解决方案。APS作为应用分析的管理平台，支持机器学习、行业算法和分析逻辑的积累与重用。

APS节点的运维项目包括主机运维以及APS应用运维两部分：

* 主机运维是指对主机状态的维护检查，以保障系统的稳定、高效运行，检查项包括但不限于对CPU、内存、网络、硬盘、文件系统等的运行指标；
* APS应用运维是指对APS应用状态的检查维护，以保障APS等正常运行并对外提供服务。APS的底层架构是基于Docker的容器管理平台，对APS的维护即对Docker容器的维护，可以通过如下两种方式：
  •    Docker shell
  Docker shell是一种基于命令行的管理方式，具有快速、直接、高效等特点，该方案比较适合运维技术人员。
  •    Dockerfly
  Dockerfly是基于Web的容器管理工具，简化了运维管理难道，对于不是很熟练使用命令行的运维人员，可以使用该方案。

## 主机运维

主机的运维通过nmon工具实现。nmon是Linux的性能监视和分析工具，可以完成CPU、内存、磁盘、网络等的性能进行检测并保存监视数据。

### 安装nmon

1. 从[http://nmon.sourceforge.net/pmwiki.php?n=Site.Download](http://nmon.sourceforge.net/pmwiki.php?n=Site.Download)选择合适的版本进行下载。
2. 将已下载的nmon执行文件如“nmon16g\_x86\_sles12”上传到“/usr/bin”目录下。
3. 执行如下命令添加可执行权限。
   `# cd /usr/bin`
   `# chmod u+x nmon16g_x86_sles114`
4. 启动nmon。
   `# ./nmon16g_x86_sles114`
   启动后，终端显示如下所示的界面。

![](/DataCanvas yunwei/nmon工具使用界面.png)

### 使用nmon

nmon有两种使用方法，一是进入nmon界面，通过按键来查看性能情况；二是生成nmon文件，然后利用nmon\_analyser进行性能分析。通过这两种方式，都可以了解系统资源的基本使用情况，以此来判断是否符合APS的运行。

#### 方法一

执行nmon命令进入nmon界面，通过按键来查看性能参数信息。各个按键的含义如下：

| 按键 | 全称 | 含义 |
| :--- | :--- | :--- |
| c | CPU | CPU使用率 |
| m | memory | 内存使用情况 |
| d | disks | 磁盘统计信息 |
| r | resource | 系统资源视图 |
| k | kernel | 内核统计信息 |
| h | more option（help） | 多种选择 |
| I | CPU Long-term | 长期处理器平均使用率视图 |
| j | filesystems | 文件系统视图 |
| n | network | 网络接口视图 |
| N | NFS | 网络文件系统视图 |
| t | Top-process | 查看消耗资源最多的进程 |
| - | faster screen updates | 加快屏幕刷新 |
| + | slower screen updates | 减慢屏幕刷新 |
| V | virtual memory | 虚拟内存，卷组统计信息 |
| v | verbose hints | 冗余检查 |
| . | only busy disks/procs | 只显示忙碌的磁盘或进程 |
| q | quit | 退出 |
|  |  |  |

在nmon信息图中查看CPU、内存和磁盘输入输出的统计情况。

![](blob:file:///e63e30d2-945f-4654-b092-ff4b8e6156c2)

#### 方法二

利用nmon\_analyser进行性能分析，需要执行以下命令生成nmon文件：

```
nmon -fT -s 30 -c 36
```

**命令中各参数含义：**

* -f ：按标准格式输出文件\_YYYYMMDD\_HHMM.nmon 。
* -s ：每隔n秒抽样一次。
* -c：取样的次数，默认为36次，即监控时间为30\*36=1080s=15min。

命令执行后，会自动生成一个xxx.nmon文件来保存性能数据，然后可以利用nmon\_analyser工具进行分析。nmon\_analyser是一个有特殊功能的excel文件，在window系统下免安装使用。该命令可以进行长时间数据捕捉。用户还可以通过设置`crontab`命令实现定时性能监控任务。

## APS应用运维

### Dockershell

#### Dockershell常用操作

Docker shell常用命令如下表所示，详细使用说明可以通过`docker Command --help`命令进行查询。

|  |  |
| :--- | :--- |
|  |  |



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

* **在docker**
  **index中搜索image（search）**

```
docker search [OPTIONS] TERM
```

* **从docker**
  **registry**
  **server**
  **中下拉image或repository（pull）**

```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

* **推送一个image或repository到registry（push）**

```
docker push [OPTIONS] NAME[:TAG]
```

* **将一个容器固化为一个新的image**

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

**查看服务状态命令举例：**

* **查看系统上的docekr服务是否开启**

```
ps    -ef    | grep docker
```

* **查看所有容器的运行状态**

```
docker    ps  -a
```

* **查看容器中运行的进程信息**

```
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```

* **查看本地所有images**

```
docker    images
```

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

  功能：成为基于OSX的编程员们手里一款实用的桌面环境开发工具。

* **Logspout**

  功能：管理容器中应用的日志。

* **Autodock**

  功能：自动化Docker。

* **Docker-in-Docker**

* **Heroku-Docker**

  功能：用命令行将其Heroku项目转换成Docker镜像。

* **DockerNodeTester**

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

Dockerfly是基于Docker1.12+\(DockerAPI1.24+\)开发出的Docker管理工具，它提供了最基本的基于Docker的管理功能，目的是能够方便广大Docker初学者以及Docker管理员能够快速的进行Docker容器的管理和维护。通过Dockerfly的swarm管理，用户可以轻松的构建起一个基于Docker原生的集群系统。

#### Dockerfly的使用

通过Dockerfly可以实现APS容器运行状态的Web管理以及维护。如需使用Dockerfly，请先在APS所在的主机上以容器的形式安装Dockerfly，然后设置物理主机端口到该容器端口的端口映射，以便用户可以在外网通过浏览器成功访问Dockerfly。

系统基本指标（如系统CPU、内存、磁盘、网络等）监控管理可采用方案一中的nmon工具。

##### 登录主界面

在浏览器地址栏中输入“[http://APS\_IP：映射后的物理端口号”](http://APS_IP：映射后的物理端口号”)并按回车键，打开Dockerfly登录界面。用户名和密码请咨询我们的工程师，用以登录。

![](blob:file:///dcc4839a-37ff-4231-9ec7-c90f398e858a)

##### 主界面内容

左侧导航栏中包括Swarm、Node、Service、Task、Container、Network、Volume、Images和Setting等标签页。

首页中可看到当前主机的基本配置。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](blob:file:///10e05c84-1064-40c1-81f8-829fd3994fd4)

##### 查看所有容器

点击左侧Container标签页，右侧页面会将这台主机上所有的container显示出来。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](blob:file:///a20e1d3f-71cb-49ac-ac3c-e23354734321)

##### 查看所有image

点击左侧Image标签页，右侧页面会将这台主机上所有的image显示出来。如需切换主机，请点击左侧导航栏中的Setting标签页，然后添加主机，在主机列表中点击Operation列的“Default”按钮即可。

![](blob:file:///a8983305-30be-4201-b9eb-acc6b2178416)

##### 设置（Setting）

Setting页面中主要包括用户管理、添加主机、修改密码和registry（即添加SaaS用户）功能。

![](blob:file:///e7068841-2427-4f8e-963c-791f4168fc0c)


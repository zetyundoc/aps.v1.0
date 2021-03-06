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
   1. `#cd /usr/bin`
   2. `#chmod u+x nmon16g_x86_sles114`
4. 启动nmon。
   1. `#cd /usr/bin`
   2. ./`#chmod u+x nmon16g_x86_sles114`

启动后，终端显示如下所示的界面。

![](/maintenance/fig/fig_001.png)

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

![](/maintenance/fig/fig_002.png)

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

| 命令类型 | 命令关键字 |
| :--- | :--- |
| 容器生命周期管理 | run、start、stop、restart、kill、rm、pause、unpause、create、exec |
| 容器操作 | ps、inspect、top、attach、events、logs、wait、export、port |
| 容器rootfs命令 | commit、cp、diff |
| 镜像仓库 | login、pull、push、search |
| 本地镜像管理 | images、rmi、tag、build、history、save、import |
| 版本信息 | info、version |

#### 容器相关命令示例

**容器及镜像管理命令**

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

* 在docker index中搜索image（search）

```
docker search [OPTIONS] TERM
```

* 从docker registry server中下拉image或repository（pull）

```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

* 推送一个image或repository到registry（push）

```
docker push [OPTIONS] NAME[:TAG]
```

* 将一个容器固化为一个新的image

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

**查看服务状态命令：**

* 查看系统上的docekr服务是否开启

```
ps    -ef    | grep docker
```

* 查看所有容器的运行状态

```
docker    ps  -a
```

* 查看容器中运行的进程信息

```
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```

* 查看本地所有images

```
docker    images
```

### Dockerfly

Docker容器管理工具概述

APS维护也就是对Docker容器的管理。用户可以通过多种监控管理工具来管理Docker容器，但是这些工具各有优缺点。

常见的Docker监控管理工具如下：

* Kubernetes：可跨计算机集群部署容器，不需要开启网络端口或执行其他操作，容器可相互联系。

* Dockersh：让多个用户可以连接到某个主机，每个用户都运行自行选择的单独的Docker容器所生成的外壳。

* DockerUI：通过Web浏览器的命令行来管理的任务。

* Shipyard：简化对横跨多个主机的Docker容器集群进行管理。

  通过Web用户界面，您可以浏览相关信息，例如您的容器正在使用处理器和内存资源的情况、正在运行的容器列表等，还可以检查所有集群上的事件日志。

* Kitematic：成为基于OSX的编程员们手里一款实用的桌面环境开发工具。

* Logspout：管理容器中应用的日志。

* Autodock：自动化Docker。

* Docker-in-Docker：在Docker中运行Docker。

* Heroku-Docker：用命令行将其Heroku项目转换成Docker镜像。

* DockerNodeTester：用于测试Docker。

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

#### Dockerfly的安装

通过Dockerfly 可以实现APS容器运行状态的Web管理以及维护。如需使用Dockerfly，请先在APS所在的主机上安装Dockerfly，然后设置物理主机端口到该容器端口的端口映射，以便用户可以在外网通过浏览器成功访问Dockerfly。

1. 获取Dockerfly镜像。  
   `# docker pull helyho/dockerfly`

2. 启动dockerfly容器。  
   `# docker run --name dockerfly --restart=always -d -v /var/run/docker.sock:/var/run/docker.sock  -p 2735:2735 -p 28083:28083 helyho/dockerfly:latest dockerfly`  
   dockerfly依赖于socat，因此在启动dockerfly前请先确保系统中已安装socat。

3. 在浏览器中输入“[http://&lt;IP&gt;:28083](#)”，其中IP为dockerfly所在服务器的IP地址。

以容器形式安装的dockerfly默认连接到了docker，因此可以直接进行镜像、容器等的管理。默认用户名/密码为admin/1234。

#### Dockerfly的使用

##### 登录系统

在浏览器中访问“[http://&lt;IP&gt;:28083](http://<IP>:28083，其中IP为dockerfly所在服务器的IP地址。进入Dockerfly登录界面，通过用户名和密码登录。)”，其中IP为dockerfly所在服务器的IP地址。进入Dockerfly登录界面，通过用户名和密码登录。

![](/maintenance/fig/fig_003.png)

##### 系统登录界面简介

左侧导航栏中包括&lt;Swarm&gt;、&lt;Node&gt;、&lt;Service&gt;、&lt;Task&gt;、&lt;Container&gt;、&lt;Network&gt;、&lt;Volume&gt;、&lt;Image&gt;和&lt;Setting&gt;等页签。

在页面左侧的导航栏中单击&lt;Summery&gt;页签，即可看到当前主机的基本概况，包括CPU核数、已使用内存、容器和镜像的个数等。

![](/maintenance/fig/fig_004.png)

##### 管理容器

在页面左侧的导航栏中单击&lt;Container&gt;页签，即可显示当前主机中的容器列表。在该列表中可以查看各容器的端口映射、运行状态等，也可以实现对容器的启停以及删除操作。

![](/maintenance/fig/fig_005.png)

##### 管理镜像

在页面左侧的导航栏中单击&lt;Image&gt;标签页，即可显示当前主机中的镜像列表。在该列表中可以查看各镜像的名称、创建时间、大小等，也可以实现对镜像的删除等操作。

![](/maintenance/fig/fig_006.png)

##### 设置（Setting）

&lt;Setting&gt;页面中主要包括用户管理、添加主机、修改密码和registry（即添加SaaS用户）功能。

Dockerfly支持跨节点的容器管理，即当多台主机上的Docker中都启用Dockerfly容器时，可以在其中一个Dockerfly管理界面实现对所有Docker的管理。要实现该功能，只要将不同的主机节点添加到Dockerfly中即可，在&lt;Setting&gt;页签下，单击“Add a host”即可添加新的Docker主机，单击主机所在行的“Default”，即可在其它页签下实现对该Docker中的容器、镜像等的管理。

![](/maintenance/fig/fig_007.png)


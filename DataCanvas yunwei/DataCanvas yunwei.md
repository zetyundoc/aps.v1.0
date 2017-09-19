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










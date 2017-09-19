# DataCanvas EDS安装手册

## 准备工作

### 系统要求

在开始安装DataCanvas EDS之前，请确保您的系统符合下列要求。

#### 操作系统要求

不能在已经安装了数据平台相关产品的主机上再安装DataCanvas EDS，例如HDP、CDH、Apache Hadoop等等。

    说明：推荐在新安装的操作系统里安装DataCanvas EDS产品。

#### 支持的操作系统

* Red Hat Enterprise Linux \(RHEL\) 7.x
* CentOS 7.x \(64-bit\)
* SUSE Linux Enterprise Server \(SLES\) 11 SP3

其中，CentOS7.1镜像文件下载地址：  
[http://vault.centos.org/7.1.1503/isos/x86\_64/CentOS-7-x86\_64-Minimal-1503-01.iso](http://vault.centos.org/7.1.1503/isos/x86_64/CentOS-7-x86_64-Minimal-1503-01.iso)

#### 浏览器要求

DataCanvas EDS管理界面是基于浏览器的Web应用程序运行的，因此您需要使用能够运行浏览器的终端（例如物理主机和虚拟机）才能访问DataCanvas EDS管理界面。支持访问的浏览器如下：

* Mozilla Firefox现代浏览器
* Google Chrome现代浏览器
* Microsoft Edge
* Safari 8.0及以上版本

#### 防火墙要求

DataCanvas EDS产品在安装期间，管理节点与计算节点需要进行通信，因此必须保证某些端口必须是开放且可用的，最简单的方法是暂时关闭防火墙。DataCanvas EDS产品在安装过程中自动关闭了系统防火墙。若DataCanvas EDS集群安装在内网环境，我们推荐永久性关闭防火墙服务；若DataCanvas EDS集群安装在外网或者公有云环境，我们建议根据如下列表开放相应端口。

![](/assets/端口配置.PNG)

说明：仅要求3台基础节点的端口开放。

#### 节点硬件要求

DataCanvas EDS安装需要3个基础节点作为管理节点和多个从节点作为计算节点，而且必须至少具有3个基础节点才能正常安装DataCanvas EDS产品。作为核心的管理节点，我们建议这3个基础节点的CPU、内存、硬盘等资源高于其它从节点。一个大数据平台的资源越多意味着它能够存储和计算的能力越强，因此建议DataCanvas EDS产品的各节点配置越大越好。各节点的资源配置说明如下：

* 推荐资源配置

![](/assets/推荐资源配置.PNG)

* 最小资源推荐配置

    DataCanvas EDS安装最少需要3个节点。最小资源配置是指3台基础节点能够正常安装DataCanvas EDS集群的最低要求，该配置下的环境仅可以作浏览使用或者用来配置简单的功能，不可以作为性能测试环境或者POC环境，更不能应用于生产环境中。

![](/assets/最小资源配置.PNG)

#### 云主机

DataCanvas EDS产品支持本地服务器、私有云和公有云3种安装环境。DataCanvas EDS产品支持在下列公有云云主机中部署，如下表所示：



### 环境准备

使用DataCanvas EDS安装包自动化部署集群，需要准备以下部署环境，请严格按照文档操作以保证能够部署成功。

#### 设置无密码SSH登录

在DataCanvas EDS的安装过程中，需要通过BASE01基础节点将DataCanvas EDS自动部署到集群中其他管理节点和计算节点上，因此需要在BASE01主机和集群中所有其他主机之间对root用户设置无密码的SSH连接。

设置无密码的SSH连接，在BASE01节点上按照以下步骤操作：

1\) 在BASE01主机上生成公有和私有SSH密钥;

```
  [root@localhost ~]# ssh-keygen 
```

2\) 把BASE01主机的公钥复制到远程机器的authorized\_keys文件中\(包括BASE01主机\);

```
  [root@localhost ~]# ssh-copy-id <remote_host>

 说明：
   * 其中<remote_hosts>是指包括BASE01主机在内的EDS集群内所有主机的IP地址，而不是主机名。
   * 执行该命令后需要输入yes并回车，然后根据提示输入远程主机的SSH密码。
```

3\) 把BASE01主机的.ssh目录复制到远程机器的/root目录下\(不包括BASE01主机\)。

```
  [root@localhost ~]# scp -r /root/.ssh <remote_host>:/root

  说明：其中<remote_hosts>代表除了BASE01主机之外的EDS集群内其他主机的IP地址，而不是主机名。
```

#### 检查网络和主机名

* 查询主机IP地址

  使用ip addr命令分别查询每个主机的IP地址，接下来在DataCanvas EDS平台的安装过程中需要配置这些IP地址\(每个IP地址与主机上指定的网卡相绑定\)，而不是配置弹性IP、映射IP或者虚拟IP，否则安装过程将会出现异常。

  ```
  [root@localhost ~]# ip addr
  ```

* 检查主机是否在同一局域网

  DataCanvas EDS平台要求集群内所有主机必须在同一局域网内且可以正常通信，否则EDS的安装和使用都会受到影响。

  ```
  [root@localhost ~]# ping <remote_host>

  说明：其中<remote_hosts>是指EDS集群内的所有主机的IP地址，并且这个IP地址一定是属于主机的某个网卡的，而不是弹性IP、映射IP或者虚拟IP。
  ```

* 设置主机名

  使用`hostnamectl`命令在集群中每个主机上设置主机名。例如:

  ```
  [root@localhost ~]# hostnamectl set-hostname <fully.qualified.domain.name>

  说明：其中<full.qualified.domain.name>是指完全合格域名/全称域名，由主机名+域名组成。故而设置主机名时，最好加上域名，如base1.zetyun.com，而不是直接使用base1作为主机名。
  ```

#### 检查磁盘

* 检查主机磁盘

  使用fdisk命令在集群中的每个主机上查看磁盘数量以及每个磁盘的容量大小，确定每个主机上数据磁盘或分区\(不包括系统盘或分区\)的数量、以及每个磁盘或分区的容量大小，方便后续进行磁盘的格式化和挂载。例如:

  ```
  [root@base1 ~]# fdisk -l
  ```

* 挂载主机磁盘

 安装DataCanvas EDS需要满足磁盘的配置要求，因此用户需要按照配置要求来挂载磁盘。假设使用`fdisk -l`命令查到每个主机上分别有2个大小为1TB的磁盘需要挂载，如/dev/sdb和/dev/sdc。挂载步骤如下：

  1\) 格式化磁盘为ext4格式。

  ```
  [root@base1 ~]# mkfs -t ext4 /dev/sdb
  ```

  2\) 创建挂载点目录。

  ```
  [root@base1 ~]# mkdir /mnt/disk01

  说明：请记录好挂载点目录与磁盘或分区文件名的对应顺序，以避免混乱。
  ```

  3\) 把磁盘或分区挂载到刚才创建的本地挂载点目录。

  ```
  [root@base1 ~]# mount /dev/sdb /mnt/disk01
  ```

  4\) 查看挂载结果:

  ```
  [root@base1 ~]# df -h
  ```
可以看到/dev/sdb磁盘文件已经挂载到了/mnt/disk01目录上，请用同样的步骤挂载/dev/sdc磁盘到本地挂载点目录，并在集群内所有主机上挂载自己所有的数据磁盘或分区。

* 挂载信息写入fstab文件

  把挂载信息写入/etc/fstable是非常重要的一步，否则主机重启之后之前挂载磁盘的操作会失效。  
  使用vi命令打开/etc/fstable文件，在文件最后写入磁盘挂载信息。例如/dev/sdb和/dev/sdc磁盘的挂载信息如下:

  ```
  /dev/sdb    /mnt/disk01    ext4    defaults    0  0
  /dev/sdc    /mnt/disk02    ext4    defaults    0  0

  说明：不能复制/etc/fstab文件到集群中的其它主机。即使每个主机的磁盘挂载设置（例如磁盘名称和挂载点目录）都是相同的也不能复制，因为fstab文件上面的磁盘或分区文件是通过唯一识别码UUID挂载的，而每个主机的系统分区UUID是不一样的，因此配置完1个节点后不能将/etc/fstab文件复制到其它所有节点。
  ```

#### 解压安装包

您无论是将DataCanvas EDS安装到集群中的3个节点还是更多的节点，请把获取到的DataCanvas EDS安装包先上传到集群内的BASE01主机上，该节点也会作为Ambari节点。

安装包上传到BASE01节点的/root目录后，在该目录下将其解压：

```
# tar –zxvf eds-deploy-v2017xx.release.tgz

说明：DataCanvas EDS安装包必须上传并解压到BASE01上。如果解压出现问题，请查看BASE01主机的日期和时间是否和当前时间和日期一致，如果不一致，修改为当前的时间和日期即可。
```

## 安装EDS集群

### 修改配置

DataCanvas EDS安装包解压到BASE01主机上之后会出现eds-deploy目录。在安装DataCanvas EDS前，请先进入该目录配置集群中所有主机的IP地址和全局变量。

#### 配置IP地址

使用vi命令打开eds-deploy/conf/hosts文件，需要配置\[eds-allips\]、\[eds-ams\]、\[eds-master\]和\[eds-slaves\]4个IP组。

```
注意：
   * 下面配置的IP地址应该是在主机上能够使用ip addr命令查看到的IP地址，且必须是绑定到某个网卡上的IP地址，而不是弹性IP、映射IP或虚拟IP地址。
   * IP组名称与IP地址之间不能有换行。两个IP地址属于同一个组的时候，两者之间也不能有换行。两个IP组之间用换行分隔，IP组名称不允许修改，也不允许在该文件中定义新的IP组。
```

* IP组\[eds-allips\]需要配置集群所有主机的IP地址

  该IP组配置是有顺序的，前3个IP分别是BASE01、BASE02和BASE03的IP地址，后面依次是Slaves从节点的IP地址。例如：

  ```
  [eds-allips]
  BASE01_IP
  BASE02_IP
  BASE03_IP
  Slave1_IP
  Slave2_IP
  …
  ```

* IP组\[eds-ams\]需要配置BASE01节点的IP地址

  该IP组定义Ambari Server的安装节点。通常我们在BASE01节点上安装Ambari服务，因此需要配置成BASE01主机的IP地址。

* IP组\[eds-master\]需要配置3个基础节点的IP地址

  该IP组定义集群中3个管理节点的IP地址，我们通常使用3个基础节点作为管理节点，因此需要配置3个基础节点的IP地址，并且按照BASE01、BASE02、BASE03顺序配置。因此，这个IP组与\[eds-allips\]组的前3个IP地址对应。例如：

  ```
  [eds-master]
  BASE01_IP
  BASE02_IP
  BASE03_IP
  ```

* IP组\[eds-slaves\]需要配置所有Slave从节点的IP地址

  该IP组定义集群中所有slave节点的IP地址\(不包括3个基础节点的IP\)，请按照\[eds-allips\]组slave节点IP的顺序配置。  
  如果3个基础节点既要作为管理节点又要作为slave节点，也无需在\[eds-slaves\]组配置3个基础节点的IP地址，只需在cluster\_setting文件中把master1\_as\_slave、master2\_as\_slave和master3\_as\_slave三个变量设置为“True”即可。

  ```
  [eds-slaves]
  Slave1_IP
  Slave2_IP
  ..

  说明：如果集群中只有3个节点，IP组[eds-slaves]不配置任何IP地址，只需要在该IP组下面直接换行，然后在cluster_setting文件中把master1_as_slave、master2_as_slave和master3_as_slave三个变量设置为“True”即可。此时3个基础节点既作为管理节点又作为slave节点，这也是最小的EDS集群。
  ```

#### 配置安装方式

使用vi命令打开eds-deploy/conf/cluster\_setting文件，需要配置mount\_point、aps\_host、aps\_port、eds\_ha、master1\_as\_slave、master2\_as\_slave和master3\_as\_slave7个变量。

* mount\_point 变量

  定义EDS集群数据的根目录，实际生产环境中要求必须有足够的存储空间，通常将其配置为一个数据磁盘的挂载点目录，如/mnt/disk01/eds。该变量配置的目录在安装过程中会自动创建。

* aps\_host变量

  定义APS节点的IP地址，即把系统默认的IP地址修改为要安装APS的主机的IP地址，有且只有1个IP地址。若不确定APS的IP地址可暂不配置。

* aps\_port变量

  自定义APS平台的端口，默认为3002。

* eds\_ha变量

  定义DataCanvas EDS是否自动进行各功能的HA部署，默认值为“False”。产品暂不支持HA自动部署，即变量设置为“True”时不生效。如需要配置HA功能，请手动进行。

* master1\_as\_slave变量

  当BASE01节点需要安装Slave组件时，可以把该变量配置为“True”，让 BASE01节点既作为管理节点又作为计算节点。否则把该变量配置为“False”，让BASE01节点只作为管理节点角色。

* master2\_as\_slave变量

  当BASE02节点需要安装Slave组件时可以把该变量配置为“True”，让 BASE02节点既作为管理节点又作为计算节点。否则把该变量配置为“False”，让BASE02节点只作为管理节点角色。

* master3\_as\_slave变量

  当BASE03节点需要安装Slave组件时可以把该变量配置为“True”，让 BASE03节点既作为管理节点又作为计算节点。否则把该变量配置为“False”，让BASE03节点只作为管理节点角色。

  示例配置如下：

  ```
  mount_point: /mnt/disk01/eds
  aps_host: 10.0.0.11
  aps_port: 3002
  eds_ha: False
  master1_as_slave: True
  master2_as_slave: True
  master3_as_slave: True

  说明：每个变量名后面有个冒号(:)，并且冒号后面有1个空格，空格之后才是变量的值。
  ```

### 运行安装脚本

使用root用户SSH登录到BASE01节点，进入/root目录下配置好的eds-deploy目录，执行以下命令运行安装脚本：

```
[root@base1 eds-deploy]# ./bin/eds.sh -m setup
```

回车之后，开始在EDS集群内所有主机上自动运行安装脚本，包括脚本软件、JDK、HTTP、系统检查等。

```
说明：执行上面的命令运行安装脚本完成之后会自动重启集群内所有主机，以使配置生效。待所有主机启动成功之后再继续安装。
```

### 运行HDP脚本

待所有主机正常重启完毕后，使用root用户SSH登录到BASE01节点，进入/root目录下配置好的eds-deploy目录，执行以下命令自动运行HDP脚本：

```
[root@base1 eds-deploy]# ./bin/eds.sh -m hdp
```

回车之后，开始安装Ambari、HDP集群等组件，待Ambari提交HDP安装模板之后，会在命令窗口打印Ambari登录信息，您可在浏览器上访问Ambari地址查看HDP集群安装进度。

```
说明：在Ambari界面上查看到HDP所有任务都成功执行到100%之后，说明HDP集群安装完成，然后回到<Dashboard>界面查看左侧导航栏，若有个别组件处于”stop”状态，可手动将其启动。
```

待HDP集群安装完后再进行下一步MPP的安装，并且在安装MPP组件之前，必须保证HDFS服务是正常可用的。

### 安装MPP

待HDP集群安装完毕之后，使用root用户SSH登录到BASE01节点，进入/root目录下配置好的eds-deploy目录，执行以下命令自动安装MPP数据库：

```
[root@base1 eds-deploy]# ./bin/eds.sh -m mpp
```

回车之后，开始安装EDS Services、MPP数据库等组件。至此，DataCanvas EDS集群已经安装完成。

### 验证EDS

为了保证DataCanvas EDS集群安装完毕之后可以正常使用，需要进行以下检查和验证。

#### 验证Ambari服务
Apache Ambari是一种开源的、基于Web的工具，支持对Apache Hadoop集群的配置、管理和监控。DataCanvas EDS采用Apache Ambari来进行组件的运维管理。

在浏览器地址栏中输入“http://BASE01_IP：8080” 并按回车键，打开Ambari登录界面。输入默认用户名admin和默认密码admin，登录Ambari。在监控界面查看是否出现服务停止、异常等告警信息。若发现部分服务处于“stop”状态，这可能是所有服务一起启动时网络通信不畅导致的，可以手动启动它，然后观察是否正常。若发现服务异常，可以根据告警信息提示尝试解决。

#### 验证Supervisor服务

在旧版本的EDS安装程序中，EDS Services是使用Supervisor进行管理的，现在我们主要验证这些service是否正常运行，可以使用Supervisorctl命令进行查看。使用root用户SSH登录到BASE01节点，执行以下命令查看Supervisor管理的所有服务状态信息：

```
[root@base1 ~]# /opt/anaconda2/bin/supervisorctl status
```

回车之后，当查看到所有服务都是“Running”状态即为正常，若发现有没有启动或者异常的服务，可以手动执行supervisor命令启动它。启动a3服务示例：

```
[root@base1 ~]# /opt/anaconda2/bin/supervisorctl start a3

说明：Supervisor还可以使用restart命令重启某个服务，使用reload命令重启所有被Supervisor管理的服务。
```

#### 验证MPP数据库

MPP分布式数据库验证方式是使用psql命令或者navicat客户端连接数据库，示例如下:

```
[root@base1 ~]# psql -h BASE01 –p 25432 –U gpadmin –d postgres

说明：其中BASE01可以是主机名或者IP地址，若使用客户端远程连接只能使用BASE01主机的IP地址。
```

#### 验证NTP服务

DataCanvas EDS产品部署完成后，集群内就已经配置好了本地时钟自动同步，每隔2分钟集群内的所有主机都向BASE01主机同步时间。

验证方式是在除BASE01主机之外的所有主机上分别执行如下命令进行时间同步，如果返回同步时间偏差表示NTP服务工作正常。

```
[root@base2 ~]# ntpdate base1.zetyun.com(或BASE01主机的IP地址)

说明：若BASE01主机上NTP服务异常，可以手动重启它。
```

## FAQ

**常见问题解答：**

**Q1.DataCanvas EDS某一步安装失败怎么办？**

先根据安装日志查看异常信息，然后判断进行到哪一个task出现failed。根据异常原因解决问题之后，再继续从当前failed处进行安装。

**Q2.如何只安装Ambari服务？**

执行完下面的命令安装依赖之后，再进行Ambari安装。

```
[root@base1 eds-deploy]# ./bin/eds.sh -m setup
```

安装Ambari命令如下：

```
[root@base1 eds-deploy]# ansible-playbook -i conf/hosts data/ambari.yml
```

Ambari安装完之后，使用浏览器访问BASE01的IP地址，端口8080，默认用户名/密码是：admin/admin，登录到Ambari管理界面。

**Q3.如何只安装HDP平台组件？**

若只安装Hortonworks HDP平台而不包括EDS Service，只需要执行setup和hdp两条命令即可，需要注意执行顺序。

**Q4.如何只安装HDP组件+EDS Services服务?**

按照Q3的解答执行完之后，要确认HDFS服务正常，然后再执行下面的命令：

```
[root@base1 eds-deploy]# ansible-playbook -i conf/hosts data/ eds-service.yml
```

**Q5.如何把本地磁盘添加到HDFS？**

请参考附录<a href="DataCanvas install/EDS install/EDS install.md#HDFS">HDFS磁盘扩容</a>小节。

**Q6.如何重启Ambari服务？**

Ambari服务包括Ambari-server和Ambari-agent，重启方式如下：

```
[root@base1 ~]# systemctl restart ambari-server
```

或者

```
[root@base1 ~]# ambari-server restart
```

**Q7.Hive MetaStore数据库连接地址是什么？**

默认连接地址：BASE02_IP，端口：10000，默认用户名/密码是hive/hive。
**
Q8.MPP数据库的连接地址是什么？**

地址:BASE01_IP，端口：25432，用户名：gpadmin，密码为空，DB:postgres。

**Q9.如何访问Hive数据库?**

```
[root@base1 ~]# beeline -u jdbc:hive2://BASE02_IP:10000/default -n hive -p hive
```
默认没有开启用户名和密码验证，因此只要地址和端口正确即可访问Hive。

**Q10.EDS Services的数据库如何连接？**

```
[root@base1 ~]# psql -h BASE02_IP -p 5432 -U datacanvas -d postgres
```
默认密码：datacanvas

**Q11.开发程序如何连接HDFS读取文件？**

hdfs://BASE01_IP:8020。
默认的HDFS超级用户是hdfs。

**Q12.Yarn的连接地址是什么？**

在yarn.resourcemanager.address配置项后输入BASE02_IP:8050，在yarn.resourcemanager. scheduler.address配置项后输入BASE02_IP:8030。

**Q13.如何查看各个组件分布在哪些主机上？**

使用浏览器访问Ambari地址，登录监控界面在<hosts>选项卡上可以查看各个组件分别分布在哪些主机上。

**Q14.Zookeper集群的连接地址是什么？**

通过自动部署程序安装的EDS集群，Zookeeper被默认安装在3个基础节点上，地址如下：
*  BASE01_IP:2181
*  BASE02_IP:2181
*  BASE03_IP:2181

**Q15.如何在Ambari上配置NameNode 、ResourceManager、HBase Master、HiveServer2等的高可用性(HA)功能？**

请参考附录<a href="/EDS install.md#ambari">Ambari操作文档</a>小节。

需要注意的是，如果要配置NameNode的HA功能，建议在刚安装完HDP之后就进行。

**Q16.如何重启PXF服务？**

在集群内所有主机上执行以下命令先结束PXF进程，然后再启动PXF服务。
停止PXF进程：

```
ps -ef | grep pxf | gawk ‘{print $2}’ | xargs -kill -9
```

启动PXF服务：


```
/etc/init.d/pxf-service start
```

**Q17.如何重启HAWQ数据库？**

HAWQ数据库依赖PXF服务。在启动HAWQ数据库时，请先确保PXF服务正常工作。启用或停止HAWQ数据库，必须在BASE01主机上通过gpadmin用户进行操作，命令如下：

```
[root@base1 ~]# su gpadmin
[root@base1 ~]# /home/gpadmin/hawqtools.sh restart
```
另外，您还可以使用start或stop命令来启动或停止HAWQ数据库。

**Q18.如何在Ambari上安装本地源中没有的HDP组件？**

DataCanvas EDS安装包里面不是完整的HDP本地源，通过安装包自动部署的HDP集群只包括了大部分的组件，如果您希望安装HDP的其他组件，可以自行下载对应版本的全量HDP本地源。全量的HDP本地源配置好之后，还需要添加系统版本对应的系统本地源。

## 附录

### <a name="ambari">Ambari操作文档</a>

关于Ambari的具体操作指导，请参考下面的配置文档链接：
https://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-operations/ bk_ambari-operations.pdf

另外，用户可根据需要在Ambari中配置一些组件的HA功能。可以配置HA功能的HDP组件如下：

1、	NameNode High Availability
2、	ResourceManager High Availability
3、	HBase High Availability
4、	Hive High Availability
5、	Oozie High Availability
6、	Apache Atlas High Availability
7、	Enabling Ranger Admin High Availability



### <a name="HDFS"> HDFS磁盘扩容</a>

由于服务器需要存储大量的数据，一般需要挂载多个磁盘。如果您在安装DataCanvas EDS前仅挂载了满足安装需求的磁盘数量，那么在部署完成后可以在Ambari监控界面手动把磁盘继续添加到HDFS中以扩大HDFS的存储空间。 

下面举例介绍如何给DataNode添加多个磁盘。

####挂载磁盘到DataNode 

假如每台主机上还有5个磁盘需要挂载，名称分别为/dev/sdd、/dev/sde、/dev/sdf、/dev/sdg、/dev/sdh。具体挂载步骤如下：

  1）格式化磁盘或分区。
    # mkfs –t ext4 /dev/sdd

  2）创建挂载点目录。
    # mkdir /mnt/disk03

  3）挂载磁盘。
    # mount /dev/sdd /mnt/disk03

  4）写入配置文件。
    # echo “/dev/sdd  /mnt/disk03  ext4  defaults  0 0” >>/etc/fstab

至此，挂载已经完成，其他磁盘挂载方法同上。

需要注意的是，由于某个节点上的/dev/sdh磁盘会被DataCanvas APS挂载到/mnt/datacanvas目录，为了统一挂载点目录，需要创建一个软连接到/dev/sdh磁盘，命令如下：

    # ln -s /mnt/datacanvas/disk07 /mnt/disk07

####修改HDFS配置 

1、	登录Ambari WebUI，选择左侧导航栏中的“HDFS”页签，然后单击“Configs”，找到“DataNode”配置框。
默认只有1个DataNode，目录为: /mnt/disk01/eds/hadoop/hdfs/data。
![](/assets/配置DataNode.png)

2、	添加DataNode数据目录，然后点击右上角的“Save”按钮。
在“DataNode”配置框中，基于各个磁盘的挂载点目录，依次设置并添加DataNode目录，如图所示。
![](/assets/添加数据目录.png)

3、	点击“Restart”右侧的小三角，选择“Restart All Affected”，重启HDFS服务。
![](/assets/重启HDFS服务.png)

####检查HDFS空间

配置完成后，进入Ambari WebUI查看HDFS服务，存储空间已经发生了变化。 
![](/assets/查看HDFS服务.png)
   
     


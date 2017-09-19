# EDS运维管理
DataCanvas EDS采用Apache Ambari来进行组件的运维管理。Apache Ambari是一种开源的、基于Web的工具，支持对Apache Hadoop集群的配置、管理和监控。

### 总览
登录DataCanvas EDS运维界面即Ambari之后，首先看到的是Dashboard界面。该页面可以查看EDS集群每个组件的状态图，比如HDFS节点数/可用节点数，CPU、内存使用情况等信息。如果EDS某个组件出现异常情况会在总览界面显示红色异常图标，如下图所示：

![](/DataCanvas yunwei/运维总览.png)

    说明：DataCanvas EDS运维入口有两个，可以在APS平台界面的集群监控上点击”运维入口”按钮，也可以直接在浏览器输入EDS的监控界面地址进入。

### 服务管理
点击上方导航栏的Service页签，或者单机左侧导航栏的某个服务组件，可以查看某个服务的状态，如下图所示：

![](/DataCanvas yunwei/服务管理.png)
    
    说明：当服务出现告警信息的时候就会显示红色的alert，点击alert可以查看告警详细信息，如上图所示。

#### 服务启停 

启停HBase服务示例如下：

点击左侧“HBase”服务进入服务摘要界面，在“Service Actions”下拉菜单中选择“stop”可以停止该服务。

![](/DataCanvas yunwei/服务启动.png)
    
    说明：服务停止后界面上会出现该服务的异常告警，这些告警是因为其他服务不能正常的与被停止的服务通信造成的，待被停止的服务正常启动之后告警就会消失。

#### 服务配置 

配置HBase服务示例如下：

点击左侧“HBase”服务进入服务摘要界面，选择“Configs”标签页进入HBase服务配置界面。

![](/DataCanvas yunwei/服务配置.png)

修改完服务配置之后点击“Save”进行保存，然后界面提示重启关联的服务，再点击重启即可。

    说明：选择“Advanced”高级配置，可修改更多HBase服务的配置项。

#### 服务监控

在服务摘要界面可以查看监控信息，例如HBase服务监控CPU、网络、打开链接数、磁盘访问等信息，如下图所示：

![](/DataCanvas yunwei/服务监控.png)

    说明：服务监控的CPU、Network、Disk都是整个集群上的资源监控，因为HBase服务是分布式的有多个节点，所以监控的是集群资源。

### 节点管理
节点管理包括启动、停止节点上的所有服务组件，节点删除、节点添加以及节点监控等功能。

#### 添加节点
点击上方导航栏的“hosts”页签，显示节点列表。

![](/DataCanvas yunwei/节点列表.png)

点击“Actions”下拉菜单，选择“Add New Hosts”进入添加节点界面。下面以添加base4.zetyun.com节点为例：

![](/DataCanvas yunwei/添加节点.png)

在“Target Hosts”框中输入要添加节点的hostname，每个节点占1行。

在“Host Registration Information”框中输入集群节点的私钥，也可以点击“选择文件”按钮上传id_rsa文件。

默认使用root用户添加节点，点击“Register and Confirm”，确认并开始检查新添加的节点，如下图所示：

![](/DataCanvas yunwei/正在检查节点.png)

![](/DataCanvas yunwei/检查节点成功.png)

节点检查通过之后显示绿色，点击“Next”继续选择服务组件进行安装，添加节点成功之后会在节点列表显示新添加的节点。

#### 节点启停 
在节点列表界面点击某个节点之后，点击“Host Actions”下拉菜单，选择“Start All Components”启动节点上的所有服务，选择“Stop All Components”停止节点上的所有服务。

![](/DataCanvas yunwei/节点启动页面.png)

点击“Started”下拉框可以选择<Stop>停止某个服务。

    说明：整个节点上的服务停止之后会出现较多的红色告警信息，待服务正常运行之后告警消失。如果被停止服务的节点上运行着重要的角色，可能导致整个服务组件无法使用，如namenode停止导致HDFS无法使用，请谨慎操作。

#### 节点删除 

在节点列表界面，点击将要删除的节点进入节点详情界面，点击“Host Actions”下拉菜单，选择“Delete Host”即可删除节点。

![](/DataCanvas yunwei/节点删除界面.png)

    说明：在删除节点之前需要停止该节点上的所有服务。

#### 节点监控 
在节点列表界面点击某个节点进入该节点的摘要界面，可以查看该节点的CPU、Memory、Network、Disk等监控信息。

![](/DataCanvas yunwei/节点监控界面.png)

    说明：点击监控图标可选择查看历史监控信息，同时还可以把监控信息导出文件。

### 告警管理
点击上方导航栏的<Alerts>页签进入告警管理界面，如下图所示。

![](/DataCanvas yunwei/告警管理界面.png)

告警管理界面可以对历史告警进行查询，也可以对某个告警进行关闭。










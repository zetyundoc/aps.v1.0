# 工具安装及应用

DataCanvas提供的EDS API小工具，便于用户简单快捷的调用EDS API。
<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >说明：小工具由Python编写而成，所以首先需要一个Python环境。</p>

### 安装Python

如果未安装Python，我们建议Anaconda的Python版本。用户可通过[https://www.continuum.io/downloads](https://www.continuum.io/downloads)下载不同操作系统的Python版本并根据网站提示步骤进行安装操作。

### 安装EDS API小工具

Then install following two dependencies:

  pip install requests click prettytable

### EDS API小工具的应用

#### 服务及端口

* Ingestion Manager Service：Port 8000. 
 
该服务用于管理事件流数据在HDFS中的存储。

* Schema Manager：Port 7080.

该服务用于事件表的schema管理；Schema会随时更新。

* Query Router：Port 18789. 

该服务用于不同数据库的多引擎查询。目前，我们支持HIVE和MPP引擎。

* Bulk Import Service：Port 7090. 

该服务用于启动批量加载功能。
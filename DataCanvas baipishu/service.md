# 产品服务及优势

### 产品竞争优势

* **分布式并行计算（可扩展，PB级存储）**

  DataCanvas EDS可帮助企业建立数据湖对数据集中存储，可弹性创建删除集群并行处理数据。并具有完整、智能的自动化运维，实现了数据的集中存储与弹性计算，集群资源可随时启停，资源分配动态调整，保证存储和分析的统一性和高效性。

  将企业原有架构迁移到基于X86的大数据分布式计算架构之上，确保海量数据存储的高可扩展性和运算的高效性。

* **统一多引擎融合架构**

  DataCanvas EDS 采用新一代超融合架构，融合了Hadoop、MPP数据库各自的处理优势， 提供兼容ANSI SQL国际标准的SQL-on-Hadoop查询引擎。实现了PB级数据的高速存储和扩容，亿级数据的秒级查询和流数据的实时处理等功能。 通过不同场景（内存快速处理，高负载、时效要求低，高并发、实时、随机读写，非实时要求不高、大批处理的工作负载，结构化数据的实时计算）派发到不同执行引擎进行处理充分发挥各个引擎的优势。

  * 统一性：权限、存储、资源、事务在同一框架下
  * Docker容器化：融合多种开发语言环境和逻辑，提供应用层面隔离，解决编程对库依赖的问题
  * 支持版本管理
  * 支持可扩展性、多元性：建立多镜像，支持多元化数据应用
  * 多角色使用：支持权限分配、管理，降低用户使用门槛
  * 统一界面管理：应用隔离，降低成本，高度复用
  * 集成开源技术：融合了MPP & Hadoop，既兼容了传统的SQL，又支持更多场景以及技术组件
  * 支持多模式云部署：私有云、公有云以及企业成员个性化需求
  * 横向扩展能力，支持自动化一键安装
  * 支持弹性部署，支持多平台：兼容不同平台技术路线

* **流数据实时处理（S.M.A.C.K）**

  DataCanvas支持数据的实时入库、实时计算、实时查询，让实时数据处理变得更加现实与可靠。

* **分析知识库**

  DataCanvas APS 提供机器学习模块和可视化的数据分析工作流，用户通过拖拽算法模块自助式组建分析流程，最终可视化呈现分析结果。通过机器学习算法那和数据建模等高级分析实现预测性分析，使数据分析周期缩短80%。

  * 更易开发：提供专门的模块编程工具，支持用户自定义创建和编辑模块与分析流，通过建模挖掘数据潜在相似性和关联性。
  * 更易管理：任务监控和管理可视化，自动报警并采集日至，自动化分析流程，实现流程的增量运行从而减少无效晕眩。
  * 更易整合新服务：提供完整的RESTful API接口，便于二次开发和应用。
  * 更易部署：基于Docker技术封装数据分析流，业界首创实现分析流的跨环境、跨云处理，达到“云中立”。

* **超快的执行速度**

  DataCanvas采用专有的高效列式内存存储格式和为内存优化的Apache Spark计算引擎，相比广泛使用的Map/Reduce框架消除了频繁的I/O磁盘访问。此外，Spark引擎还采用了轻量级的调度框架和多线程计算模型，相比Map/Reduce中的进程模型具有极低的调度和启动开销，除带来更快的执行速度以外，更使得系统的平均修复时间（MTTR）极大的缩短。在实时在线应用方面，DataCanvas构建了全局索引、辅助索引和全文索引，扩展了SQL语法，满足在线存储和在线业务分析系统（OLAP）的低延时需求。综合在执行引擎及数据存储层上的优化，使得DataCanvas性能全面领先开源Apache Hadoop 2.0,SQL支持完整程度和性能大幅领先Cloudera Impala,比主流MPP数据库快1.5到10倍。

* **完整的SQL支持**

  DataCanvas目前支持SQL 2003，可以运行TPC-DS标准测试集中的全部99个测试项；正在实现更复杂的PL/SQL语法，包括存储过程、函数、游标等功能。DataCanvas同时支持完整的HiveQL语法，并对这些SQL语法进行了扩展，对执行计划进行了大量优化。SQL支持的完整程度比性能更加重要，大量的数据仓库/数据集市应用中都采用了较为复杂的SQL 2003语法，没有这些语法支持，要想把现有应用迁移到Hadoop上来是不可行的。DataCanvas更完整的SQL支持使得用户能将原有数据分析应用方便地迁移到DataCanvas EDS大数据平台，同时又能处理更大的数据量。

* **超强的数据分析功能**

  将大数据放在数据分析师的手中，交互式地探索数据，获得洞察并发现模式和趋势，以便展开进一步的分析及决策已经变得越来越重要。DataCanvas通过分布式的内存列式存储和优化的高速执行引擎来支持交互式SQL查询，使得实时和交互式分析变成可能。DataCanvas同时支持R统计引擎，最新版本的DataCanvas除了支持通过R访问HDFS或者Hyperbase中的数据外，还支持访问存储在Inceptor分布式内存中的数据。Inceptor同时内置了常用机器学习算法的并行化实现，可以与R语言中的数千个算法混合使用。新版本同时支持R命令行以及图形化的R Studio执行R语言程序来访问DataCanvas EDS中的数据，从而使得DataCanvas成为在大数据的数据挖掘和可视化应用领域的利器。DataCanvas包含了经过高度优化的专有图算法，可高速分析关联关系网络等图数据。此外，DataCanvas还集成了机器学习算法库Mahout，包含了聚类分析、分类算法、频度关联分析和推荐系统在内的常用机器学习算法。

* **与数据分析生态系统的强力整合**

  DataCanvas非常重视与数据分析生态系统的整合以提高系统的易用性。DataCanvas与现有成熟系统的无缝整合涉及了数据获取，数据分析以及数据可视化端。传统的关系型数据库的数据可以直接作为数据源接入到集群中参与计算分析，目前已经支持Oracle，DB2及MySQL数据库。数据分析层与R语言的整合带来了R的数千种统计算法的同时，可以充分利用R语言中的绘图工具绘制专业的统计报表；数据可视化不仅可将最终分析结果展示给用户，还可以帮助数据分析师来进行数据探索来发现和解决新问题。DataCanvas支持多种可视化及报表生成工具，包括Tableau、SAP Business Objects、Oracle OBIEE等，使得基于大数据分析的商业决策更易被理解和接受，从而将大数据的潜在价值最大化。虽然有些工具也支持Apache Hadoop，但只有高性能的DataCanvas才真正使得交互式地探索大数据成为现实。

* **完备的企业级解决方案**

  DataCanvas通过提供数据存储、分布式计算、数据分析挖掘以及数据可视化的整套支持，解决了企业在GB到PB级数据分析领域碰到的各种问题。作为企业级解决方案，可管理性是DataCanvas的显著优势，用户友好的管理界面提供了系统安装、系统及集群配置、监控及预警等多方面的一站式支持。DataCanvas架构具有高可用性和快速故障恢复能力，HDFS2.5作为其底层存储系统的支撑技术（Hyperbase亦基于此）保证了数据的持久化和冗余复制，并具有数据自动检测和修复的容错功能；基于HDFS的所有服务为HDFS 2.5的HA功能而优化，确保整个大数据处理系统的高可用性。在安全领域，与Kerberos/LDAP进行整合，支持细粒度访问控制、应用程序安全及数据加密及解密等等。

### 产品服务

DataCanvas提供完整的数据服务，包括：

* DataCanvas EDS（Elastic Database Stack）
* DataCanvas APS（Analytics Platform Service）
* 开发定制服务

DataCanvas支持最广泛的云计算平台，支持公有云和私有云的一键式自动部署，已成功对接Amazon AWS、Microsoft Azure、UCloud、青云、阿里云等国内外主流公有云平台，也支持OpenStack等私有云环境；并且支持公有云和私有云的无缝迁移，为企业用户提供灵活易用的部署方案。

![](/DataCanvas baipishu/产品服务.png)

#### DataCanvas EDS

DataCanvas EDS 为企业提供超融合、可扩展性的分布式数据库服务。

DataCanvas EDS 自主研发的超融合架构，融合Hadoop与MPP数据库各自的数据处理优势，支持OLAP和OLTP场景，提供了业内性能领先的SQL-on-Hadoop查询功能，让传统数据库用户在不改变操作习惯的前提下无缝迁移到大数据架构之上进行数据分析。

**优势如下：**

* 自动部署能力：

  DataCanvas EDS 支持用户在公有云或私有云服务器上的一键部署，全自动安装，随时启停，优化资源利用率，降低费用；支持集群动态扩展，并实现了多种命令（Hive、Pig、Impala、Shell 等）的远程执行和实时交互的功能。

![](/DataCanvas baipishu/弹性部署.png)

* 数据接入能力

  支持多种形式数据接入，包括：
  * Flume 直接写入；
  * Sqoop批量导入多种数据库类型（RDBMS数据，Oracle，MySql，SQL Server等）；
  * CSV，excel文件导入。

* 存储查询能力

  DataCanvas EDS 拥有强大的存储计算能力，可存储P字节级数据，计算集群可以超过上千节点，允许并发1000个处理任务（受限于实际计算资源），同时容器计算环境（Docker）可容纳管理超过10K容器。对于大容量数据（100G）采用列存储，SSD提速以及预处理计算等措施，对于重复查询可以达到秒级响应。对于开放式查询，也可以达到分钟级别的返回。

* 流数据实时处理能力  

  DataCanvas采用极快实时处理架构（S.M.A.C.K），涵盖实时收集、存储、分析、模型和应用接口端到端的解决方案，帮助企业降低各类数据应用的成本和提高效率。

  DataCanvas的流数据实时处理时延低，1PB级数据达到毫秒级以下的延时，事件数据触发分析，分析过程始终在线。

#### DataCanvas APS 

DataCanvas APS 是分析平台工具，用户可通过该平台快速进行数据清洗、数据挖掘和数据建模分析等，从而生成行业大数据解决方案。

DataCanvas APS 可快速对接用户数据源（包括各类型的海量数据和实时数据），将数据分析进行模块化封装。用户可使用平台自带的模块库组件工作流；如果用户有特定需求可在平台界面自行编写程序，DataCanvas平台会自动封装成模块存入数据源或模块库中，用户拖拽模块组建新的工作流，运行后即可得到可视化的分析结果，用户可自定义结果的展现方式。同时开发团队可共享流程和模块，让数据分析变得高效透明。

同时，DataCanvas作为国际上第一个把容器（Docker）技术植入大数据分析的产品，极大的抽象了数据分析的流程，使得数据分析流可以跨环境、跨云处理，达到“云中立”。在混合IT形势下，封装各种环境差异，同一数据流可以运行于各类公有云和私有云环境，屏蔽了多种技术组合（包括业界流行的Hadoop, Map-Reduce, Hive，Impala，Sqoop等）、环境和云提供商的依赖性，解决了数据分析流程的依赖性实现类似虚拟机机制的“数据流程虚拟化”。

![](/DataCanvas baipishu/APS数据流.png)

**优势如下：**
* **数据分析能力**
  * DataCanvas APS支持所有大数据主流分析语言（MR, HIVE, Pig, Spark, R, Java, Python, C++），内建丰富模块库，降低数据人才门槛，提高开发效率。
  * 拥有业界首创的利用Docker容器封装分析模块，采用标准接口，拥有安全、隔离、高效的运行环境；
  * 用户可共享模块和工作流，方便协同及他人复用；通过可视化的界面拖拽模块，自助式组件数据分析流，结合机器学习，建模挖掘数据潜在的相似性和关联性，实现数据趋势的预测分析。
  * Playbook提供简单强大的行业解决方案模板，用户可直接使用，快速获得高级分析功能。
  
* **开发定制能力**

  * DataCanvas分析数据时支持在不同云平台上（包括私有环境）的无缝迁移，满足用户数据私密性要求；
  * 提供开源工具ScrewJack及网页工具，可极大简化了企业用户模块开发的流程；同时可提供覆盖各类场景和语言的运行环境。
  * 对于所有功能均提供完整REST API接口，用户可根据自身业务需求进行二次开发和利用。
  * 根据用户需求，提供数据分析模块和业务模板的定制开发，为用户提供一站式大数据解决方案。
  
* **协作运维能力**
  * 可视化管理：统计显示一目了然、可视化监控运行流程、支持详细日志的查看。
  * 自动化分析：分析流自动化，不再头疼分散的脚本、增量运行减少无效计算。
  * 个性化设置：支持自动报警和定时管理。
  * 全员化共享：团队成员之间共享模块和工程，且模块和工程拥有完善的版本，同时可对团队成员进行权限控制。

####开发定制服务 
DataCanvas可以根据行业需求，提供数据分析模块的定制开发；针对行业的通用性分析需求，定制分析业务模板。











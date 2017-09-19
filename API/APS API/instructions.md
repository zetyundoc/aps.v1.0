# 使用说明

DataCanvas平台的API接口包括以下几类：

1、**DATACONNECTOR** — 数据连接器接口

<li>用户数据平台数据导入时使用。

2、**EHC** — EDS接口 

<li>EDS即集群资源。
<li>EDS接口主要用于数据平台的集群管理、数据查询等。


3、**JOB** — 即任务接口

<li>任务即大数据分析工作流即项目运行后的状态，项目运行即生成一个任务。
<li>任务接口主要用于创建任务、查看任务的运行状态和结果等信息。

4、**MODULE** — 模块接口

<li>模块即数据分析工作流即项目的最小单元，模块内部统一封装了机器学习等常用算法。
<li>模块接口主要用于创建、删除模块，查看和设置模块信息等。

5、**PRIVILEGE** — 权限接口

<li>权限即DataCanvas平台设置的安全策略，保证用户资源的分配。
<li>权限接口主要用于二级用户的权限管理，包括用户分组、用户角色的创建以及EDS和APS的使用权限设置。

6、**PROJECT** — 项目接口

<li>项目即数据分析工作流，由多个算法模块组合而成。
<li>项目接口主要用于项目的创建、删除和运行设置等。


7、**USER** — 用户接口

<li>用户即使用DataCanvas平台的人，包括admin用户和二级用户。
<li>用户接口主要用于用户中心的设置，包括用户注册和登录，二级用户的角色创建和分组等。<br/>
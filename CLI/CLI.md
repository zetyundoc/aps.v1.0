# CLI文档

datacanvas-cli（*Command-Line Interface*）命令行界面工具旨在帮助用户通过接口命令行快速对DataCanvas进行查看、创建和编辑等操作。支持Linux及Windows。

###安装

前提：系统已经安装 [python](https://www.python.org/downloads/) 和 [pip](https://pip.pypa.io/en/latest/installing/)。


1、 通过pip安装cli。
```
$ pip install datacanvas-cli
```
2、如果不是在 virtualenv 上安装，则需要 sudo。
```
$ sudo pip install datacanvas-cli
```
3、如果需要更新到最新版本：
```
$ pip install --upgrade datacanvas-cli
```

###初次使用

如果为第一次使用datacanvas-cli，需要首先设置接口IP并登陆。

1、设置接口IP

datacanvas-cli 安装完成后，进入cli环境，通过config命令设置IP地址。

```
$ datacanvas-cli config
ip:http://
whether to change?<y/n>y
Input your eds address[]:***.***.*.***
```
<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >说明：请联系我们获取调用接口的IP地址和端口。</p>

2、登录用户

部分命令需要登录验证才能进行操作。

```
& datacanvas-cli login
Your username:******
Your password:
login success!
```
<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >说明：用户注册的用户名、密码即可登录。</p>

###输入参数

<p style="color: #C09853;background: #FCF8E3 none repeat scroll 0% 0%; font-size:13px;" >说明：可根据“-h”或者“--help”命令获取帮助。</p>

datacanvas-cli 的参数包含 int 和 string 类型。如:
```
$ datacanvas-cli eds updateeds -i e-32aea511e5956f -n DemoCluster -d “DataCanvas Aliyun demo cluster”
```

###命令列表

**user命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|deleteuser|删除user |gentoken |生成token |
|gettoken|获取token |userlogin|用户登陆 |
|userlogout|注销用户|setdafaultuserkey|设置默认userkey|
|userkeycreat|创建 userkey |userkeydefault|获取 userkey |
|userkeydetail|查看userkey详细信息|userkeylist|查看userkey列表 |
|userkeyupdate|更新userkey |userlist|查看user列表|
|userregister|注册user |userupdate|更新user|
|sendphoneverifysms|发送验证信息 |uploadavatar|上传头像 |

**eds命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|edslist| 查看目前所有EDS集群列表 |edsstatus | 查看当前EDS集群状态 |
|namebyip|通过IP地址查看EDS集群名称|createeds|创建一个EDS集群|
|deleteeds|删除一个EDS集群|listtable|查看集群列表|
|rebooteds |重启EDS集群|sqlquery|数据库查询操作|
|sqlstatus|数据库查询状态|starteds|启动一个EDS集群|
|stopeds|停止一个EDS集群|terminateeds|终止一个EDS集群|
|updateeds|更新EDS集群信息||||

**job命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|createjob| 创建job|jobstatus|查看工作状态||

**module命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|createmodule|创建一个模块|moduledelete|删除一个模块|
|modulecreateparams|创建模块参数|modulelist|查看模块列表|
|moduletags|查看模块标签|moduleversionlist|查看模块版本列表|
|updatemodule|更新模块||||

**privilege命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|checkrule| 查看权限规则|getinstanceoperates| 获取示例操作|
|gettimestamp|获取时间标识|groupdetail|查看组群详细信息|
|listgroups|查看组群列表|listresourcetypes|查看resource类型列表|
|listrole|查看角色列表|roledetail|查看角色详细信息||

**project**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|createproject|创建一个项目|deleteproject|删除一个项目|
|projectlist|查看项目列表|setupworkspace|设置项目工作流|
|updateproject|更新一个项目||||

**resource命令**

|命令|描述|命令|描述|
| -- | -- | -- | -- |
|hiveresourcelist| 查看当前hive类型的Resouce列表|resourcecopy|复制并创建Resource|
|resourcecreate|创建Resource|lifecycletypelist|查看Resource 生命周期类型列表|
|resourcelist|查看Resource列表|privacylevellist|查看Resource 隐私级别列表|
|specifytypeparams|获取某类资源创建参数|resourcetypelist|查看Resource 类型列表|
|resourceupdate|更新Resource |deleteoneresource|删除Resource|
|startoneresource|启动Resource|stoponeresource|停止Resource|
|terminateoneresource|终止Resource ||||














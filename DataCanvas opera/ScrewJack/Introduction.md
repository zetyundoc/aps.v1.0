#简介

###<a name="one">基本概念</a>

Screwjack能够帮助模块设计者快速建立模块。模块是由一个 spec.json 的文件来定义的， 下面是一个 spec.json 的例子：

```pythen
{
    "Name": "SVM",
    "Description": "A simple SVM",
    "Version": "0.1",
    "Cmd": "/usr/bin/python main.py",
    "Param": {
        "C": {
            "Default": "",
            "Type": "string"
        }
    },
    "Input": {
        "X": ["csv"],
        "Y": ["csv"]
    },
    "Output": {
        "MODEL": ["model.svm"]
    }
}
```
建立模块需要以下5步，本文会在后面的章节详细介绍。

<ol>
<li>初始化模块</li>
<li>添加输入/输出/参数</li>
<li>添加代码实现</li>
<li>测试模块<br/>
　　local 测试<br/>
　　docker 测试<br/></li>
<li>提交模块</li>
</ol>

###<a name="two">安装说明</a>

Screwjack的使用需要先安装* docker <http://www.docker.com/> *。

####安装 docker

模块的开发环境需要预装docker，请按照链接自行安装在Linux 系统中 http://docs.docker.io/installation/. 安装完毕后，请将当前用户加入到 `docker` 组中。例如在Ubuntu Linux系统中，命令行如下：
```
sudo usermod -aG docker your_linux_username
```
####安装ScrewJack

你可以直接通过PyPI直接获取screwjack :
```
pip install -U screwjack
```
####设置screwjack

在使用screwjack前，先建立用户名：
```
export DATACANVAS_USERNAME=your_username
```
也可以将用户名设置在`$HOME/.screwjack.cfg `文件中:
```
[user]
username = your_username
```
也可以添加``–username`` 的选项在screwjack 中：
```
screwjack --username=your_username init
screwjack --username=your_username param_add
screwjack --username=your_username input_add
screwjack --username=your_username output_add
```

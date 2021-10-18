## 前言
初学 Python 时，总是被 Python 的两个不太兼容的版本搞得头昏脑胀。按目前的发展趋势，Python 未来的主流版本为 python3。但是我们经常会遇到一些很有意思的代码使用的是 python2 版本，于是我们需要同时拥有 python2 和 python3 的运行环境。这里介绍一个强大的软件 Anaconda，它实现 python2 和 python3 两个版本的共存，并且可以相互切换。Anaconda 是一个用于科学计算的 Python 发行版，支持 Linux、Mac、Windows，包含了众多流行的科学计算、数据分析的 Python 包。

## 为什么选择 Anaconda
Anaconda 实际上是一个软件发行版，它附带了 conda、python 和多个科学包及其依赖项。应用程序 conda 是包和环境管理器。Anaconda 的下载文件比较大（约500MB），因为它附带了 Python 中最常用的数据科学包。如果只需要某些包，或者需要节省带宽或存储空间，也可以使用 Miniconda 这个较小的发行版（仅包含 conda 和 python）。

### 什么是 Anaconda
Anaconda 是专注于数据分析的 Python 发行版本，包含了 conda、Python 等多个科学包及其依赖项。作为好奇宝宝的你是不是发现了一个新名词 conda，那么你一定会问 conda 又是什么呢？

### 什么是 conda
conda 是开源包（packages）和虚拟环境（environment）的管理系统。

- **开源包管理**：可以使用 conda 来安装、更新、卸载工具包，并且它更关注于数据科学相关的工具包。在安装 Anaconda 时就预先集成了像 numpy、scipy、pandas、scikit-learn 这些在数据分析中常用的包。另外值得一提的是，conda 并不仅仅管理 Python 的工具包，它也能安装非 Python 的包。比如在新版的 Anaconda 中就可以安装R语言的集成开发环境 Rstudio。
- **虚拟环境管理**：在 conda 中可以建立多个虚拟环境，用于隔离不同项目所需的不同版本的工具包，以防止版本上的冲突。对纠结于 Python 版本的同学们，我们也可以建立 python2 和 python3 两个环境，来分别运行不同版本的 Python 代码。  

知道 **是什么（what）** 的同时，我们也需要问一问 **为什么（why）**。那么，为什么要选择用 Anaconda 呢？

### Anaconda 的优点
Anaconda的优点总结起来就八个字：省时省心、分析利器。

- **省时省心**： Anaconda 通过管理工具包、开发环境、Python 版本，大大简化了你的工作流程。不仅可以方便地安装、更新、卸载工具包，而且安装时能自动安装相应的依赖包，同时还能使用不同的虚拟环境隔离不同要求的项目。  
- **分析利器**： 在 Anaconda 官网中是这么宣传自己的：适用于企业级大数据分析的 Python 工具。其包含了720多个数据科学相关的开源包，在数据可视化、机器学习、深度学习等多方面都有涉及。不仅可以做数据分析，甚至可以用在大数据和人工智能领域。  

解决了 **是什么（what）** 以及 **为什么（why）** 的问题后，下面让我们看一下 **怎么做（how）**。  

说明一下 conda 的设计理念—— **conda 将几乎所有的工具、第三方包都当做 package 对待，甚至包括 python 和 conda 自身！**因此，conda 打破了包管理与环境管理的约束，能非常方便地安装各种版本 python、各种 package 并方便地切换。

### Anaconda 中的重要组件
- **Anaconda Navigator**：用于管理工具包和环境的图形用户界面，后续涉及的众多管理命令也可以在 Navigator 中手工实现。
- **Jupyter notebook**：基于 web 的交互式计算环境，可以编辑易于人们阅读的文档，用于展示数据分析的过程。
- **qtconsole**：一个可执行 IPython 的仿终端图形界面程序，相比 Python Shell 界面，qtconsole 可以直接显示代码生成的图形，实现多行代码输入执行，以及内置许多有用的功能和函数。
- **spyder**：一个使用 Python 语言、跨平台的、科学运算集成开发环境。Spyder 的最大优点就是模仿 MATLAB 的“工作空间”。
- **Conda**: Conda 的包管理就比较好理解了，这部分功能与 pip 类似。

***

## 安装 Anaconda
Anaconda 可用于 Windows、Mac OS X 和 Linux。可以在[官网](https://www.anaconda.com/download/)上找到安装程序和安装说明。

如果计算机上已经安装了 Python，这不会有任何影响。实际上，脚本和程序使用的默认 Python 是 Anaconda 附带的 Python。这里需要注意的是选对自己操作系统对应的版本。安装完后打开cmd，输入conda list可以查看当前安装的内容。输入conda upgrade --all，可以更新默认环境下的所有包。并在提示是否更新的时候输入y（Yes）以便让更新继续。初次安装下的软件包版本一般都比较老旧，因此提前更新可以避免未来不必要的问题。

### 设置 Path 变量
默认安装不会自动添加全局变量，如果有需求可以手动配置 Path，以 Anaconda2 为例：

``` bash
# 将加入到PATH路径
D:\Programs\Anaconda2D:\Programs\Anaconda2\Scripts
# 在cmd输入python
-VPython 2.7.14 :: Anaconda, Inc.
```

### Anaconda 镜像
因为<http://Anaconda.org>的服务器在国外，conda 下载的速度经常很慢。可以设置国内的镜像源来加速：

这里使用了清华大学开源软件镜像站tuna提供的资源，在此表示感谢  
<https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/Anaconda>  
安装包可以到以下地址分流下载  
<https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/>

``` bash
# TUNA 还提供了 Anaconda 仓库的镜像，运行以下命令即可添加 Anaconda Python 免费仓库
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
# 设置搜索时显示通道地址
conda config --set show_channel_urls yes
# 执行完上述命令后，会生成配置文件记录着我们对conda的配置，直接手动创建、编辑该文件是相同的效果
# Linux/Mac
~/.condarc
# Windows
C:\Users\USER_NAME\.condarc
# 运行测试一下吧
conda install numpy
```

## conda 安装 Django 实践
以下操作为 anaconda python3.6 环境下进入Anaconda Prompt安装django1.11的实践过程：

``` bash
# 在python3.6环境下进入Anaconda Prompt创建django1.x专用虚拟环境
conda create -n django1.x
# 激活专用虚拟环境
activate django1.x
# 查看conda当前django可用版本
conda search django
conda install django==1.11.10
# 切换到虚拟环境家目录
cd C:\Users\wsgzao\AppData\Local\conda\conda\envs\django1.x
# 创建项目
django-admin.py startproject myweb
# 创建app
python manage.py startapp myapp
# 启动Django中的开发服务器
python manage.py runserver
# 帮助文档
python manage.py -h
# Django命令
python manage.py <command> [options]
```

## conda 常用命令
最新版的conda是从site-packages文件夹中搜索已经安装的包，不依赖于pip，因此可以显示出通过各种方式安装的包。conda将conda、python等都视为package，因此完全可以使用conda来管理conda和python的版本

``` bash
# 列出所有已安装的包
conda list
# 安装软件包，同时它会自动安装此软件包的依赖项 
conda install package_name
# 同时安装多个包
conda install numpy pandas
# 安装指定版本的包
conda install python=2.7
# 安装离线包
conda install /package-path/package-filename.tar.bz2
# 卸载包
conda remove package_name
# 更新（小）/升级（大）环境中的所有已安装的包
conda update/upgrade --all
# 更新conda，保持conda最新
conda update conda
# 更新anaconda
conda update anaconda
# 更新python
conda update python
# 查看conda安装信息
conda info
# 查看conda帮助
conda help
# 搜索可以安装的包
conda search package_name
# 创建conda虚拟环境
conda create -n env_name
# 在这里，-n env_name 设置环境的名称（-n 是指名称），而 list of packages 是要安装在环境中的包的列表
conda create -n env_name list of packages
# 可以创建具有特定 Python 版本的环境
conda create -n py2.7.14 python=2.7.14
# 查看conda版本
conda -V
# 进入环境
# linux 下用 
conda activate env_name
# windows 下用
activate env_name
# 离开环境
# linux 下用 
conda deactivate
# windows 下用
deactivate
# 列出环境
conda env list
# 删除环境
conda env remove -n env_name
# 导出环境将包保存为 YAML，输出环境中的所有包的名称（包括 Python 版本）
conda env export > environment.yaml
# 加载环境
conda env create -f environment.yaml
```

## Troubleshooting
1. CLI报错 `zsh : command not found: conda`  
``` bash
vim ~/.zshrc  
export PATH=<anaconda_bin>:$PATH
# e.g. export PATH=/Users/friedrich/opt/anaconda3/bin:$PATH  
source ~/.zshrc
```
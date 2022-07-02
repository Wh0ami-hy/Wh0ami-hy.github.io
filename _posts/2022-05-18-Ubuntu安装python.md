---
layout: post   	
catalog: true 	
tags:
    - Linux
---



## 安装python

* 运行以下指令安装python3.8

```
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8
```

* 修改python3默认指向

```
sudo rm python3
sudo ln -s python3.8 python3
```

* 查看python版本验证修改成功：

```
$ python3 --version
```



## 安装pip

默认情况下，`pip` 未安装在 Ubuntu 上

Ubuntu 18.04 默认安装了 Python 2 和 Python 3

`pip`，默认情况下是指 Python 2。`pip3` 代表 Python 3 中的 pip。

```
sudo apt update
sudo apt install python3-pip
```



## 切换python版本

查看Ubuntu 所有 python 版本

```shell
ls /usr/bin/python*
```

切换默认版本

```
echo alias python=python3.8 >> ~/.bashrc
```

更新配置文件

```
source ~/.bashrc
```

## 卸载python

Ubuntu原装python版本切不可卸载，因为系统中有依赖于原装python的地方

## 其他报错

**1**

```
python setup.py egg_info" failed with error code 1 in /tmp/pip-build-038_2sp8/cryptography/
```

解决：升级pip版本

```
	pip3 install --upgrade pip
```

**2**

```
ubuntu升级python3.8后报错ModuleNotFoundError: No module named 
'apt_pkg'
```

解决：

第一步：

```
sudo apt-get remove --purge python-apt
```

第二步：

```
sudo apt-get install python-apt -f
```

第三步：

```
cd /usr/lib/python3/dist-packages/
```

第四步：复制为38m，若升级为了3.7则为37m

```
sudo cp apt_pkg.cpython-36m-x86_64-linux-gnu.so  apt_pkg.cpython-38m-x86_64-linux-gnu.so
```

第五步：软链接

```
sudo ln -fs apt_pkg.cpython-36m-x86_64-linux-gnu.so apt_pkg.so
```

**3**

命令行打开语言支持`gnome-language-selector`报错






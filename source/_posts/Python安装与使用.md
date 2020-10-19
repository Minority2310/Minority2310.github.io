---
title: Python安装与使用
date: 2020-02-04 15:42:33
tags:
- Python
categories:
- 学习
---

# 安装

进入[官网](https://www.python.org/)下载Python3和Python2的安装包

**安装Python3**，勾选添加环境变量和启动器的选项，点击默认安装

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2020-02-04_15-58-03.png" alt="python3安装" style="zoom: 67%;" />

**安装Python2**，一路默认安装

> **Python3**：
>
> 路径：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37`
>
> 环境变量：自动添加在用户环境变量Path中
>
> **Python2**：
>
> 路径：`C:\Python27`

# 版本切换与使用

**使用Python**

```cmd
# 使用默认版本的Python
py
# 使用Python 27
py -2
# 使用Python 35
py -3
# 查看当前默认的Python版本
py -0
```

**文件头部指定Python版本，指定后可以直接运行**

```python
#! python3
```

**pip及命令行第三方包的使用**

```cmd
py -m pip install <包名>
# 指定特定版本的pip
py -3 -m pip install <包名>
```




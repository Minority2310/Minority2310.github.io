---
title: win10系统重装
date: 2020-01-19 21:43:08
tags:
- win10
- 系统重装
categories:
- 学习
---

# 一.准备阶段

## 硬件

1. 大于等于8G的空白U盘
2. 需要重装的电脑(MECHREV)

## 软件

1. [Rufus系统盘制作工具](https://rufus.ie/)
2.  [win10镜像(MSDN)](https://msdn.itellyou.cn/)



# 二.进行阶段

## 系统盘制作

1. 插入U盘

2. 启动Rufus

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/hrwi76rg32.png" alt="Rufus界面" style="zoom: 80%;" />

>1. 从磁盘中导入win10镜像文件，确保分区方案和目标系统类型选择为**用于UEFI计算机的GPT分区方案**，文件系统为**NTFS**格式
>2. 点击开始等待完成

## 关闭secure boot

进入`Windows设置>更新和安全>恢复`点击立即重新启动

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/Snipaste_2020-01-19_22-06-27.png" style="zoom:67%;" />

在选项中进入UEFI，在`Security>Secure boot`中将`Enabled`改为`Disabled`然后保存重启，系统进入U盘系统安装引导界面

## 安装win10

1. 一路默认直到“你想执行哪种类型的安装？”界面，选择**自定义**
2. 下一步到达“你想将Windows安装在哪里？”界面，**删除**全部分区直到所有分区下**删除**变成不可点击状态，选择固态硬盘所在的分区点击**下一步**

3. 等待安装完毕快速拔出U盘，不然会再次进入系统安装界面。如果没有及时拔出，从再次出现的系统安装界面退出重启前拔掉U盘就能正常启动了



# 后续阶段

## 驱动安装

联网状态下系统会自动寻找可用的驱动下载并安装，等待即可

## 磁盘分区

进入**磁盘管理**界面，找到未分配的分区右键**新建卷**，按照需要自定义空间和盘符


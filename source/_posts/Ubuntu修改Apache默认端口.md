---
title: Ubuntu修改Apache默认端口
date: 2020-01-19 17:37:00
categories: 
- 学习
tags: 
- Ubuntu
- Apache
- 阿里云
---

> 阿里云服务器版本：Ubuntu 16.04
>
> Apache版本：Apache2

# 问题

Apache2默认端口为**80/443**，在我们引进**Nginx**的时候，会与Nginx的端口冲突



# 解决步骤

1. 修改`/etc/apache2/ports.conf`文件，将端口改为**8091/444**

![](https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/apache2修改端口_01_20200823.png)

2. 修改`/etc/apache2/sites-enabled/000-default.conf`文件，将端口也改为**8091**

<img src="https://wj-pictures.oss-cn-beijing.aliyuncs.com/img/apache2修改端口_02_20200823.png" style="zoom: 80%;" />

3. 重启并查看Apache状态：
```bash
service apache2 restart
service apache2 status
```
4. 将阿里云安全组入方向**8091/444**端口放开


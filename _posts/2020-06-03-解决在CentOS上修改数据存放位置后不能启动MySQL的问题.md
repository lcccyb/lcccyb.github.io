---
layout: post
title: 解决在CentOS上修改数据存放位置后不能启动MySQL的问题
date: 2020-6-3
categories: work
tags: 
  - Linux
  - 运维
---

配置mysql时，由于工作的项目需要修改datadir的值，修改etc/my.cnf文件：

```
datadir=/data/mysqldata
```

在CentOS中启动MySQL：

```
[root@localhost ~]# service mysqld start
```

提示如下错误：

```
Job for mysqld.service failed because the control process exited with error code. See "systemctl status mysqld.service" and "journalctl -xe" for details.
```

查看错误日志/var/log/mysqld.log：

```
mysqld started
InnoDB: Operating system error number 13 in a file operation.
InnoDB: The error means mysqld does not have the access rights to
InnoDB: the directory.
InnoDB: File name /usr/local/project/etc/data/ibdata1
InnoDB: File operation call: 'create'.
InnoDB: Cannot continue operation.
mysqld ended
```

将文件复制到新目录下并修改权限：

```
cp -r /var/lib/mysql/*  /data/mysqldata
chown  -R mysql:mysql  /data/mysqldata
chown  -R mysql:mysql  ./*
```

重启MySQL还是失败，日志信息还是一样。



解决方法：修改配置文件/etc/selinux/config：

```shell
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=enforceing
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected. 
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

将**SELINUX=enforceing**修改为**SELINUX=permissive**，然后reboot，即可解决问题。
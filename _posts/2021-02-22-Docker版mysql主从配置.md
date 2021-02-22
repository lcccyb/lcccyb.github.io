---
layout: post
title: Docker版mysql主从配置
date: 2021-2-22
categories: work
tags: 
  - Docker
---

### 准备工作

数据库的配置文件在mysqld下需要修改或添加以下的记录：

```
[mysqld]
server-id = 1  #服务器ID号，整数值，保证唯一标识一台服务器就可以,主从之间不要重复
log_slave_updates 
log-bin=mysql-bin #打开二进制日志
```

其中一台设置为主库。

### 主库服务器操作

```sh
docker exec -it $mysql_contains /bin/sh
mysql -uroot -p$password
```

登录进去之后先创建一个用户用来做同步使用：

```mysql
GRANT REPLICATION SLAVE ON *.* TO copy@'%' IDENTIFIED BY '10jqkacopy';
flush privileges;
exit;
```

备份数据：

```mysql
mysqldump -uroot -p10jqka@123 --master-data=2 --single-transaction --all-databases > /var/lib/mysql/mysql_master_data.sql
```

执行完之后数据已经保存在容器内的 /var/lib/mysql/mysql_master_data.sql 查看一下这个文件是否正常，正常的里面应该是很多sql语句。

其中有这样的语句：

```mysql
CHANGE MASTER TO MASTER_LOG_FILE='××××',  MASTER_LOG_POS=××××;
```

退出

```
exit
```

### 从库服务器上操作

将文件 mysql_master_data.sql 复制到宿主机的源目录下（如/data/mysql/3306，安装上面的操作去查找）

```shell
docker exec -it $mysql_contains /bin/sh
mysql -uroot -p$password 
```

执行命令（注意：该步骤将全部清空数据，建议先做备份，备份方式如上面主库mysqldump操作）

```mysql
source /var/lib/mysql/mysql_master_data.sql
```

执行完成后，建议检查一下，看看数据库是否都在，也可以找一些表的数据来确认

```mysql
show databases;
```

刷新一下数据库的权限

```mysql
flush privileges;
```

配置主从：

```mysql
change master to  master_host='10.1.31.184', master_port=3306, master_user='copy', master_password='10jqkacopy', MASTER_LOG_FILE='mysql-bin.000006', MASTER_LOG_POS=108782769;
```

启动slave：

```mysql
start slave;
```

查看一下状态

```mysql
show slave status \G 
```

其中必须要看到2个thread都是yes：

![](http://m.qpic.cn/psc?/V148DVWB3fRuPX/Sc7wZG8Q0BUeMz.O80ebfIGNQcJ4OigGgE7imv5bq2xnS17cC9VJWrpSYkwI*XozrURCicdYrUKubPldsS7Wj.t0IjndNg3q.I2oaj.WcLQ!/b&bo=zgJ*AQAAAAADF4A!&rf=viewer_4)

退出

```
exit
```

这样主从同步配置完成，切记之后不要再手动修改从库，否则会破坏主从关系。
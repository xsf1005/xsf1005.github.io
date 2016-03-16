---
layout: post
title: mysql主从配置
date: 2016-03-12
categories: mysql
description: mysql主从配置说明

---

### 主备服务器

   主服务器 IP:192.168.0.1

   从服务器IP:192.168.0.2

### MySQL主服务器配置方法及注意事项

   1、打开主服务器的mysql 配置文件 (默认linux下为：my.cnf，Windows下为：my.ini)
   
   2、找到[mysqld]节点，添加或修改成以下内容。

	server-id=1 #服务器ID
	log-bin=mysql-bin01
	binlog-do-db=test #这里设置需要在主服务器记录日志的数据库，只有在这里设置了的数据库才能被复制到从服务器
	binlog-ignore-db=mysql #这里设置在主服务器上不记度日志的数据库
	expire_logs_days=10

   3、执行 SHOW VARIABLES LIKE "%log_bin%";  查看主服务器的binlog是否开启。 log_bin 这项 为 ON 的话就表示已开启。

   4、在主服务器上创建从服务器使用的帐号并给予相应的权限(主要是replication slave权限)，为避免配置过程中出现问题，可以也给予 
   reload,super权限，配好后再跟据实际情况取消。

   grant replication slave, reload, super on *.* to 'backup'@'192.168.0.1' identified by '123456'; #backup是用户名，123456是密码
或者使用navicate赋予权限。

   5、至此主服务器已设置完成。

### 从服务器的mysql配置文件。

   1、在[mysqld]节点下，添加或修改成。

	server-id=2
	log-bin=mysql-bin02
	replicate-do-db=test
	replicate-ignore-db=mysql
	expire_logs_days=10

    2、命令行配置从服务（5.1.7之后）

	change master to  
	master_host='192.168.0.1',
	master_user='backup'，
	master_password='123456'，
	master_log_file='mysql-bin01.000001',  #此处填写主服务器的日志文件名，文章上方主服务状态信息中的File的值，上面已用红色强调。
	master_log_pos=4887; #此处填写主服务器日志文件记录的位置，文章上方主服务状态信息中的Position的值，上面已用红色强调。

    3、执行上面命令后，再执行start slave，用启动从服务器模式。

    4、可以使用 show processlist 查看进程。

    5、重启一下从服务器的MySQL。

    6、使用show slave status; 命令，查看从服务器的运行状态。显示结果中以下两项都为Yes的话，那说明正常。

	Slave_IO_Running: Yes  
	Slave_SQL_Running: Yes

    7、在主服务器上Test数据库创建表，写入数据，然后到从服务器上查看Test数据库有没有进行同步。

### 注意

   下面的配置只在mysql 5.1.7 之前的版本才有效，或者使用命令即可。

	master-host=192.168.0.1
	master-user=backup
	master-password=123456
	master-port=3306

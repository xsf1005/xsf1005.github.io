---
layout: post
title: kettle修改任务中的数据库的问题
date: 2016-02-29
categories: kettle
tags: [kettle,update]
description: 关于kettle中数据库修改后的问题

---

### 注意事项

	今天一个数据抽取上传任务出现问题，找了半天原因是连接数据库的用户名和密码被改了。
	于是修改了kettle中对应的数据库的用户名和密码，结果悲剧了，执行任务后，死活不成功，
	查看错误日志，搞了半天，以为sql问题，于是单独执行了sql也没有问题，最后，实在没办法，
	重启了kettle,结果惊喜发生了，居然好了，这个问题告诉我们，原来修改后需要重启才生效，应该是
	配置的数据被卸载配置问题，导致后面调用的时候，还是第一次启动时候加载的，重启后，重新加载了最新
	的配置才正常。


### 配一个kettle任务图

	![image](http://xsf1005.github.io/img/kettle/kettle.png)
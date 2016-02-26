---
layout: post
title: github blog 域名绑定
date: 2016-02-20
categoreis: domain
tags: [域名,绑定]
description: 绑定博客域名

---

### 在万网上进行域名购买

	http://wanwang.aliyun.com/ 选择你要购买的域名

### DNSPOD
	
	https://www.dnspod.cn/  注册登录进入管理,进行域名解析绑定到你的github 博客
	域名解析的时候,主机记录 @ ,记录类型 A ,线路类型 默认,记录值 192.30.252.153(或154) TTL 60

### 添加域名到博客

	在博客根目录下建立CNAME 里面填写你购买的域名即可。提交到github上。

   等待域名生效。生效后，访问域名即可跳转到你的博客。
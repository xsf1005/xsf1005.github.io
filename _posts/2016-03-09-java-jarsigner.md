---
layout: post
title: 浏览器应用applet时，jar 数字签名
date: 2016-03-09
categories: java
description: 项目中使用的应用

---


### 说明

	applet应用于浏览器的时候，如果要操作客户端文件读写，就需要授予权限和jar数字签名。主要是因为Java 沙盒模型。
	Java具有很好的安全控制，就是有名的沙盒模型。Applet可以在沙盒里做任何事情，但不能读或修改沙盒外的任何数据。
	沙盒模型的思想是在信任的环境中运行不信任的代码，这样即使用户不小心引入了不安全的applet，applet也不会对系统造成破坏。


### jar 的签名过程

    1.生成证书：

	keytool -genkey -dname "cn=所有者, ou=组织单位, o=组织,l=城市,st=省份, c=国家" -alias rwcard -keypass 123456 -storepass 123456 -validity 365 -keystore .\rwcard
	keytool -list -keystore .\rwcard -storepass 123456
	keytool -export -keystore .\rwcard -storepass 123456 -file rwcard.cer -alias rwcard


	2.证书签名

	jarsigner -verbose -keystore .\rwcard readcardapp.jar rwcard
	jarsigner -verbose -keystore .\rwcard jna-4.1.0.jar rwcard
	jarsigner -verbose -keystore .\rwcard jna-platform-4.1.0.jar rwcard


### 使用

	证书要保证和签名文件在同一目录下，在运行安装jre的程序时，会提示是否信任此证书，信任后，在控制面板的java里可以看到已经信任的用户证书。

### 注意

	如果没有提示信任证书，那么需要清楚java 的缓存，同时检查是否之前有证书存在，如果有删除原来的证书。
	同时把用户目录下的policy文件也一并删除。重新刷新加载。
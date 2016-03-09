---
layout: post
title: js 通过applet调用 java 方法
date: 2016-03-06
categories: java
description: Java applet js 使用

---

### 创建一个applet主类

	public class ReadCardApplet extends JApplet{
	
		private static final long serialVersionUID = 1L;

		public void init(){
			System.out.println("=====初始化app程序========");
		}
		
		public Object getInstance(String name){
			try{
				Class<?> clz = Class.forName(name);
				Object obj = clz.newInstance();
				return obj;
			}catch(Exception e){
				e.printStackTrace();
				return null;
			}
		}
		
		public void showMessage(String msg){
			JOptionPane.showMessageDialog(null, msg);
		}	
		
		public void destroy(){
			System.out.println("=====app已销毁========");
		}
	}


### 创建应用调用业务类

	public class DemoPrint {

		public void say(String msg){
			System.out.println(msg);
		}
		
		public String printinfo(String info){
			return  "Hello:"+info;
		}
		
		public static String sayHello(String info){
			return "I am world-->"+info;
		}
	}


### 创建applet加载js

	<applet id="readcard" name="readcard" code="com.xsf.readcard.app.ReadCardApplet" 
		codebase="./" archive="readcard.jar,demoprint.jar" width="0" height="0"
		scriptable="true" >需要安装JAVA虚拟机(jre)
	</applet>


### 页面js调用

	function test(){
		var card = readcard.getInstance("com.readcard.app.DemoPrint");
		card.say("hello,world");
	}


### jar签名

	keytool -genkey -keystore readcard.store -alias readcard -validity 3650（生成密钥库）
	-validity 3650 表示的是有效期是3650天，默认情况是六个月有效期。

	keytool -export -keystore readcard.store -alias readcard -file readcard.cert （导出证书）

	jarsigner -keystore readcard.store demoprint.jar readcard （jar签名）
	jarsigner -keystore readcard.store readcard.jar readcard
---
layout: post
title: js 调用 applet 一个实践
date: 2016-03-08
categories: java
description: 项目中使用的应用

---

### applet初始化

	1.写入权限策略文件Policy文件，一般是用户目录下，applet的权限是先加载系统policy，然后加载用户policy,叠加使用。
	public static String writeFile()
	  {
	    String userPath = System.getProperty("user.home");
	    String fileName = userPath + "\\.java.policy";

	    StringBuilder sb = new StringBuilder("");
	    try
	    {
	      StringBuilder myPermision = new StringBuilder("");
	      myPermision.append("grant { \n");
	      myPermision.append("permission java.security.AllPermission; \n");
	      myPermision.append("};");
	      String strPermision = myPermision.toString();
	      PrintWriter out = new PrintWriter(new DataOutputStream(new FileOutputStream(new File(fileName), false)), true);
	      out.print(strPermision);
	      out.close();
	    } catch (FileNotFoundException e) {
	      sb.append("文件找不到错误;");
	      e.printStackTrace();
	    } catch (SecurityException se) {
	      sb.append("安全性错误" + se.getMessage());
	    }
	    sb.append("书写安全文件操作完毕,书写文件位置：" + fileName);
	    return sb.toString();
	  }

	2.为了保证初始化后，能正常使用，做一次策略刷新。
		Policy.getPolicy().refresh();

	3.一般来说客户端要调用某个硬件设备，那么需要dll文件，怎么才能做到自动把dll文件加载到客户端目录呢？
	我们可以写一个方法：获取server上的文件并创建。
	public void checkDll(){
		URL url;
		URLConnection conn ;
		serverpath = getParameter("serverpath");
		filePath = getParameter("filePath");
		try{
			url = new URL(serverpath+"?filePath="+filePath);
			// 开始连接url的时候，会去调用服务器的downDll方法，
			//将服务端被下载的文件以流的方式写入到网络中，可以参考我自己的serverpath
			conn = url.openConnection();
			conn.connect();
			//客户端将按照本地的路径，将网络中的流内容写入到文件中
			String userPath = System.getProperty("java.home");
			System.out.println("jre.home:"+userPath);
			localpath =  userPath + "\\bin\\mwrf32.dll";

			System.out.println("本地文件路径:"+localpath);
			File file = new File(localpath);
			if(!file.exists()){
				if(file.createNewFile()){
					System.out.println("创建文件成功!");
				}else{
					System.out.println("创建文件失败!");
				}
				getInfo(conn,file);	
			}else{
				System.out.println("动态链接库文件已经存在！");
			}
		}catch(Exception e){
			System.err.println("err:"+e.getMessage());
		}
	}
	然后通过流把文件写入到本地客户端想要写入的文件夹。
	public void getInfo(URLConnection conn,File file){
		String txt = new String();
		InputStream is;
		FileOutputStream fWriter = null;
		int size=0;
		try{
			is = conn.getInputStream();
			byte[] b = new byte[1024];
			fWriter = new FileOutputStream(file);
			while((size = is.read(b)) != -1){
				if(size ==1024){
					fWriter.write(b);
				}else{
					byte[] last = new byte[size];
					System.arraycopy(b, 0, last, 0, size);
					fWriter.write(last);
				}
			}
			is.close();
		}catch(Exception e){
			System.err.println("IO Exception!");
			txt = new String("File read failed!");
			try {
				fWriter.write(txt.getBytes());
			} catch (IOException exs) {
				System.err.println("err:"+e.getMessage());
			}
		} finally {
			try {
				fWriter.flush();
				fWriter.close();
			} catch (IOException e) {
				System.err.println("err:"+e.getMessage());
			}
		}
	}

	4.到此为止，基本的初始化算完成了。如果权限管理加载不了，设置权限管理器为null。
	System.setSecurityManager(null);


### 写一个动态加载类的方法。

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

### 覆写applet的 destory方法

	public void destroy(){
		System.out.println("=====app已销毁========");
	}


### 浏览器加载使用

	if(!window['_NativeJSInjectObj']){
	document.write('<applet id="rwcard" name="rwcard" code="com.xsf.readcard.app.ReadCardApplet.class" codebase="/Tools/applet/tqi/" scriptable="true"');
	document.write(' archive="readcardapp.jar,jna-4.1.0.jar,jna-platform-4.1.0.jar"');
	document.write(' height="1" width="1">');
	document.write(' <param name="serverpath"  id="serverpath" value="'+spath+'/tqi/controller/readCard/downDLL"/>');
	document.write(' <param name="filePath" id="filePath" value="/Tools/applet/tqi/mwrf32.dll"/>');	
	document.write(' alert(需要安装JAVA虚拟机(jre))</applet>');
	}

### 总结

	一个简单的applet应用已经完成，余下的就是下业务实现类了。具体实现类调用处理相应的dll即可。

	下篇写一下关于浏览器调用applet中方法时，数字签名问题。
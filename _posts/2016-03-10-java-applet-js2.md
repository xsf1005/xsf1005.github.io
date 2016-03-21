---
layout: post
title: 第二种 applet 调用 js
date: 2016-03-10
categories: applet
description: 如何使用applet 调用 js

---

### applet调用js总结 

	1.声明一个对象
	public static AppletUseJs INSTANCE ;（applet类对象）
	
	2.初始化
	@Override
	public void init() {
		INSTANCE = this;
		// TODO Auto-generated method stub
		System.out.println("========初始化=========");
		super.init();
	}
	
	3.创建一个公共调用业务方法
	public Object invoker(String clzname,String mname,String args[]){
		try {
			//业务类名
			System.out.println("clzname:"+clzname);
			//调用方法名
			System.out.println("mname:"+mname);
			System.out.println("args:"+args);
			Class<?> clz = Class.forName(clzname);
			Object obj = clz.newInstance();		
			List lst = new ArrayList();
			for (String str : args) {
				lst.add(str);
			}
			Method m = clz.getMethod(mname,List.class);
			return m.invoke(obj, lst);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return null;
	}
   
	4.创建applet调用js方法
	JSObject getCallWindowObj(){
		return JSObject.getWindow(AppletUseJs.this);
	}
	public void runJS(final String jsFun, final String parame) {
        new Thread(new Runnable() {
            public void run() {
                try {
                    String fun = "";
                    if(jsFun != null && !jsFun.equals("")){
                        fun = jsFun + "(" + parame + ")";
                       getCallWindowObj().eval("javascript:" + fun + ";");
                    }
                } catch (Throwable e) {
                	e.printStackTrace();
                    System.out.println("调用js出错=========" + e.getMessage());
                }
            }
        }).start();
    }

	5.applet销毁关闭
    public void destroy() {
        super.destroy();
        System.out.println("applet destroy");
        System.exit(0);
    }

### 写业务公共类

	public class TestJSCall {

	private String receiveCb = "comDataCallback";
	private String errorCb = "comErrorCallback";

	public void runJs(List s){
		System.out.println("s:"+s);
		System.out.println("执行eval函数");
		try{
			final String param = (String) s.get(0);
			AccessController.doPrivileged(new PrivilegedAction<Object>() {
				@Override
				public Object run() {
					// TODO Auto-generated method stub
					System.out.println("fun--->"+fun);
					AppletUseJs.INSTANCE.runJS(receiveCb,param);
					return null;
				}			
			});
		
		}catch(Exception e){
			System.out.println("-======js调用出错:"+e.getMessage());
			AppletUseJs.INSTANCE.runJS(errorCb,param);
			e.printStackTrace();
		}	
	}

### Html页面内容

	<body>
		<applet id="readcard" name="readcard" code="com.xsf.readcard.app.AppletUseJs" codebase="./cp/" archive="testjsapp.jar" width="0" height="0"
		scriptable="true"  MAYSCRIPT>需要安装JAVA虚拟机(jre)</applet>
	</body>

	<script type="text/javascript">
		function test(){		
			readcard.invoker("com.xsf.readcard.app.TestJSCall","runJs",["1","2"]);
		}	
		function comDataCallback(a){
			alert(a);
		}
	</script>
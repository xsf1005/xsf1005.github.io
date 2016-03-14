---
layout: post
title: 第一种 applet 调用 js
date: 2016-03-10
categories: applet
description: 如何使用applet 调用 js

---

### applet调用js总结 

	1.初始化applet
	
    public void init() {
        new Thread(){
            public void run(){
                try{
                    while(true){
                        Thread.sleep(1000);
                        System.out.println("init:" + getTime());
                    }            
                }
                catch(Exception e){
                    e.printStackTrace();
                }
            }
        }.start();
        String parame = "{\"success\":true,\"info\":\"加载完成\"}";
        System.out.println("加载完成=======================" + getTime());
       // runJS("initFinish", parame); //调用客户端js方法的
    }

	2.写一个公共方法调用类
    /**
     *
     * @param json 传入的字符串数据
     * @param fun 回调的函数
     */
    public void myMethod(final String fun,final String json){
        AccessController.doPrivileged(new PrivilegedAction<Object>() {
            public Object run() {
                new Thread(){
                    public void run(){
                        try{
                            System.out.println("方法调用:" + getTime());
                            String parame = "{\"success\":true,\"info\":\"执行完毕\"}";
                            runJS(fun, parame); //调用客户端js方法的
                        }
                        catch(Exception e){
                            e.printStackTrace();
                        }
                    }
                }.start();
                return null;
            }
        });
    }

	3.写一个applet执行调用js的方法，作为执行入口
    public void runJS(final String jsFun, final String parame) {
        new Thread(new Runnable() {
            public void run() {
                try {
                    String fun = "";
                    if(jsFun != null && !jsFun.equals("")){
                        fun = jsFun + "(" + parame + ")";
                        JSObject.getWindow(AppletTest.this).eval("javascript:" + fun + ";");
                    }
                } catch (Throwable e) {
                	e.printStackTrace();
                    System.out.println("调用js出错=========" + e.getMessage());
                }
            }
        }).start();
    }

	4.applet销毁关闭
    public void destroy() {
        super.destroy();
        System.out.println("applet destroy");
        System.exit(0);
    }

	5.写一个线程调用记录时间用
    public static String getTime() {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return sdf.format(new Date());
    }


### Html页面内容

	1.闭包的方式返回applet调用对象
	var appletObj = function(){
		this.myMethod = function(json, fun){
			try{
				this.getInstance().myMethod(json, fun);
				console.log("======myMethod:");
			}
			catch(e){
				console.error(getTime() + "====applet程序挂了，请刷新页面:" + e);
			}
		}
		var tool;
		this.getInstance = function(){
			console.log("======typeof tool:" + (typeof tool));
			console.log("======tool:" + tool);
			if(typeof tool == 'undefined'){
				//根据applet id 获取对象
				tool = $("#tool")[0];
			}
			return tool;
		}
		return this;
	}();

	//applet回调js方法
	$(function(){
		$("#btn").click(function(){
			var parame = {data1:'test1'};
			appletObj.myMethod("resultData",JSON.stringify(parame));
		});
	});
	
	//回调函数
	function resultData(jsonObj){
		console.log(getTime() + "========jsonObj:" + JSON.stringify(jsonObj));
		console.log(getTime() + "======btn1Click:" + jsonObj.success);
	}
	//初始化函数
	function initFinish(jsonObj){
		console.log("========applet init end:" + jsonObj.info);
	}
</script>
<body> 
<applet id="tool" code="com.xsf.readcard.app.AppletTest" codebase="./cp/" archive="AppletTest.jar" width="0" height="0" >
</applet>
</body>
---
layout:post
title:Maven搭建JFinal项目
date:2015-11-09
categories:JFinal
tags:[jfinal,maven]
description: 搭建jfinal项目maven流程
---

## 使用环境

- apache-maven-3.1.1,eclipse Kepler Service Release 1


## 开始搭建

1. 新建一个maven项目。

  ![image]https://github.com/xsf1005/xsf1005.github.io/tree/master/img/2015-11-09-jfinal/1.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/tree/master/img/2015-11-09-jfinal/2.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/tree/master/img/2015-11-09-jfinal/3.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/tree/master/img/2015-11-09-jfinal/4.png)
 
  到此为止，一个完整的maven项目结构就搭建完成了。
 
  [maven的依赖，查找库：](http://mvnrepository.com/)

## 添加jfinal

   1.官网上下载jfinal demo包。里面提供了一些常用jar包，直接把jar拷贝到lib中使用就ok，可以快速的搭建成功，这里就不说了，
在里面没有找到关于maven搭建的。[官网见:](http://www.jfinal.com/)
	
   2.搭建maven的依赖

  
   3.配置启动jetty
	
   JFinal.start("src/main/webapp", 8088, "/", 5);

## 注意事项

- 使用maven依赖的时候，不要用其他的jetty依赖，必须使用jfinal编译过的jetty依赖。

- 如果你不使用jfinal的启动模式的话，用jetty插件启动的话，这个依赖就不需要特定了，具体没有试过。所以如果想，请自己尝试。

- 启动的时候如果是maven项目，目录名称必须是src/main/webapp,如果是其他启动默认是webapp;

- 如果启动成功后，页面不能显示，需要注意的是，一个事项目路径，一个是是否加入了jetty对jsp支持的jar包，否则会提示，jetty不支持jsp。






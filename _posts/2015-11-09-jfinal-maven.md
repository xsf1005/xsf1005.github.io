---
layout:post
title:Maven搭建JFinal项目
date:2015-11-09
categories:JFinal
tags:[jfinal,maven]
description:搭建jfinal项目maven流程

---

## 使用环境

- apache-maven-3.1.1 , eclipse Kepler Service Release 1

## 开始搭建

  1. 新建一个maven项目。
	
  ![image]https://github.com/xsf1005/xsf1005.github.io/blob/master/img/2015-11-09-jfinal/1.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/blob/master/img/2015-11-09-jfinal/2.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/blob/master/img/2015-11-09-jfinal/3.png)

  ![image](https://github.com/xsf1005/xsf1005.github.io/blob/master/img/2015-11-09-jfinal/4.png)

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


## 配置调试过程中容易出现的问题

- 如果你时间久了没有使用maven项目，忽然使用容易，忽略一个问题，就是会新建一个webapp，然后把js,css,jsp等放入。这时候如果启动，访问的时候，会找不到路径，这个是思维问题，如果对maven结构很熟悉，不会犯这种错误，正确的是，把文件放在src/main/webapp下。一般tomcat启动用久了，容易犯这种错误。

- 如果你把文件放到了新建的webapp下，和一般项目的路径一样，那么在调试的过程中，你会去更改项目路径。最容易犯的错误就是，build path里面你把编译后生成的文件夹换成了webapp/web-inf/classes,然后去用jfinal的方式启动，会给你一个大大的错误，找不到类入口，然后你就茫然了，搞了半天，还是不行，然后换回到target目录下，这时候你启动的时候，启动成功了，但是找不到文件，一般是404或者500错误。这时候参照上面的把文件到到src/main/webapp.

- 一般如果长时间没有使用一个工具，或者是新手，请先明白一个项目目录构建，然后在调试，会避免一些盲区，和一些低级错误。


   总结:由于长时间没有使用maven,导致目录结构都忘记了，搞了几个小时，才搞明白，那个郁闷，哎，有时候一个小小的疏忽，就会造成让人无语的结果，以此谨记。


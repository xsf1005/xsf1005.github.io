---
layout: post 
title: Collection中的Map
date: 2015-03-05
categories: java
description: Collection 浅谈 Map

---

### HashMap
	
	- 特点：
		1.散列无序
		2.不可重复

	- 使用：
		1.可以使用iterator和foreach进行遍历

	- 分析：
		1.put()元素时，如果key不存在，返回null,如果key存在，返回已存在key值。
		2.遍历的时候，要把Map转成Entry然后获取元素。
		3.通过抽象类HashIterator来实现遍历Entry.
		4.HashMap是基于数组和链表来实现的，当key 进行hash后，产生碰撞或者说冲突后，采用链表的形式来存储，先入的房子链尾，
后人的放在链头。
		

### LinkedHashMap
	
	- 特点：
		1.有2种输出方式，插入输出（accessOrder默认的时候，输出顺序和插入顺序一致，属于插入顺序输出。），访问输出（LRU）
		2.不可重复
		3.双向链表维护访问顺序。

	- 使用：
		1.可以使用iterator和foreach进行遍历

	- 分析：
		1.put()元素时，如果key不存在，返回null,如果key存在，返回已存在key值。
		2.遍历的时候，要把Map转成Entry然后获取元素。
		3.通过抽象类LinkedHashIterator来实现遍历Entry.
		4.它相比于HashMap多了一个重新定义的Entry<K,V> header,在Entry 中实现中增加了2个属性，before,after.


### TreeMap
	
	- 特点：
		1.默认情况下，按照key升序排列。
		2.不可重复
		3.可以自定义排序顺序，使用Comparator接口来实现

	- 使用：
		1.可以使用iterator和foreach进行遍历

	- 分析：
		1.put()元素时，如果key不存在，返回null,如果key存在，返回已存在key值。
		2.遍历的时候，要把Map转成Entry然后获取元素。
		3.通过抽象类PrivateEntryIterator来实现遍历Entry.
		4.基于红黑树实现。


	

		
### HashMap和LinkedHashMap 效率问题

	经过1万，10万，100万，数据，50次循环插入，遍历，获取测试。
	插入效率：HashMap 优于LinkedHashMap, 原因可能是LinkedHashMap需要维护双向列表。
	迭代遍历效率：LinkedHashMap 优于 HashMap，原因可能是，HashMap遍历是数组循环，LinkedHashMap是链表查询。
	获取元素：数量少的情况下差别不大，超过100万，HashMap要优于 LinkedHashMap.


### 总结

   对应数组和链表来说，数组的获取元素快，有下标，链表的增删快。但是在Hash表中，是散列存储的，和数组正常的顺序存储是有区别的。

### 问题

   **为什么HashMap不是线程安全，在多线程的情况下会出现死循环，Cpu100%？**

   主要是在HashMap进行rehash扩容的时候造成了链表闭环导致的，由于多线程执行的时候，hash产生碰撞后，在链表插入的时候，执行下一个
节点的时候，线程进行了切换，2次执行的顺序相反，形成闭环。进行get的操作的时候，产生死循环，导致cpu100%。




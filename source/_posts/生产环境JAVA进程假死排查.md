---
title: 生产环境JAVA进程假死排查
date: 2024-08-27 16:34:01
tags: java
---

### 查看cpu信息
    top
    
### 打印对象的数量和占用内存情况
    jmap -histo:live 进程id | head -n 50

### 查看全连接队列
    ss -lnt

### 查看全连接队列溢出情况（隔一段执行一次）
    netstat -s | egrep "listen|LISTEN"

### 查看gc
    jstat -gcutil 进程id 1s

### 查看cpu高线程id
    top -p 进程id -H

### 查看线程快照
    jstack 进程id | grep -A 100 nid=线程id（十六进制  工具：'printf "0x%x\n" 线程id' 例如0x1802）

### 获取进程相关信息
	jstack 进程id > jstack.dump 2>&1 #获取Java线程的堆栈跟踪信息
	jinfo 进程id > jinfo.dump 2>&1 #获取Java进程的配置信息。
	jstat -gcutil 进程id > jstat-gcutil.dump 2>&1 #获取Java垃圾回收的使用情况。
	jstat -gccapacity 进程id > jstat-gccapacity.dump 2>&1 #获取Java垃圾回收的容量信息。
	jmap 进程id > jmap.dump 2>&1 #获取 Java 堆的基本信息
	jmap -heap 进程id > jmap-heap.dump 2>&1 #获取 Java 堆的详细信息
	jmap -histo 进程id > jmap-histo.dump 2>&1 #获取 Java 堆中对象的分布情况（即对象的数量和大小）
	/usr/sbin/lsof -p 进程id > lsof.dump #列出与指定进程相关的所有打开文件

### dump堆
    jmap -dump:live,format=b,file=core.hprof 进程id
### 堆太大dump不出来文件怎么办？
    ulimit -c unlimited #先解除coredump文件限制
    gcore 进程id #生成coredump文件
    jmap -dump:live,format=b,file=core.hprof `which java`  core.xxxx #coredump文件转储为java dump文件
	
### 参考
[获取一直FullGC下的java进程HeapDump的小技巧](https://plantegg.github.io/2020/01/04/%E8%8E%B7%E5%8F%96%E4%B8%80%E7%9B%B4FullGC%E4%B8%8B%E7%9A%84java%E8%BF%9B%E7%A8%8BHeapDump%E7%9A%84%E5%B0%8F%E6%8A%80%E5%B7%A7/)	
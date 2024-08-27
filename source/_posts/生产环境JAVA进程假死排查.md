---
title: 生产环境JAVA进程假死排查
date: 2024-08-27 16:34:01
tags: java
---

### 1.查看cpu信息
    top
    
### 2.打印对象的数量和占用内存情况
    jmap -histo:live 进程id | head -n 50

### 3.查看全连接队列
    ss -lnt

### 4.查看全连接队列溢出情况（隔一段执行一次）
    netstat -s | egrep "listen|LISTEN"

### 5.查看gc
    jstat -gcutil 进程id 1s

### 6.查看cpu高线程id
    top -p 进程id -H

### 7.查看线程快照
    jstack 进程id | grep -A 100 nid=线程id（十六进制  工具：'printf "0x%x\n" 线程id' 例如0x1802）

### 8.执行dump脚本
    PIDS=`ps -f | grep java | grep "$CONF_DIR" |awk '{print $2}'`
    DATE_DIR=`date +%Y%m%d%H%M%S`
	jstack $PID > $DATE_DIR/jstack-$PID.dump 2>&1
	echo -e ".\c"
	jinfo $PID > $DATE_DIR/jinfo-$PID.dump 2>&1
	echo -e ".\c"
	jstat -gcutil $PID > $DATE_DIR/jstat-gcutil-$PID.dump 2>&1
	echo -e ".\c"
	jstat -gccapacity $PID > $DATE_DIR/jstat-gccapacity-$PID.dump 2>&1
	echo -e ".\c"
	jmap $PID > $DATE_DIR/jmap-$PID.dump 2>&1
	echo -e ".\c"
	jmap -heap $PID > $DATE_DIR/jmap-heap-$PID.dump 2>&1
	echo -e ".\c"
	jmap -histo $PID > $DATE_DIR/jmap-histo-$PID.dump 2>&1
	echo -e ".\c"
	if [ -r /usr/sbin/lsof ]; then
	/usr/sbin/lsof -p $PID > $DATE_DIR/lsof-$PID.dump
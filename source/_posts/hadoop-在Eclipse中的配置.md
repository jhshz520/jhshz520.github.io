---
title: hadoop在Eclipse中的配置
date: 2020-01-06 12:13:47
categories: 大数据
tags: 工具
---
# 新版eclipse插件是隐藏的
1 配置插件，hadoop—-eclipse-plugin-2.7.3.jar,放入plugins目录

2 删除org.eclipse.update 
文件如果重启eclipse后还是看不到这个选项的话，就在eclipse文件夹的configuration文件夹中将org.eclipse.update文件夹删除后重启eclipse就行了。
3 安装hadoop 使用镜像站点安装
4 在eclipse中配置hadoop的路径选择
5 新建mapreduce locations，填写主机名和端口号
host Ip port 9000


# 修改ssh_config
能够使本地windows与虚拟机进行通讯

文件传递

注意此时用户名与密码我设置的是非root



## 配置hadoop 在ubuntu中

1 在host文件中建立对应关系

2 ubuntu 和debian一样使用ufw防火墙比iptables容易配置和管理的多

mkdir -p 创建一系列目录

# ssh无密码验证配置


# 修改主机名 
hostname 查看

更改etc下hostname权限，将里面的主机名改为ubuntu1
# 新增hadoop用户组和hadoop用户
groupadd hadoop 
useradd -g hadoop hadoop
passwd hadoop
# 创建新用户没有文件夹的问题
加 -m 参数
useradd 和adduser 各种发行版是不相同的
# 补救措施
mkdir /home/test
chown test:test -R /home/test

cp /etc/skel/.bash* /home/test/
(chown test:test /home/test/.bash*)

# 在sudoers中可以更改执行权限

# ssh 无密码验证配置
## 在/home/hadoop目录下执行以下操作
mkdir .ssh
ssh-keygen -t rsa


# 新建data文件并将权限赋值给hadoop用户
chown -R hadoop:hadoop hadoop

# 卸载自带的openjdk
sudo apt-get remove openjdk*
或者
# 选择默认的jdk
sudo update-alternatives --install /usr/bin/java java /opt/jdk1.7.0_25/bin/java 300  
sudo update-alternatives --install /usr/bin/javac javac /opt/jdk1.7.0_25/bin/javac 300  

sudo update-alternatives --config java  
sudo update-alternatives --config javac  

# hadoop报错 JAVA_HOME is not set and could not be found
修改/etc/hadoop/hadoop-env.sh的java——home

使用绝对路径
JAVA_HOME=/usr/local/java/jdk1.8.0_231
# jps报错
sudo update-alternatives --install /usr/bin/jps /usr/local/java/jdk1.8.0_231/bin/jps 1

参考文章地址：https://www.cnblogs.com/zimo-jing/p/7822108.html
参考文章地址 https://blog.csdn.net/sheshouzuomeiyou/article/details/91383608
# windows下hadoop配置
修改启动类 hadoop-env.cmd中的路径
```
set JAVA_HOME=C:\java\jdk1.8.0_74
```
修改配置文件（core-site.xml，hdfs-site.xml，mapred-site.xml，yarn-site.xml）
## core-site.xml
```
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
</configuration>
```
## hdfs-site.xml
```
<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>/E:/Apache_hadoop/hadoop-2.7.7/data/namenode</value>
	</property>
		<property>
		<name>dfs.datanode.data.dir</name>
		<value>/E:/Apache_hadoop/hadoop-2.7.7/data/datanode</value>
	</property>
</configuration>
```
## mapred-site.xml
```
<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
</configuration>
```
## yarn-site.xml
```
<configuration>

<!-- Site specific YARN configuration properties -->
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
		<property>
		<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
		<value>org.apache.hadoop.mapred.shuffleHandler</value>
	</property>	
</configuration>
```

## windows下启动hadoop

# 格式化namenode
```
hadoop namenode -format
```

```
运行hadoop/sbin
中的 start-all.sh
```
## localhost:8088查看Hadoop调度
## hadoop.dll（从）拷贝到 C:\Windows\System32
## localhost：50070查看dfs
## 重新namenode format的时候把datanode和namenode的文件内容删除
##  上传文件到hdfs
把t1上传到HDFS: hadoop fs -put d:\t1.txt /tmp_test/t1.txt （注意t1文件夹需要使用命令新建 hadoop fs -mkdir /tmp_test)
## hdfs 常用命令
```
            hadoop fs -mkdir /user/trunk          #建立目录/user/trunk

　　　　　　　　hadoop fs -ls /user                  #查看/user目录下的目录和文件

　　　　　　　　hadoop fs -lsr /user                 #递归查看/user目录下的目录和文件

　　　　　　　　hadoop fs -put test.txt /user/trunk      #上传test.txt文件至/user/trunk

　　　　　　　　hadoop fs -get /user/trunk/test.txt      #获取/user/trunk/test.txt文件

　　　　　　　　hadoop fs -cat /user/trunk/test.txt      #查看/user/trunk/test.txt文件内容

　　　　　　　　hadoop fs -tail /user/trunk/test.txt      #查看/user/trunk/test.txt文件的最后1000行

　　　　　　　　hadoop fs -rm /user/trunk/test.txt       #删除/user/trunk/test.txt文件

　　　　　　　　hadoop fs -help ls                   #查看ls命令的帮助文档
```

## localhost 基本等价于127.0.0.1

## log4j.properties文件一般建在src目录下
内容可以如下
```
# Configure logging for testing: optionally with log file  
  
#log4j.rootLogger=debug,appender  
log4j.rootLogger=info,appender  
#log4j.rootLogger=error,appender  
  
#\u8F93\u51FA\u5230\u63A7\u5236\u53F0  
log4j.appender.appender=org.apache.log4j.ConsoleAppender  
#\u6837\u5F0F\u4E3ATTCCLayout  
log4j.appender.appender.layout=org.apache.log4j.TTCCLayout
```
## 在eclipse中的locations 菜单下点击reconnect就能将eclipse中的内容与hdfs上的内容进行一个好的同步

## 如果进行某个骚操作之后发现Eclipse中的locations没了就去configuration文件夹里面删除org.Eclipse.update文件夹

## 还要一件很重要的问题现在eclipse 插件的安装不一定是在pluguins里面

新版Eclipse 插件的位置
1、找到安装目录的configuration -->> org.eclipse.update (为什么是在这个文件夹里面呢，新版本嘛，所以就在update的配置文件里咯) -->> platform.xml；

2、打开platform.xml文件，看到关键关联代码：url="file:/C:/Users/***/.p2/pool/",原来千呼万唤始出来，隐藏在此处：

3 原来新版安装Eclipse的时候，会在C盘用户文件生成两个关键的配置文件：.eclipse和.p2文件；

## cmd 切换盘符 直接输入D:就行了

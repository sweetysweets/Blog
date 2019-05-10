---
title: 云主机centos下配置hadoop+spark集群全记录
date: 2018-10-28 10:43:59
tags:
- hadoop
- spark
---

先放图= = 踩了无数坑终于特喵搞好了！

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/%E7%BB%88%E4%BA%8E%E5%A6%A5%E4%BA%86.png)

下面记录一下全过程

 <!--more-->

### 环境准备 
我买的是阿里云的轻量应用服务器，学生认证后9.5¥/月，三台服务器均安装centos系统

master：公网ip 139.196.75.247 内网ip 172.16.47.126
Slave01:公网ip 47.107.112.152 内网ip 172.16.17.40
slave02:公网ip 120.77.80.108 内网ip 172.16.20.154
以上全是centos 7.2 

在此基础上搭建java1.8+hadoop2.7+scala2.11.12+spark2.3.2集群

### 安装java 
yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel //此时所有的机器的java都安装在同一个地方

//所有的机器修改/etc/profile文件增加JAVA_HOME环境变量

```
vim /etc/profile
```

增加：

```
export JAVA_HOME=/usr/lib/jvm/java-openjdk
```


### 配置hostname 

在3台机器上分别执行

```
//主服务器
vim /etc/sysconfig/network
添加一行
hostname=master
:wq
//执行命令
hostname master
exit
重新ssh登进

//slave01
vim /etc/sysconfig/network

hostname=slave01
:wq
hostname slave01
exit
重新ssh登进

//slave02
vim /etc/sysconfig/network

hostname=slave02
:wq
hostname slave02
exit
重新ssh登进
```



### 配置hosts文件

```
vim /etc/hosts

//主服务器添加如下信息
172.16.47.126 master
47.107.112.152 slave01
120.77.80.108 slave02
//slave01
172.16.17.40 slave01
139.196.75.247 master
120.77.80.108 slave02
//slave02
172.16.20.154 slave02
139.196.75.247 master
47.107.112.152 slave01

注意:本机的hostname与内网ip对应
其他的hostname与外网ip对应
然后依次ping master,ping slave1
```



### 增加hadoop用户

可有可无，嫌麻烦的直接在root下执行即可

```
在各台机器上执行
useradd hadoop
passwd hadoop 
//这里会提醒hadoop密码不够安全，不要管，继续输入hadoop密码即可

//这里是设置权限，让hadoop有root的权限
vim /etc/sudoers
在root下面复制一行，将root改为hadoop
hadoop ALL=(ALL) ALL 

//配置完别忘了让其生效
source /etc/sudoers
```



### 配置ssh免登陆

```
3台服务器上都执行
ssh-keygen
然后一直回车
主服务器上执行：
 ssh-copy-id hadoop@slave01
 ssh-copy-id hadoop@slave02
从服务器上执行:
 ssh-copy-id hadoop@master
 ssh-copy-id hadoop@slave02
 
//这一步需要输入密码
//如果你使用root用户配置，那就是 ssh-copy-id root@slave02
```

### 关闭各机器防火墙

```
systemctl start firewalld
firewall-cmd --permanent --zone=public --add-port=50070/tcp  //namenode web端口
firewall-cmd --permanent --zone=public --add-port=50070/udp
firewall-cmd --permanent --zone=public --add-port=9000/tcp   //namenode rpc端口
firewall-cmd --permanent --zone=public --add-port=9000/udp
firewall-cmd --permanent --zone=public --add-port=50010/udp  //datanode rpc端口
firewall-cmd --permanent --zone=public --add-port=50010/udp
firewall-cmd --permanent --zone=public --add-port=50075/udp  //下载文件端口
firewall-cmd --permanent --zone=public --add-port=50075/udp
firewall-cmd --permanent --zone=public --add-port=8031/tcp   //nodemanager rpc端口
firewall-cmd --permanent --zone=public --add-port=8031/udp
firewall-cmd --reload 
以上所有端口在namenode和datanode均全部开启
需要什么端口可以自行开放

******推荐一种暴力方法*********
firewall-cmd --permanent --zone=public --add-port=10-50100/tcp 
firewall-cmd --permanent --zone=public --add-port=10-50100/udp 
firewall-cmd --reload
```

### 安装hadoop

首先我在自己电脑上下载了[hadoop-2.7.7](https://archive.apache.org/dist/hadoop/core/hadoop-2.7.7/)的压缩文件，用filezilla传到服务器的某个文件夹下，也可以scp或者直接在服务器上下载

```
su hadoop  //如果root用户，不需要切换hadoop

cd /home/hadoop
tar xvf hadoop-2.7.7.tar.gz //将hadoop解压到hadoop-2.7.7文件夹下
```

### 配置hadoop

hadoop的配置文件在hadoop目录下/etc/hadoop文件夹下

先`cd /etc/hadoop`,然后配置各文件

##### hadoop-env.sh

```
vim hadoop-env.sh
将JAVA_HOME修改为本机的JAVA_HOME
JAVA_HOME=/usr/lib/jvm/java-openjdk
```

##### core-site.xml

```
vim core-site.xml

<configuration>
	<property>
 		<name>fs.defaultFS</name>
		<value>hdfs://master:9000/</value>
	</property>
	<property>
 		<name>io.file.buffer.size</name>
		<value>131072</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/home/hadoop/hadoop-2.7.7/tmp</value>
	</property>
</configuration>
```

##### hdfs-site.xml
```
vim hdfs-site.xml

<configuration>
	<property>
		<name>dfs.namenode.secondary.http-address</name>
		<value>master:50090</value>
	</property>
	<property>
		<name>dfs.namenode.name.dir</name>
		<value>file:///home/hadoop/hadoop-2.7.7/hdfs/namenode</value>
		<description>NameNode directory for namespace and transaction logs storage.</description>
	</property>
	<property>
		<name>dfs.datanode.data.dir</name>
		<value>file:///home/hadoop/hadoop-2.7.7/hdfs/datanode</value>
		<description>DataNode directory</description>
	</property>
	<property>
		<name>dfs.replication</name>
		<value>2</value>
	</property>
</configuration>
```



##### mapred-site.xml

```
//这里可能没有mapred-site.xml文件，需要以下操作
cp mapred-site.xml.template mapred-site.xml
vim mapred-site.html
//添加如下信息
<configuration>
 <property>
 	<name>mapreduce.framework.name</name>
  	<value>yarn</value>
  </property>
  <property>
          <name>mapreduce.jobhistory.address</name>
          <value>master:10020</value>
  </property>
  <property>
          <name>mapreduce.jobhistory.address</name>
          <value>master:19888</value>
  </property>
</configuration>
```

##### yarn-site.xml

```
vim yarn-site.xml
//如果没有这个文件，看看有没有template，若没有，新建一个也可
<?xml version="1.0"?>

<configuration>
 <property>
 	<name>mapreduce.framework.name</name>
  	<value>yarn</value>
  </property>
  <property>
          <name>mapreduce.jobhistory.address</name>
          <value>master:10020</value>
  </property>
  <property>
          <name>mapreduce.jobhistory.address</name>
          <value>master:19888</value>
  </property>
</configuration>
```

### 分发

将hadoop打包发送到两台slave主机，过程较慢，要有耐心

```
tar -zcvf hadoop-2.7.7.tar.gz hadoop-2.7.7/
scp -r hadoop-2.7.7.tar.gz hadoop@slave01:/home/hadoop/hadoop-2.7.7
scp -r hadoop-2.7.7.tar.gz hadoop@slave02:/home/hadoop/hadoop-2.7.7
```

### 配置master节点并启动

##### slaves配置

```
cd /etc/hadoop/
vim slaves
然后在namenode上，（仅在namenode上修改slaves文件） 
vim slaves (slaves文件在hadoop/etc目录中) 
slaves文件内容： 
slave1 
slave2 
```

##### 格式化hdfs

//进入hadoop文件夹，执行

```
hadoop namenode -format

//如果执行不了，请添加hadoop的环境变量,参见[文章尾部](#参考)
//注意执行一次就可以了。。多执行会出错。。
```

执行截图：

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/format.png)

##### 启动hadoop

```
//进入hadoop文件夹，执行
sbin/start-dfs.sh 
sbin/start-yarn.sh 
//或者下面的命令
sbin/start-all.sh
```

到此我们的hadoop集群就搭建好了，查看是否运行成功使用`jps`命令

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/jps.png)



![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/jps2.png)

### 动态扩容 

直接scp一份到一个服务器，起一个datanode即可 ,非常方便

### 安装scala

我下载了[Scala 2.11.12](https://www.scala-lang.org/download/2.11.12.html)

使用filezilla或scp传到服务器上

```
首先建立scala存放目录
mkdir /usr/local/scala 
然后释放scala并安装至指定目录
tar -xvzf scala-2.11.12.tgz && mv scala-2.11.12 /usr/local/scala/
```

##### 配置scala环境变量

```
vim /etc/profile
在PATH后追加scala的二进制位置，
这里是： :/usr/local/scala/scala-2.11.8/bin 
source /etc/profile
```

##### 验证安装

```
scala -version
//输出版本信息则ok
```

我的输出如下：

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/scala.png)

> ps
>
> 安装scala需要java1.8环境，一般使用sun的jdk1.8，我装的openjdk没有release文件夹，但不影响后续操作

### 安装spark

我下载了 [spark-2.3.2-bin-hadoop2.7.tgz](https://www.apache.org/dyn/closer.lua/spark/spark-2.3.2/spark-2.3.2-bin-hadoop2.7.tgz)

使用filezilla或scp传到服务器上

```
tar xvf spark-2.3.2-bin-hadoop2.7.tgz

//名字太长了重命名一下
mv spark-2.3.2-bin-hadoop2.7.tgz spark
```

### 配置spark

spark的配置文件存放在spark安装目录下的conf文件夹，三台机子的配置一摸一样，可以直接配好了scp过去（推荐filezilla，贼快）

```
cd /conf
//spark配置文件需要自己从template复制
```

##### spark-env.sh

```
cp spark-env.sh.template spark-env.sh
vim spark-env.sh

//请将环境变量设为自己的目录
export SCALA_HOME=/usr/local/scala/scala-2.11.12
export JAVA_HOME=/usr/lib/jvm/java-openjdk
export SPARK_MASTER_HOST=master
export SPARK_MASTER_PORT=7077
export SPARK_WORKER_MEMORY=500m
export SPARK_EXECUTOR_MEMORY=100m
export SPARK_DRIVER_MEMORY=100m
export HADOOP_CONF_DIR=/home/hadoop/hadoop-2.7.7/etc/hadoop
export PYSPARK_PYTHON=/usr/bin/python3
export PYSPARK_DRIVER_PYTHON=/usr/bin/python3
```

##### slaves

```
cp slaves.template slaves
vim slaves

//添加
master
slave01
slave02
```

### 分发

将配好的spark打包发给slave01和slave02，参见hadoop的分发

### 启动spark

进入spark安装目录，

```
sbin/start-all.sh 
```

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/hadoop-spark-conf/spark-start.png)

可以在7077端口查看spark信息

### 参考

环境变量文件一共添加的内容如下：

```
export HADOOP_HOME=/home/hadoop/hadoop-2.7.7
export PATH=$PATH:$HADOOP_HOME/bin:/usr/local/scala/scala-2.11.12/bin
export JAVA_HOME=/usr/lib/jvm/java-openjdk
```

### 后记

太坑啦，本来想本地主机配，局域网环境不好设置，mac和windows又有ssh的问题，虚拟机还有配置网络等等一系列乱七八糟的问题，最后用云主机踩的坑比较少，不过还是遇到一些问题，记录一下

>  ps.  hadoop启动问题可以查看hadoop安装目录下的logs中的log文件看看报了什么错再搜索解决。

##### There are 0 datanode(s) running and no node(s) are excluded in this operation.

出现上述问题可能是格式化两次hadoop，导致datanode和namenode的id号不同

解决方法1：重启linux,再使用start-dfs.sh和start-yarn.sh 重启一下hadoop

解决办法2：找到hadoop安装目录下 hadoop-2.7.7/hdfs/namenode里面的current文件夹删除

然后从新执行一下 hadoop namenode -format

再使用start-dfs.sh和start-yarn.sh 重启一下hadoop

用jps命令看一下就可以看见datanode已经启动了

暴力解决方法:

删除掉各个节点上面的tmp(可能与我设置的路径和文件不同,对应你自己在hdfs-site.xml中设置的dfs.name.dir路径就好了),然后格式化集群,最后重启集群,搞定。


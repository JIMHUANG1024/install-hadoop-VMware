# install-hadoop-VMware
# 安装虚拟机、配置hadoop

#### 1. 下载VMware虚拟机、linux环境——Centos7

**1.1 VMware虚拟机安装：** 

https://pan.baidu.com/s/1cSzZpvkbrEL6wwy6MYjP4g

提取码：pcww

+ 虚拟机安装，傻瓜式安装

**1.2 Centos7下载安装**

[Download (centos.org)](https://www.centos.org/download/)（要登入账号）

**1.3 jdk下载**

(https://www.oracle.com/java/technologies/downloads/#java8)

**1.4 hadoop下载**

[Index of /apache/hadoop/common (tsinghua.edu.cn)](https://mirrors.tuna.tsinghua.edu.cn/apache/hadoop/common/)

#### 2. 安装linux系统

安装三个虚拟机

#### 3. 配置静态网络、关闭防火墙

```shell
cd /etc

cd sysconfig

cd network-scripts/

vi ifcfg-ens33

```

```python
i # 修改
BOOTROTO="static"
IPADDR="192.168.153.x"
NETMASK="255.255.255.0"
GATEWAY="192.168.153.2"
DNS1="180.76.76.76" #百度公共DNS
:wq # 推出
```

```shell
service network restart

systemctl stop firewalld

systemctl disable firewalld

systemctl status firewalld

```

#### 4. 配置主机名、编辑hosts文件

```shell
hostnamectl set-hostname your_name

reboot
```

```shell
vi /ect/hosts

192.168.153.10 master
192.168.153.11 s1
192.168.153.12 s2
```

#### 5. 使用SSH传输文件

```shell
yum install lrzsz -y

rz
```

#### 6. SSH免密码登录

Hadoop运行过程中需要管理远端Hadoop守护进程。启动后，NameNode是通过SSH（Secure Shell）来无密码登录启动和停止各个DataNode上的各种守护进程的。同理DataNode上也能使用SSH无密码登录到NameNode。一般情况下，只需要从master单向SSH到slave

```shell
ssh-keygen -b 1024 -t rsa # 一路回车

ssh-copy-id s1

ssh-copy-id s2
```

设置权限

```shell
cd .ssh/
chmod 600 authorized_keys 
# 600 是权限的代表，6是二进制110表示可读可写
```

#### 7.时间同步

```shell
crontab -e
0 1 * * * /usr/sbin/ntpdate cn.pool.ntp.org
```

#### 8. 解压hadoop包、安装jdk

```shell
tar -xzvf name -C
tar -czvf name 

rpm -ivh jdk-8u151-linux-x64.rpm

vim .bash_profile

	export JAVA_HOME=/usr/java/jdk1.8.0_333-amd64
                                    
	export PATH=$PATH:$JAVA_HOME/bin

source .bash_profile

scp -r jdk1.8.0_171 root@s1:~/
```

#### 9. 修改hadoop配置文件

9.1 core-site.xml

```sql
    <property>
    	<name>fs.defaultFS</name>  
      	<value>hdfs://master:9000</value>  
      	</property>  
    <property>
      	<name>hadoop.tmp.dir</name>
      	<value>/root/hadoopdata</value>
	</property>
```

9.2 hadoop-env.sh

```shell
export JAVA_HOME=/root/jdk1.8.0_333
```

9.3 yarn-env.sh

```shell
export JAVA_HOME=/root/jdk1.8.0_333
```

9.4 hdfs-site.xml

```sql
<property>
     <name>dfs.replication</name>
     <value>2</value> # 复制数据个数
</property>
```

9.5 mapred-site.xml

```sql
<property>
		<!--class, yarn, local-->
         <name>mapreduce.framework.name</name>
         <value>yarn</value>
</property>

mv mapred-site.xml.template mapred-site.xml
```

9.6 yarn-site.xml

```sql
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>master</value>
</property> 
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
```

9.7 slave(workers)

添加从机名字

```shell
s1
s2
```

9.8 在/root/.bash_profile添加Hadoop路径

```shell
export HADOOP_HOME=/root/hadoop-3.3.1
export PATH=$HADOOP_HOME/bin:$PATH_HOME/sbin:$PATH

source .bash_profile
```

#### 10. 格式化文件系统

```shell
hdfs namenode -format

HDFS_DATANODE_USER=root
HADOOP_SECURE_DN_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```












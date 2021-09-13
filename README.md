# Hadoop

- Hadoop is a standard framework for processing and storing big data

- It is designed to be deployed across a network of hundreds or even thousands of dedicated servers.

- All these machines work together to deal with the massive volume and variety of incoming datasets.

- For a small project, Hadoop can be installed in a single node mode.

# Installation

(1) Install Open JDK-8: `sudo apt install openjdk-8-jdk -y`

**NOTE**: If you have multiple versions of JDK's installed, you can switch between them by running: -

`sudo update-alternatives --config java` & `sudo update-alternatives --config javac`

(Refer: <https://askubuntu.com/questions/1171037/how-to-set-java-path-to-jdk-8>)

- Verify jdk: `java -version` & `javac -version`

(2) Install OpenSSH Server / Client

- `sudo apt update`
- `sudo apt install openssh-server -y`
- `sudo apt install openssh-client -y`

(3) Setup a non root user for hadoop

- `sudo adduser hadoop`
- `sudo usermod -aG sudo hadoop`
- `su - hadoop`

(4) Passwordless SSH Settings for hadoop

- If you were to ssh into the localhost, it would ask you for a password, try `ssh localhost`

- So, enable a password-less ssh using the openssh-server and client we installed

- `ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa`
- `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`
- `chmod 0600 ~/.ssh/authorized_keys`
- `ssh localhost`

(5) Download Hadoop from apache org

- Download the binary of version hadoop-3.2.2.tar.gz
- `tar -xvzf hadoop-3.2.2.tar.gz`

**NOTE**-

- Hadoop is mainly used in full distributed mode on large clusters of networked servers.
- For the demo, we will use a single java process based hadoop installation which is also known as a **Pseudo-Distributed Mode**

(6) Hadoop Configuration

- `cd /home/hadoop/hadoop-3.2.2`
- `sudo nano ~/.bashrc`
- Add the following lines

```
# Hadoop Config
export HADOOP_HOME=/home/hadoop/hadoop-3.2.2
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```

- `source ~/.bashrc`
  
(7) Modify the hadoop-env.sh

- `cd /home/hadoop/hadoop-3.2.2`

Get the path to Java Home Dir

- `which javac`
- `readlink -f /usr/bin/javac`
- `sudo nano etc/hadoop/hadoop-env.sh`
- Go to the line #export JAVA_HOME=, add the following
- `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64`

(8) Modify XML's

- **core-site.xml**: `sudo nano etc/hadoop/core-site.xml`

```
<configuration>
 <property>
   <name>hadoop.tmp.dir</name>
   <value>/home/hadoop/hadoop-3.2.2/tmpdata</value>
 </property>
 <property>
   <name>fs.default.name</name>
   <value>hdfs://127.0.0.1:9000</value>
 </property>
</configuration>
```

- **hdfs-site.xml**: `sudo nano etc/hadoop/hdfs-site.xml`

```
<configuration>
 <property>
   <name>dfs.data.dir</name>
   <value>/home/hadoop/hadoop-3.2.2/dfsdata/namenode</value>
 </property>
 <property>
   <name>dfs.data.dir</name>
   <value>/home/hadoop/hadoop-3.2.2/dfsdata/datanode</value>
 </property>
 <property>
   <name>dfs.replication</name>
   <value>1</value>
 </property>
</configuration>
```

- **mapred-site.xml**: `sudo nano etc/hadoop/mapred-site.xml`

```
<configuration>
 <property>
   <name>mapreduce.framework.name</name>
   <value>yarn</value>
 </property>
</configuration>
```

- **yarn-site.xml**: `sudo nano etc/hadoop/yarn-site.xml`

```
<configuration>
 <property>
   <name>yarn.nodemanager.aux-services</name>
   <value>mapreduce_shuffle</value>
 </property>
 <property>
   <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
   <value>org.apache.hadoop.mapred.ShuffleHandler</value>
 </property>
 <property>
   <name>yarn.resourcemanager.hostname</name>
   <value>127.0.0.1</value>
 </property>
 <property>
   <name>yarn.acl.enable</name>
   <value>0</value>
 </property>
 <property>
   <name>yarn.nodemanager.env-whitelist</name>       <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
 </property>
</configuration>
```

(9) Format HDFS Name Node

- `cd /home/hadoop/hadoop-3.2.2`
- `hdfs namenode -format`

After a successful format, namenode will shutdown

(10) Start Hadoop Cluster

- `sbin/start-dfs.sh`

**NOTE**

You might encounter an error like **Permission Denied error while running start-dfs.sh**. (Ref: <https://stackoverflow.com/questions/42756555/permission-denied-error-while-running-start-dfs-sh>)
To fix this `sudo nano ~/.bashrc` and add the following

- `export PDSH_RCMD_TYPE=ssh`
- `source ~/.bashrc`

(11) Start Yarn Resource and Node Manager

- `sbin/start-yarn.sh`

(12) Stop Hadoop Cluster

- `sbin/stop-dfs.sh`

(11) Stop Yarn Resource and Node Manager

- `sbin/stop-yarn.sh`

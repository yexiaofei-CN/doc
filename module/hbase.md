## hbase，zookeeper分布式集群安装

- jar包选用 

```
  hadoop - 2.7.3
  zookeeper - 3.4.8
  hbase - 1.2.5
```
## Zookeeper分布式安装

- 在etc/hosts中配置集群节点
```
  192.168.56.100 master
  192.168.56.101 slave1
  192.168.56.102 slave2
```

- 将zookeeper/conf目录下面的 zoo_sample.cfg修改为zoo.cfg 添加内容：

```
  1.	tickTime=2000  
  2.	dataDir=/home/hadoop/storage/zookeeper  
  3.	clientPort=2181  
  4.	initLimit=5  
  5.	syncLimit=2  
  6.	server.1=slave-01:2888:3888  
  7.	server.2=slave-02:2888:3888  
  8.	server.3=slave-03:2888:3888 
```

- 远程复制分发安装软件

```
scp -r  hbase root@192.168.56.103:/usr/local/
```
- 设置myid

```
在我们配置的dataDir指定的目录下面，创建一个myid文件，里面内容为一个数字，用来标识当前主机，conf/zoo.cfg文件中配置的server.X中X为什么数字，则myid文件中就输入这个数字
```

- etc/profile中添加Path

```
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

- 启动Zookeeper集群

```
zkServer.sh start
```

- 安装验证

```
zkServer.sh status
```

## HBASE分布式安装
- 在etc/profile中配置hbasepath

```
  export HBASE_HOME=/usr/local/hbase
  export PATH=$PATH:$HBASE_HOME/bin

```

- 更改hbase/conf  hbase-site.xml

```
     <!--hbase.rootdir 对应hdfs中的 core-site.xml中的fs.defaultFS-->
     <configuration>
          <!-- 指定HBase在HDFS上面创建的目录名hbase -->
         <property>
             <name>hbase.rootdir</name>
             <value>hdfs://master:9000/hbase</value>
     </property>
        <property>
            <name>hbase.master.info.port</name>
            <value>60010</value>
        </property>
     <!—对应zookeeper zoo.cfg中 dataDir路径 -->
     <property>
         <name>hbase.zookeeper.property.dataDir</name>
         <value>/opt/zookeeper</value>
         <description>Property from ZooKeeper config zoo.cfg.
         The directory where the snapshot is stored.
         </description>
       </property>
         <!-- 开启集群运行方式 -->
         <property>
             <name>hbase.cluster.distributed</name>
             <value>true</value>
         </property>
         <property>
             <name>hbase.tmp.dir</name>
             <value>/var/hbase</value>
         </property>
         <property>
             <name>hbase.zookeeper.quorum</name>
             <value>master,slave1,slave2</value>
         </property>
     </configuration>
     
   hbase.zookeeper.property.dataDir 一定要配置 我入了这个坑
   不配置启动hbase会报ERROR: Can't get master address from ZooKeeper; znode data == null错误
   网上说的报错原因基本我都试过了 。没用 这是找不到zookeeper得节点位置。
```

- 修改hbase-env.sh

```
   export JAVA_HOME=/usr/java/default
   <!--对应 hbase.tmp.dir 中得路径 不配置关闭hbase找不到pid -->
   export HBASE_PID_DIR=/var/hbase/pids
   <!--关闭内置zk-->
   export HBASE_MANAGES_ZK=false
```

- 修改regionservers

```
 <!--我这里配置得两台-->
 slave1 slave2
```

- 创建backup-masters文件 这个是当hbase主宕掉后备份的master 里面写 salve3，当master宕掉 salve3就是主
     
- 将hadoop中的 core-site.xml hdfs-site.xml复制到hbase/conf的路径下

- 启动hbase  先启动hadoop ZK

```
  启动HBase集群：
  bin/start-hbase.sh
  单独启动一个HMaster进程：
  bin/hbase-daemon.sh start master
  单独停止一个HMaster进程：
  bin/hbase-daemon.sh stop master
  单独启动一个HRegionServer进程：
  bin/hbase-daemon.sh start regionserver
  单独停止一个HRegionServer进程：
  bin/hbase-daemon.sh stop regionserver
```

## 至此 hbase就配置完了
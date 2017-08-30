### hive整合hbase

### hbase得安装
- [查看hbase安装](hbase.md) 

###hive得安装
- [查看hive安装](hive.md)

###hive多用户模式安装
- [查看hive多用户模式安装](hive多用户模式.md)

- 看完前三个在看下面得整合就都搞定了

- 将 hbase-common-1.1.1.jar zookeeper-3.4.6.jar 拷贝到hive/lib中

- hive整合hbase 在hive-site.xml 加入

```$xslt
  <property>
          <name>hive.aux.jars.path</name>
          <value>file:///usr/local/hive/lib/hive-hbase-handler-2.3.0.jar,file:///usr/local/hive/lib/guava-14.0.1.jar,file:///usr/local/hive/lib/hbase-common-1.1.1.jar,file:///usr/local/hive/lib/zookeeper-3.4.6.jar</value>
    </property>
    <property>
             <name>hbase.zookeeper.quorum</name>
             <value>master,slave1,slave2</value>
    </property>

```

- hive与hbase通信是通过 hive-hbase-handler-2.3.0.jar

- 这样在hive中创建表就可以在hbase中看到

### hive整合hbase

- hbase安装 http://blog.csdn.net/qq_33127597/article/details/76685928

- hive 安装 http://blog.csdn.net/qq_33127597/article/details/76210417

- hive多用户模式安装（服务端 客户端）一般公司都这样装 http://blog.csdn.net/qq_33127597/article/details/77611577

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

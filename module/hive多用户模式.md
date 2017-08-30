## hive 多用户模式安装 

- hive多用户模式分开服务端和客户端

- hive单用户模式参考http://blog.csdn.net/qq_33127597/article/details/76210417

- 服务端配置如下
```
 <configuration>
    <property>
         <name>hive.metastore.warehouse.dir</name>
         <value>/opt/hive/warehouse</value>
     </property>
     <property>
         <name>javax.jdo.option.ConnectionURL</name>
         <value>jdbc:mysql://192.168.137.10:3306/hive?characterEncoding=UTF-8</value>
     </property>
     <property>
         <name>javax.jdo.option.ConnectionDriverName</name>
         <value>com.mysql.jdbc.Driver</value>
     </property>
     <property>
         <name>javax.jdo.option.ConnectionUserName</name>
         <value>hive</value>
     </property>
     <property>
         <name>javax.jdo.option.ConnectionPassword</name>
         <value>mysql</value>
     </property>
     <property>
     <name>hive.server2.transport.mode</name>
     <value>binary</value>
     <description>
       Expects one of [binary, http].
       Transport mode of HiveServer2.
     </description>
    </property>
 </configuration>
```

- 客户端

```
 <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/opt/hive/warehouse</value>
  </property>
  <property>
        <name>hive.metastore.local</name>
        <value>false</value>
  </property>
        <value>thrift://slave1:9083</value>
        <description>thrift URI</description>
  </property>

```

- 启动hive服务端程序
```$xslt
hive --service metastore
客户端直接用hive打开即可
```

- hive2 beeline启动方式
```$xslt
服务端启动  hiveserver2
客户端启动 beeline
!connect jdbc:hive2://192.168.137.101:10000 默认密码为none
```


Hive安装

1.   解压Hive，到/usr/local目录，将解压后的目录名mv为hive 
设定环境变量HADOOP_HOME，HIVE_HOME，将bin目录加入到PATH中
export HADOOP_HOME=/usr/local/hadoop
export HIVE_HOME=/usr/local/hive
exportPATH=$PATH:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
export PATH=$PATH:$HIVE_HOME/bin
 
2.    cd /usr/local/hive/conf
cp hive-default.xml.template hive-site.xml
修改hive.metastore.schema.verification，设定为false
创建/usr/local/hive/tmp目录，替换${system:java.io.tmpdir}为该目录
 
3.       安装mysql
4.      为Hive建立相应的MySQL账户,并赋予足够的权限,执行命令如下:
   mysql -uroot -pmysql
mysql> CREATE USER 'hive' IDENTIFIED BY 'mysql';
mysql> GRANT ALL PRIVILEGES ON *.* TO'hive'@'%' WITH GRANT OPTION;
mysql> flush privileges;
 
默认情况下Mysql只允许本地登录，所以需要修改配置文件将地址绑定给注释掉：
 
root@ubuntu:~# sudo gedit/etc/mysql/my.cnf
找到如下内容：
 
# Instead of skip-networkingthe default is now to listen only on
# localhost which is morecompatible and is not less secure.
#bind-address           = 127.0.0.1  <---注释掉这一行就可以远程登录了
 
重启mysql服务：
sudo service mysql restart
5.       建立 Hive 专用的元数据库，记得创建时用刚才创建的“hive”账号登陆。
mysql> exit;
mysql -uhive -pmysql
mysql> create database hive;
6.       在Hive的conf目录下的文件“hive-site.xml”中增加如下配置:
<configuration>
    <property>
       <name>hive.metastore.local</name>
        <value>true</value>
    </property>
    <property>
       <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://192.168.11.157:3306/hive?characterEncoding=UTF-8</value>
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
</configuration>
7.      把MySQL的JDBC驱动包复制到Hive的lib目录下。
JDBC驱动包的版本:mysql-connector-java-5.1.40-bin.jar
8.      启动hive 报错
1.      Caused by:MetaException(message:Hive metastore database is not initialized. Please useschematool (e.g. ./schematool -initSchema -dbType ...) to create the schema. Ifneeded, don't forget to include the option to auto-create the underlyingdatabase in your JDBC connection string (e.g. ?createDatabaseIfNotExist=truefor mysql))
 
解决： schematool -initSchema -dbType mysql
 
2.  Caused by:javax.jdo.JDOUserException: Could not create"increment"/"table" value-generation container`SEQUENCE_TABLE` since autoCreate flags do not allow it.
NestedThrowables:
org.datanucleus.exceptions.NucleusUserException: Could notcreate "increment"/"table" value-generation container`SEQUENCE_TABLE` since autoCreate flags do not allow it.
 
解决: 最后发现是mysql数据库的binlog_format参数设置不正确，
原来设置的是STATEMENT，修改为MIXED
修改方法，在/etc/my.cnf文件中加上binlog_format=MIXED
然后重启mysql数据库
问题解决
9、验证Hive配置是否有误，进入Hive的shell新建表,在MySQL的Hive数据库中可以看到相应的元数据库信息。
1)在Hive上建立数据表
hive>CREATE TABLE xp(id INT,name string) ROW FORMAT DELIMITED FIELDS TERMINATED BY'\t';
2)从 MySQL 数据库上查看元数据信息
用到的 SQL 语句:
use hive;//使用 hive 数据库库
showtables;//显示 hive 数据库中的数据表
select *from TBLS;//查看 hive 的元数据信息
到此Hive集成Mysql作为元数据已完成。
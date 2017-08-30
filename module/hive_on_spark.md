## hive on spark 安装 

- jar包选用

```
  hadoop - 2.7.3
  hive - 2.3.0
  spark - 2.0.0
  scala - 2.11.8
```

- spark安装

```
   引用官网
   https://cwiki.apache.org//confluence/display/Hive/Hive+on+Spark:+Getting+Started
   请注意，您必须具有不包括Hive jar 的Spark版本  。意思是一个没有用Hive配置文件构建的。
   我选用spark2.0 因为hive现在最新版本支持spark2.0版本 下载hive源码pom.xml中支持spark
   2.0版本
```

```
    下载sprk-2.0版本解压 用maven编译 
    编译前必须先安装scala maven
     ./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.7,parquet-provided"
    编译时间长 耐心等待
    编译完成后在当前文件下 生成 spark-2.2.0-bin-hadoop2-without-hive.tgz
```

- 配置 spark-env.sh

```
    解压 后配置 spark-env.sh
    export SCALA_HOME=/usr/local/scala
    export JAVA_HOME=/usr/java/default
    export HADOOP_HOME=/usr/local/hadoop
    export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)
    export SPARK_MASTER_IP=192.168.137.100
    export SPARK_LOCAL_DIRS=/usr/local/spark
    export SPARK_WORKER_MEMORY=1g
    export SPARK_DRIVER_MEMORY=1g
    # spark master端口 默认7077 下面是可选的
    export SPARK_MASTER_PORT=7077
    export SPARK_WORKER_PORT=7078
    
    配置有很多 这是基础得
    export SPARK_DIST_CLASSPATH 必须配置 不配置会报
    Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder
```

- 配置spark-defaultt.conf

```
   spark.master                     yarn-cluster
   spark.home                       /usr/local/spark
   spark.eventLog.enabled           true
   spark.eventLog.dir               hdfs://master:9000/spark-log
   spark.serializer                 org.apache.spark.serializer.KryoSerializer
   spark.driver.memory              1g
   spark.executor.memory            1g
   spark.executor.extraJavaOptions  -XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"
```

-- 配置hive

```
   将 spark/jars 文件下得scala-library  spark-core spark-network-common包复制到hive/lib下     
   hive-site.xml添加
   <property>
   	   <name>hive.enable.spark.execution.engine</name>
   	   <value>true</value>
   	</property>
   	<property>
   		<name>spark.home</name>
   		<value>/usr/local/spark</value>
   	</property>
   	<property>
   		<name>spark.master</name>
   		<value>yarn-client</value>
   	</property>
   	<property>
   		<name>spark.enentLog.enabled</name>
   		<value>true</value>
   	</property>
   	<property>
   		<name>spark.enentLog.dir</name>
   		<value>hdfs://master:9000/spark-log</value>
   	</property>
   	<property>
   		<name>spark.serializer</name>
   		<value>org.apache.spark.serializer.KryoSerializer</value>
   	</property>
   	<property>
   		<name>spark.executor.memeory</name>
   		<value>1g</value>
   	</property>
   	<property>
   		<name>spark.driver.memeory</name>
   		<value>1g</value>
   	</property>
   	<property>
   		<name>spark.executor.extraJavaOptions</name>
   		<value>-XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"</value>
   	</property> 
```

-- 注意 hive与spark版本要对应

```
 不匹配启动spark-yanr 会报
 Failed to execute spark task, with exception 'org.apache.hadoop.hive.ql.metadata.HiveException(Failed to create spark client.)' FAILED: Execution Error, 
 return code 1 from org.apache.hadoop.hive.ql.exec.spark.SparkTask
```

-- 启动spark  启动hive 验证Hive on Spark是否可用

```
   命令行输入 hive，进入hive CLI
   set hive.execution.engine=spark; (将执行引擎设为Spark，默认是mr，退出hive CLI后，回到默认设置。若想让引擎默认为Spark，需要在hive-site.xml里设置）
   create table test(ts BIGINT,line STRING); (创建表）
   select count(*) from test;
   若整个过程没有报错，并出现正确结果，则Hive on Spark配置成功。
```
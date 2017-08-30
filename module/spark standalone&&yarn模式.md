### spark standalone&&yarn模式

- standalone模式

```
1.slaves文件
  slave1
  slave2
  slave3

2.spark-env.sh
    export SCALA_HOME=/usr/local/scala
    export JAVA_HOME=/usr/java/default
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)
    export SPARK_MASTER_IP=192.168.137.100
    export SPARK_LOCAL_DIRS=/usr/local/spark
    export SPARK_WORKER_MEMORY=1g
    export SPARK_MASTER_PORT=7077

3.启动 ./sbin/start-all.sh

4.standlone模式之client模式 结果xshell可见
 ./bin/spark-submit --class org.apache.spark.examples.SparkPi  --master spark://master:7077  --executor-memory 1G --total-executor-cores 1 ./examples/jars/spark-examples_2.11-2.1.0.jar 100  

4.standlone模式之cluster模式
 ./bin/spark-submit --class org.apache.spark.examples.SparkPi  --master spark://master:7077 --deploy-mode cluster --supervise --executor-memory 1G --total-executor-cores 1 ./examples/jars/spark-examples_2.11-2.1.0.jar 100  

```
- yarn模式

```$xslt
1.spark-env.sh
    export SCALA_HOME=/usr/local/scala
    export JAVA_HOME=/usr/java/default
    export HADOOP_HOME=/usr/local/hadoop
    export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
    export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)
    export SPARK_MASTER_IP=192.168.137.100
    export SPARK_LOCAL_DIRS=/usr/local/spark
    export SPARK_WORKER_MEMORY=1g
    export SPARK_MASTER_PORT=7077

2. yarn-client模式：
   ./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn-client --executor-memory 1G --num-executors 1 ./examples/jars/spark-examples_2.11-2.1.0.jar 100

3. yarn-cluster模式：
   ./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn-cluster --executor-memory 1G --num-executors 1 ./examples/jars/spark-examples_2.11-2.1.0.jar 100
```


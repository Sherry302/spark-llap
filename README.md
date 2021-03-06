# spark-llap

A library to load data into Spark SQL DataFrames from Hive using LLAP. It also contains Catalog/Context classes to enable querying of Hive tables without having to first register them as temporary tables in Spark SQL.

## Building From Source
This library is built with [SBT](http://www.scala-sbt.org/0.13/docs/Command-Line-Reference.html), which is
automatically downloaded by the included shell script.
To build a JAR run 'build/sbt package' from the project root.


## Using spark-llap

### Prerequisites
- Spark 1.6
- HiveServer2 Interactive (LLAP)


### Configuration changes to use spark-llap:
Using Ambari, the following config changes are needed:

#### Custom hive-interactive-site (only needed for secure cluster):
  - hive.llap.task.principal=*hive principal*  (Can use same value as hive.llap.task.principal)
  -  hive.llap.task.keytab.file=*hive keytab file* (Can use same value as hive.llap.task.keytab.file)

#### Custom spark-defaults.conf:
  - spark.sql.hive.hiveserver2.url=*HiveServer2URL* (e.g. jdbc:hive2://hostname:10500;principal=hive/\_HOST@EXAMPLE.COM)
  - spark.hadoop.hive.llap.daemon.service.hosts=*value for hive.llap.daemon.service.hosts in Hive configuration*
  - spark.hadoop.hive.zookeeper.quorum=*value for hive.zookeeper.quorum in Hive configuration*

#### Custom spark-thrift-sparkconf:
  - spark.sql.hive.hiveserver2.url=*HiveServer2URL*;hive.server2.proxy.user=${user} (e.g. jdbc:hive2://hostname:10500;principal=hive/\_HOST@EXAMPLE.COM;hive.server2.proxy.user=${user})
  - spark.hadoop.hive.llap.daemon.service.hosts=*value for hive.llap.daemon.service.hosts in Hive configuration*
  - spark.hadoop.hive.zookeeper.quorum=*value for hive.zookeeper.quorum in Hive configuration*

####Advanced spark-env:
- spark\_thrift\_cmd\_opts= --jars /path/to/spark-llap-assembly.jar


### Using spark-llap with Spark ThriftServer:
If using Spark ThriftServer from HDP and the above configuration changes are made, the Spark ThriftServer should be using the LlapContext in place of HiveContext any SparkSQL queries should automatically be using spark-llap.


### Using spark-llap with spark-shell:
Include the spark-llap JAR when running spark-shell:

    spark-shell --jars /path/to/spark-llap-assembly.jar


The application will need to import/instantiate/use the LlapContext in place of the HiveContext when running SparkSQL queries:

    import org.apache.spark.sql.hive.llap.LlapContext
    // Instantiate LlapContext, and use in place of HiveContext
    var llapContext = new LlapContext(sc)
    llapContext.sql("select * from tab1").show


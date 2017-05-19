---
layout: post
title: A High Level Experiment with Hadoop (Cloudera)
date: 2017-05-18 23:00:00
category: "Hadoop"
---

When following the book led me to a place where I didn't know how to create a hadoop project (the eclipse comment in the last post didn't seem to create me anything in my project folder), I decided to give a pre-configured machine a try. Maybe experiencing with a high level tour can help me better understand the hadoop usage, then I can switch back to basic configurations.

I had the following items installed/downloaded:
*VMWare Fusion
*Virtual machine from [here](https://downloads.cloudera.com/demo_vm/virtualbox/cloudera-quickstart-vm-5.4.2-0-virtualbox.zip){:target="_blank"}

So I started the VM (Linux I think) and it already has hadoop as well as other components configured for me, including:
*Hadoop 2.6.0 (cdh5.4.2)
*Java 1.7.0_67 64 bit
*Sqoop (SQL to Hadoop tool, transfer data from RDBMS to hadoop data type)
*Impala (Cloudera's hadoop-like table management/querying tool)
*Apache Spark
*MySQL

First, I tried to export 6 tables from MySQL into HDFS by running the following commands in terminal:

```bash
sqoop import-all-tables \
    -m 1 \
    --connect jdbc:mysql://quickstart:3306/retail_db \
    --username=retail_dba \
    --password=cloudera \
    --compression-codec=snappy \
    --as-avrodatafile \
    --warehouse-dir=/user/hive/warehouse
```

What the above command does:
1. Connect to MySQL database (located in //quickstart:3306/retail_db)
2. Export
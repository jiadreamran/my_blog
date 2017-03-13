---
layout: post
title: Hadoop Configuaration Pseudodistributed Mode
date: 2017-03-12 23:01:25
category: "Hadoop"
---

So I begin to config my first Hadoop application.

Apparently there are three modes to run Hadoop:
- Standalone mode
- Pseudodistributed mode
- Fully distributed mode

The first mode merely runs Hadoop (without "daemons", which at this time I think will be the processes that represents namenodes, datanodes, resource managers, node managers or history servers) in a single JVM. This will be ideal for developers (maybe I can get to this later when I need to practice developing something).

Pseudodistributed mode is the one I am aiming for today. It simulates a cluster on a small scale.

Fully distributed mode will be used in production, and the configuration of it will be massive, I think.

As described in the title, I will be using pseudodistributed mode (since now I am practicing on one machine).

The config files for such mode are located under ```bash etc/hadoop``` folder in your hadoop installation directory. However, in order to distinguish the default (installed) configs with my customized config, I decided to copy all the config files within this folder to a new place and set an environment variable, <b>"HADOOP_CONF_DIR"</b> for it.

So here is what I did:
```bash
vim ~/.bash_profile
```

This will open the bash_profile I created in my [last article](hadoop-installation.html){:target="_blank"}. There I just added the following into the "export" list:

```bash
export HADOOP_CONF_DIR=/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3-jiadreamran-config
```

In the future when you create other instances of Hadoop, you can replace it with your own folder.

After copying all the files from etc/hadoop folder to my own config folder, I modifed the contents of the four following files:

| Filename | Description |
|---------|---------|
| core-site.xml | Common configs of a hadoop site |
| hdfs-site.xml | Config file for Hadoop Distributed Filesystem (HDFS) |
| mapred-site.xml | Config file for MapReduce |
| yarn-default.xml | Config file for YARN, <b>I don't know what YARN is at this moment.</b> |

The actual pseudodistributed config is done by modifying the file contents as such:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- core-site.xml -->
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost</value>
    </property>
</configuration>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--hdfs-site.xml-->
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
</configuration>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--mapred-site.xml-->
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
<b>For mapred-site.xml, there is no such file in the original config files. There is a file called mapred-site.xml.tempalte file, I am guessing this may be a master template? So for the time being, I created an emtpy mapred-site.xml file and populated with the content above.</b>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--yarn-site.xml-->
<configuration>
<!-- Site specific YARN configuration properties -->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>localhost</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```

Now it's time to allow the application to ssh into my Mac. Before doing anything, I enabled the "Remote Login" (under  > System Preference > Sharing).

Not sure if you have ssh installed already on your Mac, if not:
```bash
sudo apt-get install ssh
```

To enable passwordless login, generate a new SSH key with an empty passpharse:
```bash
ssh-keygen -t rsa -p '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
```

Test that you can connect with:
```bash
ssh localhost
```

You should be able to login without having to input your password.

Before starting Hadoop HDFS, you should format the HDFS filesystem by running:
```bash
hdfs namenode -format
```

Now we can start HDFS, YARN and MapReduce daemons:
```bash
start-dfs.sh
start-yarn.sh
mr-jobhistory-daemon.sh start historyserver
```

Because we have already configurated the xml files (and created an environment variable for external configs), the HDFS should start properly.

To test whether the daemons started successfully by looking at the logfiles, go to:
- [http://localhost:50070](http://localhost:50070){:target="_blank"} for the namenode
- [http://localhost:8088](http://localhost:8088){:target="_blank"} for the resource manager
- [http://localhost:19888](http://localhost:50070){:target="_blank"} for the history server

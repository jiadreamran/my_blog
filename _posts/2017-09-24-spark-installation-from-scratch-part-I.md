---
layout: post
title: Spark Installation and Configuration 1 Hadoop Installation
date: 2017-09-24 22:00:00
category: "Hadoop"
---

It has been a while since last time I updated something. During this time I have learned more about hadoop and spark, which leads to me the decision to focus on spark.

This is an article to guide you through spark installation/configuration via virtual machines. Again, I am not a typicall Linux/Mac user so everything is also new to me.

In this chapter, I will install Hadoop from scratch. In the next chapter, on top of what I got, I will install Spark.

What I have before installation:

* MacOs Sierra 10.12
* VMWare Fusion 8.5 (purchased a license)

To install Spark, there are a lot of things to configure:

* Linux virtual machines (Redhat)
* Java installation
* Hadoop installation
* Spark installation


Let's install and configure them one at a time.

## Linux Virtual Machine (Redhat)
I have VMWare Fusion installed on my Mac so the only thing I need is to have a virtual machine image to mount (I tried to build a customized virtual machine but failed). So I downloaded the Redhat Enterprise Linux 5.4.0 version (DVD iso) downloaded from [here](https://access.redhat.com/downloads/content/69/ver=/rhel---5/5.4/x86_64/product-software/){:target="_blank"}.

### Create Virtual Machines 

1. Open VMWare, go to virtual machine library.
2. File > New, then double click on "Install from disc or image".
3. Browse to the iso file you just downloaded, then drag it onto the popup panel.
4. Choose installation language and so on. Choose "Skip" in CD page.
    4.1 Software Selection: Server with GUI: select FTP Server, KDE, Development Tools.
5. Configure your partition, make sure you choose "I will configure partitioning". By default your VM will have 20 GB of disk space. To create a new mount point, click "+" button on the bottom left corner.
    5.1 Create a root mount point by selecting "\" as the mount point and assign it 15 GB. File system will be "xfs".
    5.2 Create a boot mount point by selecting "\boot" as the mount point and assign it 300 MB (xfs).
    5.3 Create a swap mount point by selecting "swap" as the mount point and assign it 2048 MB (twice of the machine's physical memory). The file system type will be defaulted to "swap".
6. After clicking "Done" button, "Begin Installation" should be enabled. Click it to start installation.
7. During installation, you can set a password for root user.
8. After installation, shut down the vm.

Follow the above instruction, I created a virtual machine, "Master". Both virtual machine bundle name and the name within VMWare are "Master".

Then we will copy this "Master" machine and named it "Slave01". To copy a vm in VMWare Fusion, follow [this tutorial](https://developers.redhat.com/products/rhel/download/){:target="_blank"}. When opening up this newly copied vm (by simply double click on its bundle file), you have to choose "I Copied It" from the popup window.

### Configure VMWare Fusion IP

This is a relatively easy but important task. You need to configure the IP address for VMWare so it can be used in conjunction with the virtual machines' configured IP address.

In this tutorial, we will dim the overall IP address as "192.168.3.6".

The Master vm will have an IP address of "192.168.3.100".

The Slave01 vm will have an IP address of "192.168.3.101".

Turn off all vm and VMWare Fusion.

To change the IP address for VMWare, use the following command:

```bash
sudo vim /Library/Preferences/VMWare Fusion/networking
```

In vim, change the value of VNET_8_HOSTONLY_SUBNET to "192.168.3.6".

Restart VMWare Fusion.

[This blog (in Chinese)](http://blog.csdn.net/seven_zhao/article/details/43406289){:target="_blank"} can be used for reference regarding these settings.

### Configure Machine IP and Network

1. Start both machines you created above.
2. To login, just use "root" username.
3. In "Master" vm, go to System > Administration > Network.
4. Double-click on the only (if more than one entry, delete one) item under "Devices" tab.
5. Check "Statically set IP addresses".
6. Change the "Address" value to "192.168.3.100". Change the "Subnet mask" value to "255.255.255.0".
7. Click OK (bottom right).
8. Click "Activate".

Repeat step 3 to 8 for "Slave01" vm, this time set Address to "192.168.3.101".

When all the steps are done correctly, you should be able to ping your two vms using the IP address you configured.

### Configure Machine Names

1. SSH into both vms. I used [Shuttle](https://github.com/fitztrev/shuttle){:target="_blank"} since I cannot find xshell on Mac.
    1.1 To SSH into a vm, simply open Shuttle (or terminal), then type in "ssh username@host".
    1.2 For example, to login to master using "root" user: 
    
    ```bash
    ssh root@192.168.3.100
    ```
    
2. Modify hostname:
    2.1 After ssh into the machine, open network config file
    
    ```bash
    vim /etc/sysconfig/network
    ```
    
    2.2 Modify "HOSTNAME" attribute, set it to the correct name.
    2.3 I set Master machine (192.168.3.100) as "master". Slave machine (192.168.3.101) as "slave01".
3. Disable firewall. This is disabled during vm installation, but you can always disable it by:

    ```bash
    vim /etc/sysconfig/selinux
    ```
    
4. Config host name so master and slave01 can communicate with each other.
    4.1 On both vms, modify "hosts" file:
    
    ```bash
    vim /etc/hosts
    ```
    
    4.2 Appending the following the the end of the file:
    
    ```bash
    192.168.3.100 master
    192.168.3.101 slave01
    ```

5. Reboot both vms.

    ```bash
    reboot
    ```
    
If everything works alright, you should be able to ssh into master and then two changes will happen:

1. Instead of "root@192.168.3.100" in the head of the command line, it will have "root@master".
2. If you ping slave01, you should be able to do that.

Same applies to slave01.



## Hadoop Cluster Creation

### Install Java

In order to install Java, we need to upload the Java installation package from local computer (my mac) to both virtual machines. To do that, we need to enable ftp server first.

#### Enable FTP Server

To enable ftp server, ssh into both master and slave01 machines, and then try:

```bash
/etc/init.d/vsftpd restart
```

The shutting down of the ftp server may fail once, but just try serval times until both shutting down and restart have "OK" status.

We need to create a folder for uploading files from local to vm (both vms):

```bash
mkdir installer
```

#### Upload Installation File to VMs

To upload any file from local machine to the configured VMs, I used [FileZilla for OS X](https://filezilla-project.org/download.php?platform=osx){:target="_blank"}.

You need to config the connections to the two vms via Site Manager, the port number is 22 and the connection type is recommended as SFTP:

![FTP Connection Config](assets/FileZilla_Conig.jpg)

Once config is done, connect to both vms within FileZilla.

In my previous article on hadoop installation, I choose [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u80-oth-JPR){:target="_blank"} as the Java version. This time I will choose the same thing, however using Linux 64-bit version.

Now need to upload the JDK to the two vms via FileZilla. Just use the FileZilla GUI to do it (you need to upload the file under the newly created "/root/installer/" folder).


#### Install JDK

After finished uploading, cd into /root/installer, then:

```bash
rpm -ivh jdk-7u80-linux-x64.rpm
```

You should be able to verify your JDK version:

```bash
javac -version
```


### Create Hadoop Users

Create two new tabs in terminal, ssh into master and slave01 respectively. Create a username "hadoop", and then switch to

```bash
useradd hadoop
passwd hadoop
# Input your password here and retype it according to prompt
su - hadoop
```

Create "installer" directory for user "hadoop"

```bash
mkdir installer
```

### Config SSH Equivalent

Config ssh equivalent for both vms. The purpose of setting ssh equivalent for these two machines is to let you ssh into the two vms without having to type the password.

```bash
ssh-keygen -t rsa
# Press "Enter" for 3 times.
```

Config id_rsa.pub for "master":

```bash
cd .ssh/
authorized_keys
# You will see authorized_keys file generated in this directory if you want to
ls
scp authorized_keys slave01:~/.ssh/
# Enter your password for hadoop user on "slave01"
```

The "authorized_keys" file will be added to slave01 under hadoop/.ssh/ folder.

Go to "slave01" ssh terminal, write the copied id_rsa.pub file into authoized_keys file. Then send it back to "master", so the "authorized_keys" file will also have the "slave01" config content.

```bash
cat id_rsa.pub >> authorized_keys
# View the modified authorized_keys file if you want to
cat authorized_keys
scp authorized_keys master:~/.ssh/
# Enter your password for hadoop user on "master"
```

Modify the privilege of the authorized_keys file on both vms:

```bash
chmod 600 authorized_keys
```

This way you can directly ssh into "master" and "slave01" without having to type passowrd everythime. However, since you have only configured ssh equivalent for "hadoop" user, you can only ssh into both machines when using "hadoop". You "root" user won't be able to have that function.


### Hadoop Installation

Download hadoop installation tar file from [here](http://mirror.stjschools.org/public/apache/hadoop/common/hadoop-2.6.5/){:target="_blank"}, I used hadoop 2.6.5 just to be safe.

Use FileZilla to connect to both vms using "hadoop" user (not root), and upload the hadoop installation file to the "installer" folder.

In "master", unzip the tar file and rename it "hadoop2"

```bash
cd installer
tar -zxvf hadoop-2.6.5.tar.gz
mv hadoop-2.6.5 hadoop2
```

### Config Hadoop Environment Variables on VMs

Similar to my first article on hadoop installation on Mac, here we need to config some hadoop environment variables:

```bash
# Go back to hadoop user root
cd
vim .bashrc
```

In vim, under "User specific aliases and functions", type in this:

```bash
export JAVA_HOME=/usr/java/jdk1.7.0_80
export HADOOP_HOME=/home/hadoop/installer/hadoop2
export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_HOME}/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
# Append Java lib and Hadoop lib to CLASSPATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$HADOOP_HOME/lib
# Append Java bin and Hadoop bin/sbin to PATH
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

JAVA_HOME is where your jdk is installed and HADOOP_HOME is where your hadoop is just installed.

After configuring .bashrc file, make it effective immediately (without restarting the vm):

```bash
. .bashrc
```

Copy the configured .bashrc file to "slave01" root directory using scp:

```bash
scp .bashrc slave01:~
```

Also enable the newly copied .bashrc file on slave01.

### Config Hadoop Application

#### Config Hadoop Environment

```bash
cd $HADOOP_HOME
cd etc/hadoop
vim hadoop-env.sh
```

In vim, delete "export JAVA_HOME" line, replace it with:

```bash
export JAVA_HOME=/usr/java/jdk1.7.0_80
```

There is a "for" loop, after which you will find "#export HADOOP_HEAPSIZE=". Make it like this:

```bash
export HADOOP_HEAPSIZE=100
```

#### Config YARN Environment

Leave vim and now configure yarn-evn.sh:

```bash
vim yarn-evn.sh
```

Set JAVA_HOME attribute in the first uncommented row:

```bash
export JAVA_HOME=/usr/java/jdk1.7.0_80
```

Go down to find one line:

```bash
JAVA_HEAP_MAX=-Xmx1000m
```

Change it to this:

```bash
JAVA_HEAP_MAX=-Xmx300m
```

There is also a line under this line saying:

```bash
#YARN_HEAPSIZE=1000
```

Uncomment this line and make "1000" to 100.

```bash
YARN_HEAPSIZE=100
```

Save and quit vim.

#### Config MapReduce Environment

```bash
vim mapred-env.sh
```

Uncomment the JAVA_HOME line and make the same Java home change. Also in the next line make "1000" to "100".

```bash
export JAVA_HOME=/usr/java/jdk1.7.0_80
export HADOOP_JOB_HISTORYSERVER_HEAPSIZE=100
```

Save and quit vim.

#### Config Slaves

```bash
vim slaves
```

There will be only one line in the "slaves" file: localhost. Delete it and replace it with "slave01". That is because we configured the only slave machine name as "slave01" (step 2.3 in previous section). After configuration the "slaves" file should look like this:

```bash
slave01
```

#### Config core-site.xml

```bash
vim core-site.xml
```

Replace the <configuration></configuration> part with the following. This is to configure the default file system and the temp dir location.

```bash
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/home/hadoop/tmp</value>
        </property>
</configuration>
```

Save and exit vim. In the config file we specified a temp folder, "tmp", directly under the hadoop user folder. So we need to create the physical folder in the vms as well. The easiest way to do this is:

1. SSH into master using hadoop as the user.
2. Make sure you are in "hadoop" home folder.
3.

```bash
mkdir tmp
```

Also do the same thing for "slave01".

#### Config hdfs-site.xml

```bash
vim hdfs-site.xml
```

Replace the <configuration></configuration> with the following:

```bash
<configuration>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>master:50090</value>
        </property>
        
        <property>
                <name>dfs.namenode.name.dir</name>
                <value>/home/hadoop/data/dfs/name</value>
        </property>
        
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/home/hadoop/data/dfs/data</value>
        </property>

        <property>
                <name>dfs.replication</name>
                <value>1</value>
        </property>

        <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
        </property>
</configuration>
```

The above config specifies the following:
1. Secondary name node address
2. Name node directory
3. Data node directory
4. Number of replication of data (just 1 replication)
5. Enable WebHDFS

You also need to create the above specified folders (name node dir and data node dir) for both "master" and "slave01", under hadoop user folder.

```bash
mkdir -p data/dfs/data
mkdir -p data/dfs/name
```

#### Config mapred-site.xml

In "master", copy mapred-site.xml.template to the same directory and name it mapred-site.xml:

```bash
cp mapred-site.xml.template mapred-site.xml
vim mapred-site.xml
```

Replace <configuration></configuration> with the following:

```bash
<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>

        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>master:10020</value>
        </property>

        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>master:19888</value>
        </property>

        <property>
                <name>mapreduce.map.memory.mb</name>
                <value>300</value>
        </property>

        <property>
                <name>mapreduce.reduce.memory.mb</name>
                <value>300</value>
        </property>

        <property>
                <name>yarn.app.mapreduce.am.resource.mb</name>
                <value>100</value>
        </property>
</configuration>
```

In above file we configured the following:
1. Set the resource manager for MapReduce as YARN
2. Set the job history address
3. Set the job history web app address
4. Set the memory allocated for a map task to be 300 MB
5. Set the memory allocated for a reduce task to be 300 MB
6. Set the memory for YARN over MapReduce to be 100 MB

#### Config yarn-site.xml

```bash
vim yarn-site.xml
```

```bash
<configuration>
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

        <property>
                <description>The host name of the RM.</description>
                <name>yarn.resourcemanager.hostname</name>
                <value>master</value>
        </property>

        <property>
                <description>The address of the applications manager interface in the RM.</description>
                <name>yarn.resourcemanager.address</name>
                <value>${yarn.resourcemanager.hostname}:8032</value>
        </property>

        <property>
                <description>The address of the scheduler interface.</description>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>${yarn.resourcemanager.hostname}:8030</value>
        </property>

        <property>
                <description>The http address of the RM web application.</description>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>${yarn.resourcemanager.hostname}:8088</value>
        </property>

        <property>
                <description>The https address of the RM web application.</description>
                <name>yarn.resourcemanager.webapp.https.address</name>
                <value>${yarn.resourcemanager.hostname}:8090</value>
        </property>
        
        <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>${yarn.resourcemanager.hostname}:8031</value>
        </property>
        
        <property>
                <description>The address of the RM admin interface.</description>
                <name>yarn.resourcemanager.admin.address</name>
                <value>${yarn.resourcemanager.hostname}:8033</value>
        </property>
        
        <property>
                <description>The minimum allocation for every container request at the RM, in MBs. Memory requests lower than this won't take effect, and the specified value will get allocated at minimum.</description>
                <name>yarn.scheduler.minimum-allocation-mb</name>
                <value>300</value>
        </property>
        
        <property>
                <description>The maximum allocation for every container request at the RM, in MBs. Memory requests higher than this won't take effect, and will get capped to this value.</description>
                <name>yarn.scheduler.maximum-allocation-mb</name>
                <value>1024</value>
        </property>
        
        <property>
                <description>Ratio between virtual memory to physical memory when setting memory limits for containers. Container allocations are expressed in terms of physical memory, and virtual memory usage is allowed to exceed this allocation by this ratio.</description>
                <name>yarn.nodemanager.vmem-pmem-ratio</name>
                <value>6.1</value>
        </property>
</configuration>
```

You are done with hadoop "master" configuration!

#### Config Hadoop on "slave01"

Because you haven't done anything (besides creating some empty folders) on "slave01" yet, you need to directly copy your configured "hadoop2" folder from "master" to "slave01".

```bash
# This is "cd [space]" to go back to "master" hadoop user root
cd 
cd installer
scp -r hadoop2 slave01:~/installer/
```

While the files are being copied, we can make the environment config effective by going to "master" and run:

```bash
. .bashrc
```

After file copy, run the same command for "slave01".

#### Format Hadoop

Go to ssh "master", run the following format command to format the namenode.

```bash
hadoop namenode -format
```

Now start HDFS:

```bash
start-dfs.sh
start-yarn.sh
# To see your processes:
jps
```

You should have 4 items in there: Jps, Namenode, SecondaryNamenode, ResourceManager.
In "slave01", run the same command and do "jps", you should have DataNode, NodeManager and Jps in there.

If you don't have secondary namenode, check your core-site.xml and delete (rm -rf *) the content of data/dfs/data and data/dfs/name (corresponding to datanode and namenode) folder, and stop and reformat hadoop.

### Test Hadoop

#### Create Test File

Create a random text file, in this case, we will name it 1.txt. The content can be:

```bash
hadoop hadoop spark storm
java java c C# c
java spark
```

Put it into hdfs /data/wordcount folder

```bash
hdfs dfs -mkdir /data
hdfs dfs -mkdir /data/wordcount
hdfs dfs -put 1.txt /data/wordcount
```

Run hadoop command to create folders for test:

```bash
cd $HADOOP_HOME
cd share/hadoop/mapreduce/
# Invoke "wordcount" function in the example jar file.
# "/data/wordcount" folder is the hdfs input folder (every file will be read)
# "/data/wordcount/output" is the output folder: it cannot exist before running this command
hadoop jar hadoop-mapreduce-examples-2.6.5.jar wordcount /data/wordcount /data/wordcount/output

# To view result after running
hdfs dfs -cat /data/wordcount/output/*

# To view result file names
hdfs dfs -ls /data/wordcount/output
```

Congrats! After so long you have finally installed and configured your hadoop application on a master and slave machine, and get your example code running. If you can successfully get the mapreduce example to run it means everything is correct for you.

Now the only thing left is to create snapshots of both your machines so you can rollback to the initiation state if you encountered errors in the future (hopefully that won't happen).
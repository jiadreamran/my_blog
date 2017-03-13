---
layout: post
title: Hadoop Installation on Mac OSX
date: 2017-03-12 15:00:00
category: "Hadoop"
---

Well as a beginner of UNIX programming, I decided to build this web site just for me to review the code and installation that I have been learning.

In this process, I decided to use Hadoop as my study material given that it will be the future trend to Linux and Java programming. If I can somehow master this technology, not only I will not fall behind the trend, I will also pick up some great things to do in my spare time. So let me begin this (hopefully interesting) journey.

First of all, Hadoop runs on UNIX/Linux or similar OS - I think. Therefore as a newbie to any UNIX/Linux programming, it is a pain for me to get used to its command system. I have been using cygwin for a short time in my work but I tend to avoid it whenever I can - a bad behavior inherited from Windows-spoiled developers. So I started to ask: how can I install a Linux virtual machine? This was quickly answered by some articles online: Mac OSX is built based on UNIX. This actually answers why in bay area so many people prefer to use Mac as their computers in Starbucks. So Mac OSX actually has its own terminal, similar to cygwin command prompt: all I need to do is to open Siri and say "open terminal", as I don't actually know where it is located in my computer :)

After all this being said, I googled and followed this article to install my first Hadoop application:
[https://amodernstory.com/2014/09/23/installing-hadoop-on-mac-osx-yosemite/](https://amodernstory.com/2014/09/23/installing-hadoop-on-mac-osx-yosemite/){:target="_blank"}

To some extent, this article may help people that are familiar with Hadoop or at least it's their second time configuring the system. To a complete idot like me, it brought this problem:

- First of all, when installing HomeBrew, I was prompted to install Java JDK (of course!), which leads to a future problem that led me to uninstall everything: the Java version installed by default (1.8) is actually not supported as documented [here](https://wiki.apache.org/hadoop/HadoopJavaVersions){:target="_blank"}. So that is why everytime I tried to run a Hadoop start, it barked for "Could not create the Java Virtual Machine". Lesson learned: install JDK 1.7 instead of 1.8.

So I followed the offical guide: Hadoop the Definitive Guide by Tom White. Here are the steps:
- Do not use root user (if you don't know what a root user is, it is by default disabled by Mac, so if you are new to Mac, you probably don't have to worry about it).
- Install JDK 1.7. I am using MacOS Sierra 10.12.3, so the compatible JDK version I found is [here](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u80-oth-JPR){:target="_blank"}. I directly used the dmg file.
- The installed JDK is located in /Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
- Then I chose Hadoop 2.7.3 in [here](http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/SingleCluster.html#Download){:target="_blank"}.
- To install Hadoop, simply move the downloaded gzipped tar file to your desired location, and then run:

```bash
tar xzf hadoop-x.y.z.tar.gz
```

- After Hadoop has been successfully unzipped, I did two extra things: created environment variable on JAVA_HOME and created Hadoop command shortcuts. This is all done via the creation of bash_profile file. (A bash_profile file, according to my understanding, is like the environment variable in Windows, there you store locations of commonly used directories so whenever you enter a command, it will go through these directories to find it.)
- By default, I didn't find the bash_profile file anywhere, so after intensive researching, I used the following command to create it:

```bash
vim ~/.bash_profile
```

For people like me who have headaches with vim, here are some common usage of it:
- In a newly opened vim window, press "a" to begin edit.
- Press "Esc" to quit editing mode.
- In non-editing mode, hold "Shift" and press "Z" and "Z" to save your change and exit.
- In non-editing mode, hold "Shift" and press "Z" and "Q" to quit without saving.
- After creation of bash_profile file, here is what I put in there:

```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
export HADOOP_HOME=YOUR_EXTRACTED_HADOOP_FOLDER
# Mine is /Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

alias hstart="YOUR_EXTRACTED_HADOOP_FOLDER/sbin/start-dfs.sh;YOUR_EXTRACTED_HADOOP_FOLDER/hadoop-2.7.3/sbin/start-yarn.sh"

alias hstop="YOUR_EXTRACTED_HADOOP_FOLDER/sbin/stop-yarn.sh;YOUR_EXTRACTED_HADOOP_FOLDER/sbin/stop-dfs.sh"
```

So Hadoop commands will be registered to your terminal (you need to restart the terminal for it to take effort). If everything goes well, when you run the following command:

```bash
hadoop version
```

You should be able to see some output like:
```bash
Hadoop 2.7.3
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r baa91f7c6bc9cb92be5982de4719c1c8af91ccff
Compiled by root on 2016-08-18T01:41Z
Compiled with protoc 2.5.0
From source with checksum 2e4ce5f957ea4db193bce3734ff29ff4
This command was run using YOUR_EXTRACTED_HADOOP_FOLDER/share/hadoop/common/hadoop-common-2.7.3.jar
```

I stopped at Hadoop installation and haven't done any configurations (to core-site.xml, hdfs-site.xml, yarn-site.xml etc).

The configuration part will be in my next blog.

That's by far all I have gotten. Next time I will try to create a single cluster and see what I can run there.

Cheers.
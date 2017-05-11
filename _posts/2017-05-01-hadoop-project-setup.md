---
layout: post
title: Hadoop Installation on Mac OSX
date: 2017-05-01 23:00:00
category: "Hadoop"
---

OK. I had been officially struggling for 3 days to setup the Eclipse environment just to make it work with the JDK I installed for hadoop, and I learned the lesson in a hard way.

Previously I stated in [this article](hadoop-installation.html){:target="_blank"} that JDK 7 is needed, so I have to download the correct Eclipse version.

After struggling for so long time, including Eclipse cannot be opened error, extraction error, JVM version error, etc., I finally figured out this is the version that is working for my Mac:
Elicpse Mars 2. To download it, click [here](https://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/mars/2/eclipse-java-mars-2-macosx-cocoa-x86_64.tar.gz){:target="_blank"}.

You can directly double click on the tar.gz file so the Eclipse application package will be extracted. I also modified the eclipse.ini file to specify the Java path (ini file located inside Eclipse package, to reveal it, right click on Eclipse > Show Package Conents > Contents > Eclipse > eclipse.ini). I append the following content before "-vmargs":

```bash
-vm
/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/home/bin/java
```

In order to create my first MapReduce project (don't know what it would be like at this moment), I need to have Maven installed on my Mac (yes, I am that far behind). To install Maven, I went to [here](http://maven.apache.org/download.cgi){:target="_blank"}. Then I downloaded the 3.5.0 version of the tar.gz file, and extract it to one of my Desktop folder ("/Users/jiadreamran/Desktop/Coding/maven/apache-maven-3.5.0").

Just like any other tar.gz file, I need to register the maven command to PATH environment variable. So I modified my ~/.bash_profile to be like this:
```bash
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_80.jdk/Contents/Home
export HADOOP_HOME=/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3
export MAVEN_HOME=/Users/jiadreamran/Desktop/Coding/maven/apache-maven-3.5.0
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$MAVEN_HOME/bin
export HADOOP_CONF_DIR=/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3-jiadreamran-config

alias hstart="/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3/sbin/start-dfs.sh;/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3/sbin/start-yarn.sh"

alias hstop="/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3/sbin/stop-yarn.sh;/Users/jiadreamran/Desktop/hadoop/hadoop-2.7.3/sbin/stop-dfs.sh"
```

Note that I added "MAVEN_HOME" and append it (plus the "/bin" folder) to the end of the "PATH" variable.

If you successfully completed the above steps, the following command should give you the maven version:

```bash
mvn -v
```

Then I started to create the [pom.xml](https://github.com/jiadreamran/my_blog/blob/gh-pages/_posts/assets/pom.xml){:target="_blank"} file. This pom file tells maven how to build the project and what would be the dependencies.

Then I simply point my terminal to the folder that contains the pom file, and run the following command to build the project using eclipse:

```bash
mvn eclipse:eclipse -DdownloadSources=true -DdownloadJavadocs=true
```

After run the above command for 90 seconds, my project was successfully built. I didn't notice any change in the folder containing the pom file but I still think something is happening. More on that later.
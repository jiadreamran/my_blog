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
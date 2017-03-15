---
layout: post
title: Hadoop Configuaration Pseudodistributed Mode
date: 2017-03-12 23:01:25
category: "Hadoop"
---

So far I haven't really figured out where hadoop copies a file from local to its HDFS.
However here is what I understand: when a local file, for example, "myBlog.md" is copied to HDFS via the following command:

```bash
hadoop fs -copyFromLocal /Users/jiadreamran/Blog/my_blog/_posts/myBlog.md copiedBlog.md
```

You actually won't be able to find it anywhere by the name "copiedBlog.md" on the computer (?) because it will be represented in a HDFS block format. Also when you tried the same HDFS copy command again you will receive an error message telling you the file already exists in the HDFS (so we cannot have files named the same in HDFS??).

However when you copy the file from HDFS to your local using:

```bash
hadoop fs -copyToLocal copiedBlog.md /Users/jiadreamran/Desktop/copyToLocal.md
```
The "copyToLocal.md" file will be on my desktop having the same content as "myBlog.md".

I need to further investigate on how to specify HDFS default local drive and take a look at the copied format of the file. I searched via the hadoop log it looks like I can download it from /users/jiadreamran somewhere.

TODO: need to investigate on how to list files within my HDFS and how to delete them.
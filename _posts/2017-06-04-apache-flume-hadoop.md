---
layout: post
title: Apache Flume
date: 2017-06-04 20:00:00
category: "Hadoop"
---

When you are dealing with structured data, such as RDBMS or tables, you can directly use SQL to query them. However in big data world, you can never expect data to have a uniform structure, or even clean structure. That's why hadoop's "schema on read" is such a powerful capability.

In this chapter, I will learn a new tool from Apache to ingest stream data: [Flume](https://hortonworks.com/apache/flume/){:target="_blank"}.

Flume is one of the tools that accepts stream data (such as application logs (Google search logs, etc.), machine or sensor data (Internet of Things), geographic locations (GeoRSS), etc.) and (garenteed) transfer them into HDFS for future analysis (using Hive for HQL, for example).


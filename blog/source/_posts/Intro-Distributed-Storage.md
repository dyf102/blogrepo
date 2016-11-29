layout: blog
title: Intro. Distributed Storage
date: 2016-11-29 13:53:00
tags:
---
Ceph is a Distributed storage system

### Overview
MDS  meta data server
ODS  Object Data server
MON   Monitor
- Decouple Data and metadata

- Dynamic:
Metadata is split among cluster of servers
• Distribution of metadata changes with the
number of requests to even load among
metadata servers
• Metadata servers also can quickly recover
from failures by taking over neighbors’
data
• Improves performance by leveling
metadata load

- Reliable
• Data storage servers act on events by
themselves
• Initiates replication and
• Improves performance by offloading
decision making to the many data servers
• Improves reliability by removing central
control of the cluster (single point of
failure)

Hive

### Overview

HQL, a variant of SQL
Translate queries into mapreduce job

component:
• Shell: allows interactive queries
• Driver: session handles, fetch, execute
• Compiler: parse, plan, optimize
• Execution engine: DAG of stages (MR,
HDFS, metadata)
• Metastore: schema, location in HDFS, et


Swift

###Overview
An object Storage

Use Case:
• store unstructured Object
• Images/ movies/Audio/large queue of messages

Example
1.  Azure Blob Storage
2.  Ambry - Linkedin
3.  Facebook Warm
4.  Amazon s3
5.  Apache openstack Block service(SWIF)

Goals:
1.  RESTful Api
2.  High availability


Amazon AWS EBS

###Overview
highly available and Reliable
required quickly accessible and long-term persistence
encryption
up tp 16 table

Amazon AWS Glacier

### Overview

archive you data with very low cost
Single file archive up to 40 table

Amazon EFS

Elastic File system

### Overview

SSD backend
highly available and highly durable
Grow or shrink as needed

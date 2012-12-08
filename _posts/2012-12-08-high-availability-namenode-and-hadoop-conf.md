---
layout: post
title: "High Availability NameNode and hadoop conf"
category: 
tags: []
---
{% include JB/setup %}

I'm working on adding support for High Availability Namenode support
within [hadoop-conf](https://github.com/ekoontz/hadoop-conf).

# hadoop-conf

First, some background on
[hadoop-conf](https://github.com/ekoontz/hadoop-conf/blob/master/README). It's
my Hadoop-on-a-laptop deployment setup tool, simply a Makefile with some
shell scripts and XSL templates. It just developed slowly over the
past year to ease development with Hadoop.

Its advantages are:

- Generates Hadoop configurations in a developer-friendly
 (i.e. hackable) way using a [Makefile](http://gnu.org/software/make)
- Configures difficult-to-configure system services (DNS, Kerberos, ntpd, ntpclient)
- Modifies core system properties on a Linux VM,
  avoiding any need for modifications to your main development OS (*but see Caveat below)
- Works disconnected from the Internet (if you are working while you commute on BART or Caltrain and have spotty or no Internet connection you'll appreciate this)
- Works with security (Kerberos)
- Facilitates the following development cycle:

1. Configure and install hadoop (`make install`)
2. Start hadoop (`make start`)
3. Observe bug or opportunity for enhancement
4. Modify some hadoop code to fix the bug or add the enhancement
5. Rebuild hadoop (`make build`)
6. Shutdown hadoop (`make stop`)
7. goto 2 (or 1 if reconfiguration is desired)

Caveats:

- It does require that your host system run ntpd. But if you are
running Mac OS X, it's already running by default. So no modifications
are needed, if your dev machine is a Mac. You may need to configure or
start it on other OSes.

- step 5 (make build) is not yet supported by
hadoop-conf. It's simple to describe: simply go to the top-level
hadoop directory and do:

      	   mvn -Pdist package

or, opting for certainty of development speed):

      	   mvn -Pdist clean package



# High Availability Namenode

I am working on adding support for: 

- [HDFS-3077 Quorum-based protocol for reading and writing edit logs](https://issues.apache.org/jira/browse/HDFS-3077)
- [HDFS 3042 Automatic failover support for NN HA](https://issues.apache.org/jira/browse/HDFS-3042)
- [HDFS-3092 Enable journal protocol based editlog streaming for standby namenode](https://issues.apache.org/jira/browse/HDFS-3042).

# Historical Background

The umbrella JIRA is: [HDFS-3278 Umbrella Jira for HDFS-HA Phase 2](https://issues.apache.org/jira/browse/HDFS-3278).

The predecessor of this umbrella JIRA is: [HDFS-1623 High Availability Framework
for HDFS NN](https://issues.apache.org/jira/browse/HDFS-1623)

Note that HDFS-3278 mentions that there are two phases to HA NN. The
first phase was covered by the predecessor (HDFS-1623). In the
description of HDFS-3278 is written:

> Branch HDFS-1623 was merged into trunk for tactical reasons even
> though the work for HA was not complete, Branch HDFS-1623 contained
> mechanisms for keeping a standby Hot (ie read from shared journal),
> dual block reports, fencing of DNs, Zookeeper library for leader
> election etc.



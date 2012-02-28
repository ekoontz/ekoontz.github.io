---
layout: post
category : hadoop
tags : [hadoop, outofthebox]
---
{% include JB/setup %}

This is a short guide to getting started with building and running the latest Hadoop, from source code.

## Prerequisites

* [Git](http://git-scm.org)
* [Apache Maven 3](http://maven.apache.org/download.html)
* [Protocol Buffers](https://code.google.com/p/protobuf/downloads/list) version 2.4.1 or later.

After you do the `git clone` in the following step, see also the file
`~/hadoop-common/BUILDING.txt` for detailed prerequisite information.

## Compiling

    git clone git://git.apache.org/hadoop-common.git
    cd hadoop-common
    mvn clean -DskipTests install package -Dtar -Pdist

## Installing

You will now have a file in the `hadoop-dist/target` directory tar.gz
called `hadoop-$VERSION-SNAPSHOT.tar.gz`, where `$VERSION` will vary
depending on when you clone the Apache Hadoop repository.

For me, today, it's

    $ find -name "hadoop-[0-9]*SNAPSHOT.tar.gz"
    ./hadoop-dist/target/hadoop-0.24.0-SNAPSHOT.tar.gz

Back in your home directory, untar the tar.gz:

    $ cd
    $ tar xfz hadoop-common/hadoop-dist/target/hadoop-0.24.0-SNAPSHOT.tar.gz 

Now make a symlink to this directory to make your configuration and daemon-running simpler:

    $ ln -s hadoop-0.24.0-SNAPSHOT hadoop-runtime

## Configuring

### `core-site.xml`

Open the file `hadoop-runtime/etc/hadoop/core-site.xml` in an editor
and replace it with the following. Change references to `/home/ekoontz/hadoop-runtime` in `yarn.application.classpath` to use your username.

<script src="https://gist.github.com/1933913.js?file=core-site.xml"></script>

### Symlinks

Hadoop trunk uses three configuration files:
`core-site.xml`,`hdfs-site.xml`, and `yarn-site.xml`. Rather than
three separate files, I've chosen to gather all Hadoop configuration
into a single file, `core-site.xml`, and link the other two files to
this. Set up the following symlinks to accomplish this:

    cd ~/hadoop-runtime/etc/hadoop
    mv hdfs-site.xml hdfs-site.dist.xml
    mv yarn-site.xml yarn-site.dist.xml
    ln -s core-site.xml hdfs-site.xml
    ln -s core-site.xml yarn-site.xml
    
## Startup

### Namenode

    bin/hdfs namenode -format
    bin/hdfs namenode &

#### Datanode

    bin/hdfs datanode &

#### Nodemanager
    
    bin/yarn nodemanager &
    
#### Resourcemanager

    bin/yarn resourcemanager &

## Test

### Web UI: HDFS

You should now be able to look at your namenode's web interface at http://localhost:50070.

### Web UI: YARN

You should now be able to look at your resourcemanager web interface at http://localhost:8088.

### Run an example MapReduce Job

    cd ~/hadoop-runtime
    bin/hadoop --config etc/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-0.24.0-SNAPSHOT.jar pi 5 5

Your output should look similar to the following:

    Number of Maps  = 5
    Samples per Map = 5
    Wrote input for Map #0
    Wrote input for Map #1
    Wrote input for Map #2
    Wrote input for Map #3
    Wrote input for Map #4
    Starting Job
    12/02/27 22:29:44 WARN conf.Configuration: mapred.used.genericoptionsparser is deprecated. Instead, use mapreduce.client.genericoptionsparser.used
    12/02/27 22:29:44 INFO input.FileInputFormat: Total input paths to process : 5
    12/02/27 22:29:44 INFO mapreduce.JobSubmitter: number of splits:5
    12/02/27 22:29:44 INFO mapred.ResourceMgrDelegate: Submitted application application_1330381210883_0002 to ResourceManager at localhost/127.0.0.1:8040
    12/02/27 22:29:44 INFO mapreduce.Job: The url to track the job: http://ip-10-178-30-197.us-west-1.compute.internal:8088/proxy/application_1330381210883_0002/
    12/02/27 22:29:44 INFO mapreduce.Job: Running job: job_1330381210883_0002
    12/02/27 22:29:54 INFO mapreduce.Job: Job job_1330381210883_0002 running in uber mode : false
    12/02/27 22:29:54 INFO mapreduce.Job:  map 0% reduce 0%
    12/02/27 22:30:12 INFO mapreduce.Job:  map 20% reduce 0%
    12/02/27 22:30:13 INFO mapreduce.Job:  map 60% reduce 0%
    12/02/27 22:30:14 INFO mapreduce.Job:  map 100% reduce 0%
    12/02/27 22:30:14 INFO mapreduce.Job:  map 100% reduce 100%
    12/02/27 22:30:14 INFO mapreduce.Job: Job job_1330381210883_0002 completed successfully
    12/02/27 22:30:14 INFO mapreduce.Job: Counters: 43
    	File System Counters
    		FILE: Number of bytes read=836
		FILE: Number of bytes written=275445
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=1295
		HDFS: Number of bytes written=215
		HDFS: Number of read operations=23
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=3
	Job Counters 
		Launched map tasks=5
		Launched reduce tasks=1
		Data-local map tasks=5
		Total time spent by all maps in occupied slots (ms)=88309
		Total time spent by all reduces in occupied slots (ms)=17311
	Map-Reduce Framework
		Map input records=5
		Map output records=10
		Map output bytes=90
		Map output materialized bytes=140
		Input split bytes=705
		Combine input records=0
		Combine output records=0
		Reduce input groups=2
		Reduce shuffle bytes=140
		Reduce input records=10
		Reduce output records=0
		Spilled Records=20
		Shuffled Maps =5
		Failed Shuffles=0
		Merged Map outputs=5
		GC time elapsed (ms)=764
		CPU time spent (ms)=3600
		Physical memory (bytes) snapshot=1223208960
		Virtual memory (bytes) snapshot=5357535232
		Total committed heap usage (bytes)=1051000832
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=590
	File Output Format Counters 
		Bytes Written=97
    Job Finished in 30.688 seconds
    12/02/27 22:30:14 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
    Estimated value of Pi is 3.68000000000000000000


## References

[Apache Hadoop 0.23 Overview](http://hadoop.apache.org/common/docs/r0.23.0/)

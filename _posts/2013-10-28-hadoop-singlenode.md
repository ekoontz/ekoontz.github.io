---
layout: post
title: "Hadoop on a single host"
category:
tags: [hadoop guide]
---
{% include JB/setup %}

# Hadoop on a single host


## Install Hadoop distribution

First, download the [binary
tarball](http://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-1.2.1/hadoop-1.2.1.tar.gz)
of the latest Hadoop 1 release (currently 1.2.1).

Untar this to your home directory:

    tar xfz hadoop-1.2.1-bin.tar.gz 

You should now have a `hadoop-1.2.1` directory:

<script src="http://gist.github.com/7198576.js" style="height:5em">
</script>

# HDFS

## Edit `hdfs-site.xml`

Within this directory, find the file `conf/hdfs-site.xml` and use a text editor to change the file
to have the following contents:

<script src="http://gist.github.com/7198632.js" style="height:5em">
</script>

## Format HDFS

Within the `hadoop-1.2.1` directory, run the following:

    bin/hadoop namenode -format

Sample output of this command is shown here:

<script src="http://gist.github.com/7198728.js" style="height:5em">
</script>

Note from the above the `Re-format filesystem in
/tmp/hadoop-ekoontz/dfs/name ? (Y or N)`.  This is because I ran this
command before, and so there is existing contents in this directory. I
confirmed with `Y` because this is only for temporary work and I have
no valuable data there. 

The directory `/tmp/hadoop-XXXX` will vary for your installation
depending on your username - your username will used instead of `ekoontz`.

## Start namenode

Start the namenode with: `bin/hadoop namenode`. 

Sample output of this command:

<script src="http://gist.github.com/7198865.js" style="height:5em">
</script>

## Start datanode

Open up another shell, `cd` to the `hadoop-1.2.1` directory, and do: `bin/hadoop datanode`. 

If you get an error about `Incompatible namespaceIDs` like this:

<script src="http://gist.github.com/7198985.js" style="height:5em">
</script>

Then do the following:

     rm -rf /tmp/hadoop-ekoontz/dfs/data

where `ekoontz` should be replaced with your username as mentioned
above in the "Format HDFS" section, and then rerun the command
`bin/hadoop datanode`.

If all goes well, the output of starting the datanode should look like
this:

<script src="http://gist.github.com/7198969.js" style="height:5em">
</script>

Glance at the first terminal, where you started the namenode. You
should see some acknowledgement from the Namenode that the Datanode
that you started in the second terminal has connected to the Namenode:

<script src="http://gist.github.com/7199032.js" style="height:5em">
</script>

## Test HDFS

Open up a third terminal and `cd` to the `hadoop-1.2.1` directory as
before. Run the following:

	bin/hadoop fs -ls hdfs://localhost:8020/
	bin/hadoop fs -mkdir hdfs://localhost:8020/mydir
	bin/hadoop fs -copyFromLocal README.txt hdfs://localhost:8020/mydir
	bin/hadoop fs -lsr hdfs://localhost:8020/

(`-lsr` means 'ls, recursively', similar to the Unix command `ls -R`).

The output from the above should look similar to the following:

<script src="http://gist.github.com/7199233.js" style="height:5em">

</script>


# MapReduce

## Edit `mapred-site.xml`

Within the `hadoop-1.2.1` directory, find the file
`conf/mapred-site.xml` and use a text editor to change the file to
have the following contents:

<script src="http://gist.github.com/7199282.js" style="height:5em">
</script>

## Start Jobtracker

Open up a new shell, `cd` to the `hadoop-1.2.1` directory and do:
`bin/hadoop jobtracker`.

The output should look something like:

<script src="http://gist.github.com/7199477.js" style="height:5em">
</script>

## Start Tasktracker

Open up a new shell, `cd` to the `hadoop-1.2.1` directory and do:
`bin/hadoop tasktracker`.

The output should look something like:

<script src="http://gist.github.com/7199496.js" style="height:5em">
</script>

Looking at the shell in which you started the Jobtracker, you should
see some acknowledgement that the Jobtracker received a connection
from the Tasktracker:

<script src="http://gist.github.com/7199508.js" style="height:5em">
</script>

## Test MapReduce

We are now ready to run a sample MapReduce job. The example will use
is "Pi", which estimates the value of the mathematical constant Pi by
random sampling. This job's definition is found in a jar file in your
`hadoop-1.2.1` directory called `hadoop-exmples-1.2.1.jar`. 

As before, open up a new terminal, `cd` to `hadoop-1.2.1` and do:

    bin/hadoop jar hadoop-examples-1.2.1.jar pi 5 5

The output of running the job in this terminal should look something
like this:

<script src="http://gist.github.com/7199550.js" style="height:5em">
</script>

The job should take a few seconds to complete. As it is running, watch
the open terminals you have, that are running the Namenode, Datanode,
Jobtracker, and Tasktracker, which should be showing log messages
showing how each component is doing its specific work to run the job.






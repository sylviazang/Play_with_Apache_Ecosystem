# Install a Single Node Hadoop Cluster

In general, to install a single node hadoop cluster, I followed the link here: https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html. However, here are more explanations on commends that I was not familiar with.

## Standalone Operation

The followed command is made in path of unpacked Hadoop distribution.
```
$ mkdir input
$ cp etc/hadoop/*.xml input
```
Copy all .xml file in etc/hadoop directory to subdirectory input.
```
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.1.jar grep input output 'dfs[a-z.]+'
```
Furthur explanation needed. "The following example copies the unpacked conf directory to use as input and then finds and displays every match of the given regular expression. Output is written to the given output directory." According to Hadoop site.
```
$ cat output/*
1	dfsadmin
```
Display the content of all files in output subdirectory.

## Pseudo-Distributed Operation

### Setup passphraseless ssh
For Mac users, SSH is installed by default but the daemon is closed. Therefore, go to "System Peference/Sharing" and check "Remote Login" option.

### Execution
1. Format the filesystem
```
$ bin/hdfs namenode -format
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   user = yize
STARTUP_MSG:   host = Yizes-MacBook-Pro.local/35.1.172.107
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.8.1
STARTUP_MSG:   classpath =...
...
17/07/30 17:57:38 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at Yizes-MacBook-Pro.local/35.1.172.107
************************************************************/
```

2. Start NameNode daemon and DataNode daemon: (Daemon is a process running in the background)
```
$ sbin/start-dfs.sh
```
NameNode, DataNode and secondary NameNode are started. By default NameNode could be checked http://localhost:50070/

3. Make HDFS directory to execute MapReduce jobs:
```
$ bin/hdfs dfs -mkdir /user
$ bin/hdfs dfs -mkdir /user/yize
```

4. Copy the input files into the distributed filesystem:
```
$ bin/hdfs dfs -put etc/hadoop input
```

5. Run an example:
```
$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
```

6. Examine the output files: copy the result from DFS to local or on the DFS:
```
$ bin/hdfs dfs -get output output
$ cat output/*
cat: output/output: Is a directory
1	dfsadmin
```
```
$ bin/hdfs dfs -cat output/*
17/07/30 18:23:05 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
cat: `output/output': No such file or directory
6	dfs.audit.logger
4	dfs.class
3	dfs.logger
3	dfs.server.namenode.
2	dfs.audit.log.maxbackupindex
2	dfs.period
2	dfs.audit.log.maxfilesize
1	dfs.log
1	dfs.file
1	dfs.servers
1	dfsadmin
1	dfsmetrics.log
1	dfs.replication
```
Not sure why the results are different.

7. Stop the daemons:
```
$ sbin/stop-dfs.sh
```

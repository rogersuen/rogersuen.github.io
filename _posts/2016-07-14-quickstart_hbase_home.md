---
title: "HBase Quick Start Error Due to Missing HBASE_HOME Variable"
tags: [HBASE]
excerpt: "The HBASE_HOME variable is required to start standalone HBase following the HBase Quick Start."
---

For my first taste of HBase (1.2.2), I come across the following error when I try to start the standlone HBase by following the HBase Quick Start guide:

```
$ ./start-hbase.sh 
Error: Could not find or load main class org.apache.hadoop.hbase.util.HBaseConfTool
Error: Could not find or load main class org.apache.hadoop.hbase.zookeeper.ZKServerTool
starting master, logging to /Users/rogersuen/hbase-1.1.2/logs/hbase-rogersuen-master.local.out
nice: /Users/rogersuen/hbase-1.1.2/bin/hbase: No such file or directory
cat: /Users/rogersuen/hbase-1.1.2/conf/regionservers: No such file or directory
cat: /Users/rogersuen/hbase-1.1.2/conf/regionservers: No such file or directory
```

After some googling, it turns out the `HBASE_HOME` variable is required to point to the correct installation directory, as you can see the script attempts to access the wrong directory for a different version 1.1.2, which should be 1.2.2 in my case.

To export `HBASE_HOME`, specify the root directory, not any subdirectories as some resources might point out:

```
$ export HBASE_HOME=/Users/rogersuen/hbase-1.2.2
```

## Environments
* OS X El Capitan 10.11.5
* HBase 1.2.2
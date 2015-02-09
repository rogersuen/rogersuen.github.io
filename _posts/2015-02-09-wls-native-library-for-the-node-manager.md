---
title: WebLogic - Native library for the Node Manager
tags: NodeManager WebLogic Administration
excerpt: The Node Manager for a WebLogic domain will be unable to start up with the default configuration on an operating system without the corresponding native library provided by Oracle. To correct this error, the configuration setting NativeVersionEnabled has to be changed to false to disable the native version of the Node Manager for such an operating system.
---

After the WebLogic domain configuration is complete, while starting the Node Manager, you might encounter an error as reported below:

```
WARNING: NodeManager native library could not be loaded to write process id
java.lang.UnsatisfiedLinkError: no nodemanager in java.library.path
	at java.lang.ClassLoader.loadLibrary(ClassLoader.java:1886)
	at java.lang.Runtime.loadLibrary0(Runtime.java:849)
	at java.lang.System.loadLibrary(System.java:1088)
	at weblogic.nodemanager.util.UnixProcessControl.<init>(UnixProcessControl.java:25)
	at weblogic.nodemanager.util.ProcessControlFactory.getProcessControl(ProcessControlFactory.java:23)
	at weblogic.nodemanager.server.NMServer.writeProcessId(NMServer.java:253)
	at weblogic.nodemanager.server.NMServer.writePidFile(NMServer.java:230)
	at weblogic.nodemanager.server.NMServer.<init>(NMServer.java:121)
	at weblogic.nodemanager.server.NMServer.main(NMServer.java:505)
	at weblogic.NodeManager.main(NodeManager.java:31)

<Feb 9, 2015 10:19:50 AM CST> <SEVERE> <Fatal error in NodeManager server: Native version is enabled but NodeManager native library could not be loaded>
```

This is because by default, Oracle enables native libraries for the operating system to be used by the Node Manager, even when the native version is actually not provided for the specific operating system. Here's the statement from the Oracle documentation **[Administering Node Manager for Oracle WebLogic Server](http://docs.oracle.com/middleware/1213/wls/NODEM/toc.htm)**:

> Oracle provides native Node Manager libraries for Windows, Solaris, Linux on Intel, Linux on Z-Series, and AIX operating systems.

To fix this error in an unsupported operating system (like Mac OS, in my case), you can simply disable the native version support by updating the configuration setting in the `nodemanager.properties` file. The file only gets created until the Node Manager has started up once. It's typically in the `$DOMAIN_HOME/nodemanager` directory. In the file, find the the following setting:

```
NativeVersionEnabled=true
```

Update it to be as follow:

```
NativeVersionEnabled=false
```

Now, you can start the Node Manager:

```
nohup ./startNodeManager.sh > nm.out&
```

Check out the log file, the warning about the NodeManager native library could not be loaded is still there, but the Node Manager should start up successfully after printing out the current configuration settings. 

**Resources:**

* [Node Manager Implementations](http://docs.oracle.com/middleware/1213/wls/NODEM/overview.htm#NODEM114)

* [Reviewing nodemanager.properties](http://docs.oracle.com/middleware/1213/wls/NODEM/java_nodemgr.htm#NODEM178)

* [3.2 Starting the Servers](http://docs.oracle.com/middleware/1213/core/WLSIG/create_domain.htm#WLSIG298)
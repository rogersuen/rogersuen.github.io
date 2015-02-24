---
title: BEA-141297 - Could not get the server file lock
tags: WebLogic Administration
excerpt: The solution to the BEA-141297 error - a problem of starting up WebLogic server - by manually removing the server lock file.
---
While starting the WebLogic Administration Server or a Managed Server, you might encounter the following error that prevents the server from starting up:

```
<Feb 9, 2015 1:40:34 PM CST> <Info> <Management> <BEA-141297> <Could not get the server file lock. Ensure that another server is not running in the same directory. Retrying for another 60 seconds.>
```

This is because the server lock file is left behind for some reason from the last run. To fix this error:

* Navigate to the server-specific `tmp` directory under your `$DOMAIN_HOME` directory, in my case, for example: `~/Oracle/config/domains/base_domain/servers/AdminServer/tmp` for the Administration Server or `~/Oracle/config/domains/base_domain/servers/wls_server_1/tmp` for one of the Managed Servers; 
* Delete the lock file for the server instance, `AdminServer.lok` for the Administration Server or `wls_server_1.lok` for the mentioned Managed Server.
* Start the server instance again.
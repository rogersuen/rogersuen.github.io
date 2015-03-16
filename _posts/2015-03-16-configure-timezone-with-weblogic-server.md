---
title: Configuring the Time Zone with WebLogic Server
tags: [Time Zone, WebLogic, ADF Model Tester, ADF]
excerpt: This post introduces how to configure the time zone with an integrated or a standalone Oracle WebLogic Server and Oracle ADF Model Tester.
---
In order to properly handle the date and time data in your ADF applications, you probably need to configure the WebLogic Server time zone. The reasons for which you do this might include but not be limited to:

* Configure the default time zone for &lt;af:convertDateTime&gt; used by input and output components.
* Configure the time zone that affects how Oracle JDBC driver handles the data and time data.

This post introduces how to configure the time zone with an integrated or a standalone WebLogic Server, or the ADF Model Tester. 

## Integrated WebLogic Server and ADF Model Tester

When you are running and testing your application using an Integrated WebLogic Server, or testing your model project with the ADF Model Tester you can configure the time zone by adding the following system property to the **Java Options** on the **Launch Settings** page in the **Edit Run Configuration** window:

    -Duser.timezone=UTC

To do this:

1. Select the project in the **Applications** window.
2. From the main menu, choose **Application** > **Project Properties**
3. Select **Run/Debug**.
4. Choose to **Edit** the selected run configuration (a default run configuration is created for each new project).
5. Add the time zone system property to **Java Options**

![Image: Edit Run Configuration]({{ site.baseurl }}{{ page.id }}/edit-run-configuration.jpg)

The configuration will apply when the Java program is launched from JDeveloper, for example the Integrated WebLogic Server and the ADF Model Tester. To confirm it, you can look for the system property in the **Log** window after the program is launched:

![Image: Log Window]({{ site.baseurl }}{{ page.id }}/log-window.jpg)

Another way to configure the Integrated WebLogic Server time zone is to set it by modifying the properties of the integrated application server:

1. In the **Application Servers** window, right-click the integrated application server (the default instance is called **IntegratedWebLogicServer**), choose **Properties**.
2. Select **Launch Settings** tab.
3. Add the time zone system property to **Java Options**

![Image: Application Server Properties]({{ site.baseurl }}{{ page.id }}/application-server-properties.jpg)

Please note that the Launch Settings of the Application Server Properties are used only when the server starts with no application selected (effectively meaning no application is open in the **Applications** window).

**Caution:** when the server starts with no application selected, and then open the application and run it against the server, the Launch Settings defined in the Application Server Properties will be used; the Java Options defined in the run configuration of the project will be ignored.


## Standalone WebLogic Server

To configure the time zone with a standalone WebLogic Server instance, if you use a WebLogic Server script to start servers, you can edit the `JAVA_OPTIONS` in the script to set the system property, see "[Specifying Java Options for a WebLogic Server Instance](http://docs.oracle.com/middleware/1213/wls/START/overview.htm#START138)"; if you use the Node Manager to start servers, you can set Java Options for each server instance in the Oracle WebLogic Server Administration Console, see "[Set Java options for servers started by Node Manager](http://docs.oracle.com/middleware/1213/wls/WLACH/taskhelp/startstop/SetJavaOptionsForServersStartedByNodeManager.html)".

## Resources:

* [Specifying Java Options for a WebLogic Server Instance](http://docs.oracle.com/middleware/1213/wls/START/overview.htm#START138)
* [Set Java options for servers started by Node Manager](http://docs.oracle.com/middleware/1213/wls/WLACH/taskhelp/startstop/SetJavaOptionsForServersStartedByNodeManager.html)
* [ADF RichClient API - &lt;af:convertDateTime&gt;](https://docs.oracle.com/middleware/1213/adf/tag-reference-faces/tagdoc/af_convertDateTime.html)

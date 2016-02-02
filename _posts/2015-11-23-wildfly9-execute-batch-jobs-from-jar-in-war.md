---
title: "Wildfly 9 - Execute Batch Jobs from a JAR in a WAR"
tags: [Java EE, batch, JSR-352, Wildfly]
excerpt: "Describes on Wildfly 9, how to execute Java EE JSR-352 batch jobs from a JAR in a WAR and avoid JBERET000601."
---

It's very common to package Java EE JSR-352 batch jobs and artifacts in a JAR file, and execute the jobs in a web application with the JAR as a dependency. In Wildfly 9, due to the [Class Loading](https://docs.jboss.org/author/display/WFLY9/Class+Loading+in+WildFly) based on JBoss Modules and [an issue](https://issues.jboss.org/browse/WFLY-4988) in Wildly 9, you might end up with an exception: `javax.batch.operations.JobStartException: JBERET000601: Failed to get job xml file for job XXX`.

This post is about how to execute Java EE JSR-352 batch jobs from a JAR file in a WAR archive on Wildfly 9 particularly.

## `META-INF/batch-jobs` in the WAR

In the WAR archive, make sure you have a `META-INF/batch-jobs` directory. It goes under the `WEB-INF/classes` directory in a WAR archive. If you don't really have any job XML files in this directory, put a README file for example to make sure it's not empty in order to avoid being ignored by the packaking tool.

## `WEB-INF/beans.xml` in the WAR

If you are using CDI for job artifacts in the WAR, make sure you have a `beans.xml` file for CDI under the `WEB-INF` directory. This is the trigger which leads Wildfly implicit module dependency to CDI subsystem being added.

## `META-INF/batch-jobs` in the JAR

In the JAR file, you need storing the job XML documents under the `META-INF/batch-jobs` directory of course.

## `META-INF/beans.xml` in the JAR

If you are using CDI for job artifacts in the JAR, you should have a CDI `beans.xml` file under the `META-INF` directory in the JAR. This will ensure Wildfly will scan the JAR for job artifacts. This is optional for some deployments though.

## `META-INF/services/org.jberet.spi.JobXmlResolver` in the JAR

As a workaround for Wildfly 9 particularly, you need place a *service provider configuration* file in the resource directory `META-INF/services` in the JAR, with the name `org.jberet.spi.JobXmlResolver`. The configuration file contains only the following line:

    org.jberet.tools.MetaInfBatchJobsJobXmlResolver

This service provider configuration file will ensure Wilefly 9 to scan the `META-INF/batch-jobs` directory in the same JAR file for job XML documents.

## Resources
* [Implicit module dependencies for deployments](https://docs.jboss.org/author/display/WFLY9/Implicit+module+dependencies+for+deployments)
* [Can't load job from another jar inside ear](https://issues.jboss.org/browse/WFLY-4988)
---
title: ADF - Package reusable components into an ADF Library JAR
tags: ['ADF Library JAR', 'Deployment', 'Test',  'ADF', 'ADFSamples']
blogger_url: /2014/11/adf-package-reusable-components-into-adf-library-jar.html
excerpt: 
  Use a bare project with its project source paths pointing to the corresponding ones in the orginal project, to deploy a clean ADF Library JAR with only relevant artifacts selected by including or excluding artifacts in the bare project, making testing of reusable components as easy as usual. 
---
When we work on ADF components, we may want to reuse them across different projects, applications and teams. Oracle ADF and JDeveloper support to package reusable components and associated artifacts into an ADF Library JAR. An ADF Library JAR is a special JAR containing ADF components and appropriate control files for the components. ADF page templates is one of types of these high-level reusable components that can be packaged into an ADF Library JAR.

I'm not going to introduce how to package reusable ADF components into an ADF Library JAR in general here again, because the official documentation **Developing Fusion Web Applications with Oracle Application Development Framework** itself is a great resource where you can learn about it. You can find the information from the chapter [**44 Reusing Application Components**](https://docs.oracle.com/middleware/1213/adf/develop/adf-web-component-reuse.htm#ADFFD1845).

In this post, taking page templates as an example, I'll be talking about a practical approach to package only required artifacts into an ADF Library JAR, leaving irrelevant files out of it, and still allowing us to develope and test the reusable components easily.

You can find and download [sample sources](https://github.com/adfsamples/PageTemplateADFLibrary) for this post. The sample sources consists of two applications - the `pagetemplates` application for the ADF Library containing the page template, and the `consumer` application that uses the page template from the ADF Library: 

![Image: Sample Applications]({{ site.data.config.download_url }}/{{ page.id }}/sample-applications.png)

To create ADF page templates as reusable components, we start from an ADF ViewController project. In the sample applications, the `pagetemplates-webapp` project in the `pagetemplates` application is such an ADF ViewController project. It uses the popular Bootstrap framework to create a very simple page template. We can test the page template with test pages in the root directory of web contents.

If we create an ADF Library JAR deployment profile for the `pagetemplates-webapp` project to package the page template as a reusable component, we will have all the required artifacts and resources in the result ADF Library JAR. As a side-effect, some artifacts we don't really want will also be included, such as those test pages and irrelevant metadata files, in this case, files under the `WEB-INF/` directory, etc. Unfortunately, it's now allowed to configure the files and folders that are to be packaged into an ADF Library JAR.

You can surely remove unwanted artifacts from the project to get a tidy JAR, but that will make development and testing of your page templates very hard. You might consider creating another ADF ViewController project that uses the deployed ADF Library JAR to test the page templates. Better this time, but every time you make a change to you page templates, you have to build and deploy your ADF Library JAR, and then rebuild and run your testing project to see the result. You would eventually rather stay with your untidy JAR.

To get a clean ADF Library JAR without making testing a nightmare, instead of deploying the ADF Library JAR directly from the original ADF ViewController project containing the page templates, we can create another ADF ViewController project dedicated in deployment purpose. This second project is a bare ViewController project actually, with all appropriate project source paths configured to point to the corresponding ones in the original project. With project source paths, it's allowed to configure a list of files or folders that are to be specifically included in the current project, such that you can choose now which artifacts are going to be packaged into the ADF Library JAR deployment profile.

In the sample application, the `pagetemplates-adflib` project is created as an ADF ViewController project for this purpose. Once the project is created, its project source paths are set to point to the respective ones in the `pagetemplates-webapp` project, and choose to include or exclude selected files or folders for the current project.Particularly, In the **Project Source Paths** window, it looks like this:

![Image: Project Properties / Project Source Paths]({{ site.data.config.download_url }}/{{ page.id }}/project-source-paths.png)

And in the **Project Source Paths: Web Application** window, the setting is as follow:

![Image: Project Properties / Project Source Paths: Web Application]({{ site.data.config.download_url }}/{{ page.id }}/project-source-paths-web-application.png)

Finally, create an ADF Library JAR deployment profile for the `pagetemplates-adflib` project:

![Image: Project Properties / Deployment]({{ site.data.config.download_url }}/{{ page.id }}/deployment.png)

**Note:** the deploy folder is configured intentionally under the root folder containing the sample applications.

With this approach, we can develop and test the page templates in the full ADF ViewController project, and deploy the ADF Library JAR any time from the bare ViewController project. The same approach also works for other types of reusable ADF components. Enjoy!

**Chinese Summary:** 

在开发可重用的 ADF 组件，并将其打包为 ADF 库文档时，如果不希望包含那些无关的配置文件和用于测试的文件时。可以创建两个 ADF 视图控制器项目，其中之一为完整项目，包含组件及其所需工件和资源文件，以及所有用于测试的文件。另一个为裸项目，其项目源代码路径都设置指向完整项目中的对应路径，同时设置包含在该项目中的文件或目录的列表。基于这个方法，完整的项目用于开发和测试可重用组件；而为裸项目定义一个 ADF 库文档部署概要文件，专门用于部署 ADF 库文档。

**Sample Applications:**

* [Source Code](https://github.com/adfsamples/PageTemplateADFLibrary)
* [Download ZIP](https://github.com/adfsamples/PageTemplateADFLibrary/archive/master.zip)

**Environment:**

* Mac OS X Version 10.10
* Safari Version 8.0
* JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S
* Oracle Alta UI

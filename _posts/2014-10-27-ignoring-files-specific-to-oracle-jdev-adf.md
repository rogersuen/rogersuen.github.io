---
title: Ignoring Files Specific to Oracle JDeveloper & ADF
tags: [ADF, JDeveloper, "Version Control", Git, Subversion]
blogger_url: /2014/10/ignoring-files-specific-to-oracle-jdev-adf.html
excerpt: 
  Minimum list of untracked files specific to Oracle JDeveloper and ADF for the .gitignore file of Git or similar ignore files used by other version control system, such as Subversion and CVS.
---
If you are using Oracle JDeveloper and ADF with some version control system, such as Git, here's a minimum list of JDeveloper and ADF specific untracked files and directories you will want to add to your `.gitignore` file to ignore them:

    .data/
    temp/
    classes/
    cwallet.sso.lck

The `.data/` directory is the application storage directory used by the **IDE Performance Cache** feature. For each application, you can specify a directory where application-specific caches and indexes used by JDeveloper are stored. To customize the location of the directory, open the **Application Properties** dialog, then go to the **IDE Performance Cache** page.


![Image: IDE Performance Cache]({{ site.baseurl }}{{ page.id }}/ide-performance-cache.png)


The default location is the root directory of the application:


![Image: IDE Performance Cache]({{ site.baseurl }}{{ page.id }}/ide-performance-cache-data.png)


The `temp/` directory is used for ADF styles caching. It gets created under the `WEB-INF/` directory in the web content directory of the view project, when you open a page or page fragment in the design mode in JDeveloper.


![Image: ADF Styles caching]({{ site.baseurl }}{{ page.id }}/adf-styles-cache.png)


The `classes/` directory is the default Output Directory for the project. It can be customized in the Project Properties dialog, Project Source Paths page.


![Image: Project Source Paths]({{ site.baseurl }}{{ page.id }}/project-source-paths.png)


The `cwallet.sso.lck` file is the lock file for the `wallet.sso` file which is a part of **Oracle Credential Store Framework**.

The ignore file list is also useful when you are using Subversion or CVS.

**Chinese Summary:**

针对 Oracle 的 JDeveloper 和 ADF 的，用于版本控制系统的忽略文件列表。可以添加到诸如 Git 的 `.gitignore` 文件，或者类似的其它版本控制系统（Subversion 和 CVS）的忽略文件中。

---
title: ADF - Access resources from an ADF Library JAR
tags: ['ADF Library JAR', 'Deployment', 'ADF', 'ADFSamples']
excerpt: 
  To load resources of a reusable component packaged in an ADF Library JAR, the web.xml of the consuming web application must be extended with the entries of filter and servlet to serve the resources from the JAR. For resources other than commonly used file types, like font files (eot, rtf, off, svg), you need configure include extension list for the filter.

---
Oracle ADF and JDeveloper support packaging a reusable component and its resources into an ADF Library JAR. When the component is consumed by a web application, ADF and JDeveloper automatically make the resources of component available at runtime to serve requests. At most time, you don't have to care about the detail behind the scene. But if the resources (`css`, `js`, `png`, `jpeg`, etc.) cannot be served from the ADF Library JAR, or only some special resources cannot be accessed, then this post is probably for you. As using bundled font files in a web application is getting so popular these days (e.g. Bootstrap uses font files for its glyph icons), I guess you are reading this post probably because of missing font files (`eot`,`ttf`,`woff`,`svg`).

When you deploy a project into an ADF Library JAR, JDeveloper packages artifacts under the web content root directory (if any) to the JAR. When you make use of a component from the JAR in your web application JDeveloper extends the `web.xml` file in your web application with two key players for accessing resources from an ADF Library JAR - the `adflibResources` servlet and the `ADFLibraryFilter`.

Here's the entries for the `adflibResources` servlet:

```
<servlet>
  <servlet-name>adflibResources</servlet-name>
  <servlet-class>oracle.adf.library.webapp.ResourceServlet</servlet-class>
</servlet>

<servlet-mapping>
  <servlet-name>adflibResources</servlet-name>
  <url-pattern>/adflib/*</url-pattern>
</servlet-mapping>
```

And the entries for the `ADFLibraryFilter`:

```
<filter>
  <filter-name>ADFLibraryFilter</filter-name>
  <filter-class>oracle.adf.library.webapp.LibraryFilter</filter-class>
</filter>

<filter-mapping>
  <filter-name>ADFLibraryFilter</filter-name>
  <url-pattern>/*</url-pattern>
  <dispatcher>FORWARD</dispatcher>
  <dispatcher>REQUEST</dispatcher>
</filter-mapping>
```
Usually, JDeveloper can add these entries automatically if it's aware that you are using a component from an ADF Library JAR, for example, when you use the wizard to create a page based on a page template exposed from the JAR. If for some reason, these entries are not there, you can surely manually add them there.

At runtime, when the web application serves a request, if a resource is not found in the web content directory of the web application, the `ADFLibraryFilter` forwards the request to the `adflibResources` servlet to load the resource from the ADF Library JAR. By default, the resources handled by the `ADFLibraryFilter` are files with an extension in the following list:

`",png,jpg,jpeg,gif,js,css,htm,html,"`

So, for any other type of resources, you will end up with a `404` error. Fortunately, it's allowed to customize this list. For the Bootstrap font files case, you can customize the extension list with the `include-extension-list` initialization parameter of the `ADFLibraryFilter` like this:

```
<filter>
  <filter-name>ADFLibraryFilter</filter-name>
  <filter-class>oracle.adf.library.webapp.LibraryFilter</filter-class>
  <init-param>
    <param-name>include-extension-list</param-name>
    <param-value>,png,jpg,jpeg,gif,js,css,htm,html,eot,ttf,woff,svg,</param-value>
  </init-param>
 </filter>
```

**Chinese Summary:** 为了加载 ADF 库文档中的资源，Web 应用程序的 `web.xml` 文件需要配置 `adflibResources` servlet 以及 `ADFLibraryFilter` 过滤器。对于特殊文件类型的资源，例如字体文件等，还需要配置 `ADFLibraryFilter` 的初始化参数以允许其处理此类文件。

**Sample Application:**

* <https://github.com/adfsamples/ADFLibraryResources>
* [Download ZIP](https://github.com/adfsamples/ADFLibraryResources/archive/master.zip)

**Environment:**

JDeveloper 12.1.3.0.0 Build JDEVADF_12.1.3.0.0_GENERIC_140521.1008.S

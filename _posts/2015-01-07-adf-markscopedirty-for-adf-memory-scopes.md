---
title: ADF - The markScopeDirty() method for ADF memory scopes
tags: [ADF Controller, Managed Bean, ADF, HA]
keywords: [markScopeDirty, pageFlowScope, viewScope]
excerpt: Managing memory-scoped data as properties of managed beans, instead of putting them directly into memory scopes is a good practice in many ways, but don't forget markScopeDirty() when you are using ADF scopes - page flow scope and view scope, in an HA enabled, clustered environment.
---

We have a coding convention of using memory-scoped data in our ADF Faces development - always manage memory-scoped data, such as parameters or state as properties of managed beans. Comparing to putting the data directly into memory scopes, the convention has the following benefits:

* allows proper documentation
* allows validation, initialization and logging
* helps understanding and maintenance.

There is a caveat however, when applying the coding convention to the ADF Faces-specific scopes - page flow scope and view scope. 

The ADF Controller uses session scoped objects to hold the page flow scope and view scope. When the high availability (HA) mode is on, the application server will serialize any objects in the session scope and replicate the serialized data within the cluster. To void blind serialization of the page flow scope and view scope, ADF optimizes the process - you have to make sure the framework is aware of changes to one of these ADF scopes by marking the scope as dirty.

If the scope is modified by calling its `put()`, `remove()`, or `clear()` methods, the framework will handle the marking, such that you don't have to care about it when you put data directly into the scope. When the properties of managed beans are used as suggested in our coding convention, then the framework must be notified of changes to the properties. This can be done by the code like this:

```java
Map<String, Object> viewScope = AdfFacesContext.getCurrentInstance().getViewScope();
ControllerContext ctx = ControllerContext.getInstance();
ctx.markScopeDirty(viewScope);
```

Repeating this for every property of managed beans is surely bad. Hard-coding the scope where the managed beans are put into does not look like a good idea either. These concerns lead to the following solution in the base class for all managed beans:

```java
public static final String VIEW_SCOPE = "view";
public static final String PAGE_FLOW_SCOPE = "pageFlow";
private String scope;

public String getScope() {
    return scope;
}

public void setScope(String scope) {
    if (scope == null || VIEW_SCOPE.equals(scope) ||
        PAGE_FLOW_SCOPE.equals(scope)) {
        this.scope = scope;
    } else {
        throw new IllegalArgumentException("Unsupported ADF scope: " +
                                           scope);
    }
}

public void markScopeDirty() {
    if (this.scope == null) {
        return; // no scope is specified, skip
    }

    String prop =
        ControllerConfig.getCurrentProperty(ControllerProperty.ADF_SCOPE_HA_SUPPORT);
    if (!"true".equals(prop)) {
        return; // support not enabled, skip
    }

    Map<String, Object> scopeObject = null;
    if (VIEW_SCOPE.equals(this.scope)) {
        scopeObject = AdfFacesContext.getCurrentInstance().getViewScope();
    } else if ("pageFlowScope".equals(this.scope)) {
        scopeObject =
            AdfFacesContext.getCurrentInstance().getPageFlowScope();
    } else {
        // should never happen, setScope() has done the validation
    }

    ControllerContext.getInstance().markScopeDirty(scopeObject);

    System.out.println("DEBUG: markScopeDirty for HA [scope=" + scope +
                       "]");
}
``` 

Now it's much easier to mark the scope as dirty, simply call the `markScopeDirty()` method in the property setter method of the managed bean. 

How to set the scope that should be marked? It's recommended to use the `scope` property as a **managed property**, e.g. set the `scope` property right at the point where and when the managed bean is declared to be in that scope:

![Image: The scope managed property]({{ site.baseurl }}{{ page.id }}/managed-property.png)

**Resources**:

* [26.4.3 How to Set Managed Bean Memory Scopes in a Server-Cluster Environment](https://docs.oracle.com/middleware/1213/adf/develop/adf-ui-get-started.htm#ADFFD22271)
* [48.3 Troubleshooting Fusion Web Applications for High Availability](https://docs.oracle.com/middleware/1213/adf/develop/adf-web-high-availability.htm#ADFFD53980)
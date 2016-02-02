---
title: "Hibernate 5.0.2 - Specifying Schema Causes AssertionFailure in JoinedSubclassEntityPersister"
tags: [Hibernate 5, Wildfly 10, JPA]
excerpt: "When using Hibernate 5.0.7 bundled in WildFly 10, specifying schema for entities using JOINED inheritance strategy causes AssertionFailure in JoinedSubclassEntityPersister"
---

After migrate to Wildfly 10, my web application based on JPA and Hibernate cannot be deployed due to the following error:

```
Caused by: org.hibernate.AssertionFailure: Table xxx.XXX not found
	at org.hibernate.persister.entity.AbstractEntityPersister.getTableId(AbstractEntityPersister.java:5107)
	at org.hibernate.persister.entity.JoinedSubclassEntityPersister.<init>(JoinedSubclassEntityPersister.java:433)
	at sun.reflect.GeneratedConstructorAccessor89.newInstance(Unknown Source)
	at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.lang.reflect.Constructor.newInstance(Constructor.java:422)
	at org.hibernate.persister.internal.PersisterFactoryImpl.createEntityPersister(PersisterFactoryImpl.java:96)
	... 13 more
```

I tested against Hibernate 5.0.1, up to 5.0.7 which is bundled in WildFly 10. It turns out until Hibernate 5.0.1, my application works just fine. From Hibernate 5.0.2 and higher, it's broken.

It seems the `<schema>` elements in my ORM mapping file for those entities in the entity hierarchy using the `JOINED` inheritance strategy triggering the problem. I have reported an issue at [HHH-10490](https://hibernate.atlassian.net/browse/HHH-10490).

Current workaround is to remove those `<schema>` elements for entities using `JOINED` inheritance strategy from the mapping file. If it's not good for your case, you can also downgrade to Hibernate 5.0.1. For those WildFly 10 users, add the following property to your `persistence.xml`:

```
<property name="jboss.as.jpa.providerModule" value="application" />
```

Then, add Hibernate 5.0.1 as a dependency of your application, so that the required Hibernate artifacts will be packaged along with your application.

## Environments
* WildFly 10
* Hibernate 5
* MySQL Connector/J 5.1.37

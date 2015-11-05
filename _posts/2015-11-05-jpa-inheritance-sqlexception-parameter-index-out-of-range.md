---
title: "JPA Inheritance and SQLException: Parameter Index Out of Range"
tags: [JPA, Hibernate, Inheritance]
excerpt: "When you work with JPA inheritance, you don't have to map the discriminator column to any field in any of the classes in the hierarchy. If you do so, you might encounter the SQLException: Parameter index out of range."
---

JPA (Java Peristence API) supports inheritance. When working with the `SINGLE_TABLE` or `JOINED` mapping strategy, the `@DiscriminatorColumn` annotation (or `discriminator-column` element if you are using XML mapping descriptor) is used to specify the name of the type discriminator column. This colummn will not be mapped to any field in any of the classes in the entities hierarchy. If you do so, you might encounter the SQLException: Parameter index out of range. I'm using MySQL and Hibernate, the stack trace looks like this:

```
Caused by: org.hibernate.exception.GenericJDBCException: could not insert: [...]
	at org.hibernate.exception.internal.StandardSQLExceptionConverter.convert(StandardSQLExceptionConverter.java:54)
	at org.hibernate.engine.jdbc.spi.SqlExceptionHelper.convert(SqlExceptionHelper.java:126)
	at org.hibernate.id.insert.AbstractReturningDelegate.performInsert(AbstractReturningDelegate.java:65)
	at org.hibernate.persister.entity.AbstractEntityPersister.insert(AbstractEntityPersister.java:3032)
	at org.hibernate.persister.entity.AbstractEntityPersister.insert(AbstractEntityPersister.java:3556)
	at org.hibernate.action.internal.EntityIdentityInsertAction.execute(EntityIdentityInsertAction.java:97)
	at org.hibernate.engine.spi.ActionQueue.execute(ActionQueue.java:480)
	at org.hibernate.engine.spi.ActionQueue.addResolvedEntityInsertAction(ActionQueue.java:191)
	at org.hibernate.engine.spi.ActionQueue.addInsertAction(ActionQueue.java:175)
	at org.hibernate.engine.spi.ActionQueue.addAction(ActionQueue.java:210)
	at org.hibernate.event.internal.AbstractSaveEventListener.addInsertAction(AbstractSaveEventListener.java:324)
	at org.hibernate.event.internal.AbstractSaveEventListener.performSaveOrReplicate(AbstractSaveEventListener.java:288)
	at org.hibernate.event.internal.AbstractSaveEventListener.performSave(AbstractSaveEventListener.java:194)
	at org.hibernate.event.internal.AbstractSaveEventListener.saveWithGeneratedId(AbstractSaveEventListener.java:125)
	at org.hibernate.jpa.event.internal.core.JpaPersistEventListener.saveWithGeneratedId(JpaPersistEventListener.java:84)
	at org.hibernate.event.internal.DefaultPersistEventListener.entityIsTransient(DefaultPersistEventListener.java:206)
	at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:149)
	at org.hibernate.event.internal.DefaultPersistEventListener.onPersist(DefaultPersistEventListener.java:75)
	at org.hibernate.internal.SessionImpl.firePersist(SessionImpl.java:807)
	at org.hibernate.internal.SessionImpl.persist(SessionImpl.java:780)
	at org.hibernate.internal.SessionImpl.persist(SessionImpl.java:785)
	at org.hibernate.jpa.spi.AbstractEntityManagerImpl.persist(AbstractEntityManagerImpl.java:1181)
	... 1 more
Caused by: java.sql.SQLException: Parameter index out of range (1 > number of parameters, which is 0).
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:998)
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:937)
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:926)
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:872)
	at com.mysql.jdbc.PreparedStatement.checkBounds(PreparedStatement.java:3367)
	at com.mysql.jdbc.PreparedStatement.setInternal(PreparedStatement.java:3352)
	at com.mysql.jdbc.PreparedStatement.setInternal(PreparedStatement.java:3389)
	at com.mysql.jdbc.PreparedStatement.setNull(PreparedStatement.java:3428)
	at org.hibernate.type.descriptor.sql.BasicBinder.bind(BasicBinder.java:77)
	at org.hibernate.type.AbstractStandardBasicType.nullSafeSet(AbstractStandardBasicType.java:282)
	at org.hibernate.type.AbstractStandardBasicType.nullSafeSet(AbstractStandardBasicType.java:277)
	at org.hibernate.type.AbstractSingleColumnStandardBasicType.nullSafeSet(AbstractSingleColumnStandardBasicType.java:56)
	at org.hibernate.persister.entity.AbstractEntityPersister.dehydrate(AbstractEntityPersister.java:2843)
	at org.hibernate.persister.entity.AbstractEntityPersister.dehydrate(AbstractEntityPersister.java:2818)
	at org.hibernate.persister.entity.AbstractEntityPersister$4.bindValues(AbstractEntityPersister.java:3025)
	at org.hibernate.id.insert.AbstractReturningDelegate.performInsert(AbstractReturningDelegate.java:57)
	... 20 more
```

There's not necessarily a field mapping to the discriminator column, because for any specific concrete entity, its type discriminator value is basically a constant.

## Environment

* Java Persistence API 2.1
* Hibernate 4.3.1
* MySQL Connector 5.1.36
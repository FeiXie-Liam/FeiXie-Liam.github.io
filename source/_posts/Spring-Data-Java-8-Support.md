---
title: Spring-Data-Java-8-Support
date: 2018-11-12 15:25:01
tags:
 - spring
 - java 8
 - JPA
categories:
 - spring

---

[TOC]

Spring data support core Java 8 features – such as *Optional*, *Stream* API and *CompletableFuture*.

<!--more-->

## Optional

---

now CrudRepository wrap results in an Optional.

```java
public interface UserRepository extends JpaRepository<User, Integer> {
     
    Optional<User> findOneByName(String name);
     
}
```

When returning an [*Optional*](https://www.baeldung.com/java-optional) instance, it’s a useful hint that there’s a possibility that the value might not exist. 

all we need to do is to specify return type as an Optional:

```java
public interface UserRepository extends JpaRepository<User, Integer> {
    
    Optional<User> findOneByName(String name);
    
}
```

## Stream API

---

Spring Data also provides the support for the Stream API.

In the past, whenever we needed to return more than one result, we needed to return a collection, one of the problems with this implementation was the memoory consumption. we had to eagerly load and keep all retrieved objects in it.

one thing we can do is leveraging paging:

```java
public interface UserRepository extends JpaRepository<User, Integer> {
    // ...
    Page<User> findAll(Pageable pageable);
    // ...
}
```

but it can only work in some scenarios. we can now **define that our repository method returns just a Stream of objects**:

```java
public interface UserRepository extends JpaRepository<User, Integer> {
    // ...
    Stream<User> findAllByName(String name);
    // ...
}
```

**Spring Data use provider-specific implementation to stream result.** It reduces the amount of memory comsumption and query calls to a database. Because of that, it's much faster than two solutions mentioned earlier.

Processing data with a Stream requires us to close a Stream when we finish it. It can be done calling the close() method on a Stream or by using try-with-resources:

```java
try (Stream<User> foundUsersStream 
  = userRepository.findAllByName(USER_NAME_ADAM)) {
  
assertThat(foundUsersStream.count(), equalTo(3l));
}
```

**Note**: remember to call a repository method within a transaction. Otherwise, we'll get a exception:

> *org.springframework.dao.InvalidDataAccessApiUsageException*: You’re trying to execute a streaming query method without a surrounding transaction that keeps the connection open so that the *Stream* can actually be consumed. Make sure the code consuming the stream uses *@Transactional* or any other way of declaring a (read-only) transaction.

## CompletableFuture

---

**Spring Data repositories can run asynchronously with the support of Java 8’s CompletableFuture** and Spring mechanism for asynchronous method execution:

```java
`@Async``CompletableFuture<User> findOneByStatus(Integer status);`
```

A client which calls this method will return a future immediately but a method will continue an execution in a different thread.
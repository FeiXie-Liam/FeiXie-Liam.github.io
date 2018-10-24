---
title: TWU learning route
date: 2018-10-14 20:34:37
tags:
 - 培训
 - java
 - oo
categories:
 - 培训

---

### OO recap

---

- use `final` if the variable never change
- use polymorphism if there are several relevant class
  - use `interface ` if several class has same method
  - use inherent if class is a special case of another class
- use factory design pattern to create a Object(eg. square -> rectangle )

### TDD

---

First write the test case and then the simplest implementation according to the test case, the small step to achieve the code through the test, refactoring the original code, the above steps are repeated several times, and finally complete the project development.

### GIT

---

- git add -> add local file to cache
- git commit -> commit file to local repo
- git pull -> pull file from remote repo
- git pull --rebase -> combine commit to one branch 
- git rebase —continue -> continue pull after resolve conflicts

### Value Object

---

- **same** means that two objects have same value and address
- **equal** means that two objects have same value
- `==` to validate object is same or not
- override equals() to validate object is equal or not
- use hashcode when use HashSet, HashMap

### Mocks and Stubs

---

- mock focus on behavior, usually don't have a return value
- stub focus on return value
- inherent test class to create a mock class and override the test method
- use mockito or someother framework to create mocks or stubs

### Application dojo

---

![mvc](http://wiki.jikexueyuan.com/project/spring/images/mvc1.png)

When accepting an HTTP request (GET/POST/PUT/DELETE), Spring MVC first enters the DispatcherServlet for request distribution, and then passes the message to the Controller layer through HandlerMapping.

 The Controller layer accepts the request message through the corresponding URI and request type, and then transfers To the Service layer to perform related business processing on the input message.

When the Service performs related business processing, it is necessary to define the entity object Model, the Model corresponds to the corresponding table in the database, exchange data with the database by operating the Repository, and return the data processed by the business logic. Data, that is, after completing the View Resolver, the processed data is handed over to the View layer for page data update and rendering.

### MyBatis Migrations

---

add dependences to build.gradle

> compile 'org.mybatis:mybatis:3.4.5

use migrate command(go to /migration folder first):

- migrate up/down => apply/undo migrate file to database
- migrate status => report current state of the database
- migrate bootstrap => deal with status of a exist database 
- migrate new => create a new migrate script

migrate file always contain to part for up and down command, for example:

````sql
-- // add country of residence to account
-- Migration SQL that makes the change goes here.
ALTER TABLE account
ADD COLUMN country_of_residence CHARACTER VARYING(255) NOT NULL DEFAULT '';


-- //@UNDO
-- SQL to undo the change goes here.
ALTER TABLE account
DROP COLUMN country_of_residence;
````

### Some bash command

---

#### set

- set -u => throw error and exit if variable don't exist
- set -x => print the command before execute
- set -e => if any command failed then exit

#### if condition

if structure:

```bash
if [ <some test> ]
then
	<commands>
elif [ <some test> ] 
then
	<different commands>
else
	<other commands>
fi
```

possible operators in if condition:

| Operator              | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| ! EXPRESSION          | The EXPRESSION is false.                                     |
| -n STRING             | The length of STRING is greater than zero.                   |
| -z STRING             | The lengh of STRING is zero (ie it is empty).                |
| STRING1 = STRING2     | STRING1 is equal to STRING2                                  |
| STRING1 != STRING2    | STRING1 is not equal to STRING2                              |
| INTEGER1 -eq INTEGER2 | INTEGER1 is numerically equal to INTEGER2                    |
| INTEGER1 -gt INTEGER2 | INTEGER1 is numerically greater than INTEGER2                |
| INTEGER1 -lt INTEGER2 | INTEGER1 is numerically less than INTEGER2                   |
| -d FILE               | FILE exists and is a directory.                              |
| -e FILE               | FILE exists.                                                 |
| -r FILE               | FILE exists and the read permission is granted.              |
| -s FILE               | FILE exists and it's size is greater than zero (ie. it is not empty). |
| -w FILE               | FILE exists and the write permission is granted.             |
| -x FILE               | FILE exists and the execute permission is granted.           |

#### scp

copy files in multi servers through ssh
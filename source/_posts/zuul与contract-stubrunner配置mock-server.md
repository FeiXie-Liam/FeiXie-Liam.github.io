---
title: zuul与contract-stubrunner配置mock-server
date: 2018-08-30 21:10:51
tags:
 - contract
 - zuul
categories:
 - 后端

---

## 背景

---

由于微服务系统经常存在各种微服务之间相互调用，若单独对某个微服务进行修改必须启动其所依赖的所有服务，为了解决这一痛点，本文使用zuul与stubrunner组成mock-server，模拟所有依赖的服务，这样在对某个微服务进行开发时，可以只需要启动mock-server即可正常运行微服务。

<!--more-->

## 缺陷

---

该方法依赖于在前期已经撰写的非常完备的契约，并需要将其发布到共有仓库以供调用。

## 实现方式

---

两个依赖：

```
compile('org.springframework.cloud:spring-cloud-starter-netflix-zuul')
compile('org.springframework.cloud:spring-cloud-starter-contract-stub-runner')
```

两个注解：

```java
@SpringBootApplication
@EnableZuulProxy
@AutoConfigureStubRunner
public class ContractApplication {

    public static void main(String[] args) {
        SpringApplication.run(ContractApplication.class, args);
    }
}
```

两段配置

```yaml
stubrunner:
  ids: com.thoughtworks:contract:+:stubs:8090
  repositoryRoot: http://localhost:8081/nexus/content/repositories/snapshots/
  stubs-mode: local
zuul:
  routes:
    product:
      path: /products/**
      url: http://localhost:8090/products
```

最后将调用了mock-server服务的微程序端口指向mock-server即可自动从mock-server调用对应的stubs。
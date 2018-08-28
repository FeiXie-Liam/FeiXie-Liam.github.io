---
title: 如何使用FeignClient
date: 2018-08-28 21:13:17
tags:
 - Feign
 - Spring Cloud
categories:
 - Spring Cloud

---

## FeignClient中的配置问题

----

在build.gradle中添加如下依赖

```}
ext {
    springCloudVersion = 'Finchley.SR1'
}

dependencies {
   compile('org.springframework.cloud:spring-cloud-starter-openfeign')
    compile('org.springframework.cloud:spring-cloud-starter-netflix-ribbon')
}

dependencyManagement {
    imports {
        mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
    }
}
```

Ribbon依赖用于自动负载均衡，如果没有使用Euraka服务注册与发现，则需要自己在application.yml文件中进行相应的配置，否则程序运行过程中无法找到Client对应的服务。

在application.yml中，需要配置客户端server以及其相应的地址

```yaml
client-server:
  ribbon:
    listOfServers: localhost:8090
    
hlp:
  client-server:
    url: http://localhost:8090
```

Client类

```java
@FeignClient(value = "client-server", url = "${hlp.client-server.url}")
public interface ProductClient {
...
}
```

最后在应用程序入口处中加入`@EnableFeignClients`注解。
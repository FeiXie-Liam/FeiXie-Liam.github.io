---
title: 契约测试示例-spring-cloud-contract
date: 2018-08-25 23:32:00
tags:
 - spring cloud contract
categories:
 - spring cloud
---

# Spring Cloud Contract

本文展示了使用Spring Cloud Contract，实现契约测试的必要代码。

Provider端:

## 首先配置Contract相关依赖

---

<!--more-->

build.gradle

```gradle
buildscript {
    ...
    dependencies {
        ...
        classpath("org.springframework.cloud:spring-cloud-contract-gradle-plugin:2.0.1.RELEASE")
    }
}

...
apply plugin: 'spring-cloud-contract'
...

dependencies {
    testCompile('org.springframework.cloud:spring-cloud-starter-contract-verifier')
}

contracts {
    baseClassForTests = "com.thoughtworks.contract.ProductBaseTest"
}

...
```

## 在测试目录下创建基类测试文件

---

ProductBaseTest

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ContractApplication.class, webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@DirtiesContext
@AutoConfigureMessageVerifier
public abstract class ProductBaseTest {
    @Autowired
    private ProductController productController;

    @Before
    public void setup() {
        StandaloneMockMvcBuilder standaloneMockMvcBuilder = MockMvcBuilders.standaloneSetup(productController);
        RestAssuredMockMvc.standaloneSetup(standaloneMockMvcBuilder);
    }
}
```

这里需要注意将基类声明为abstract class，不这样做在运行测试时会对当前测试文件报错:`java.lang.Exception: No runnable methods`

## 创建stubs

---

在test的resources目录下创建contracts目录，并在其中添加stubs文件，编写stubs可以使用两种方式进行编写：yaml，groovy。

yaml

```yaml
name: "should_return_all_products_yaml"
request:
  url: /products
  method: GET
response:
  status: 200
  bodyFromFile: response.json
```

groovy

```groovy
import org.springframework.cloud.contract.spec.Contract

Contract.make {
    name "should_return_all_products_groovy"
    request {
        method GET()
        url("/products")
    }
    response {
        body(file("response.json"))
        status(200)
    }
}
```

注意到本文都是通过在contracts目录下创建对应的json文件，作为body的输入。

## 测试

---

完成代码编写后，通过运行./gradlew build即可自动进行契约测试，contract插件将自动在build/generated-test-sources目录下生成对应的测试文件，进行测试。



完整示例代码地址：https://github.com/FeiXie-Liam/contract-start-provider
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

## Provider端:

---

### 首先配置Contract相关依赖

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

如果项目中存在多个controller，需要分别对各个controller进行测试，则需要将contracts任务进行修改

```
contracts {
    packageWithBaseClasses = "com.thoughtworks.contract.provider"
}
```

然后创建相应实体名称的test基类(eg:ProductBase)，在test/resouces/contracts目录下创建不同实体的Dir储存不同实体的请求。

### 在测试目录下创建基类测试文件

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

### 创建stubs

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
  headers:
    Content-Type: application/json;charset=UTF-8
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

### 添加DB-Rider

---

添加DB-Rider使得在测试时，使用DB-Rider生成的数据库数据进行测试，防止原始数据库在测试阶段被修改。

首先添加依赖

```
    testCompile('com.github.database-rider:rider-spring:1.2.9') {
        exclude group: 'org.slf4j', module: 'slf4j-simple'
    }
```

在ProductBaseTest中添加DB-rider的相关注解

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ContractApplication.class, webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@DBRider
@ActiveProfiles("test")
@DBUnit(caseSensitiveTableNames = true)
@DataSet("product.yml")
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

然后在test/resources目录下的创建datasets文件夹，在里面使用yaml格式添加自定义的数据库数据

```yaml
product:
  - id: 1
    name: "苹果"
  - id: 2
    name: "笔记本电脑"
  - id: 3
    name: "电视机"
```

### nexus

---

为了使producer端生成的契约jar包和相关文件能够发布到共有仓库，以便consumer端能够调用，使用docker创建nexus的容器

```
docker pull sonatype/nexus
docker run -d -p 8081:8081 --name nexus sonatype/nexus
```

然后将生成的契约文件自动部署到nexus的容器中，在进行自动部署时，需要在build.gradle中配置依赖

```
...
apply plugin: 'maven-publish'
...
publishing {
    repositories {
        maven {
            url 'http://localhost:8081/nexus/content/repositories/snapshots/'
            credentials {
                username = 'admin'
                password = 'admin123'
            }
        }
    }
}
```

### 测试

---

完成代码编写后，通过运行./gradlew build即可自动进行契约测试，contract插件将自动在build/generated-test-sources目录下生成对应的测试文件，进行测试。

### 发布

---

运行./gradlew publishing将build生成的stubs自动发布到nexus上，供consumer端进行调用。

完整示例代码地址：https://github.com/FeiXie-Liam/contract-start-provider

## consumer端

---

### 配置相关依赖

---

```
    testCompile ('org.springframework.cloud:spring-cloud-starter-contract-stub-runner')

```

### 添加nexus配置

---

在测试环境的application-test.yml文件中添加配置：

```yaml
stubrunner:
  ids: com.thoughtworks:contract:+:stubs:8998
  repositoryRoot: http://localhost:8081/nexus/content/repositories/snapshots/
```

需要注意的是在一台电脑上运行provider和consumer服务时，当provider端已经通过某个端口(eg.8090)运行起来时，如果consumer端想要进行契约测试，stubrunner的ids端口不能与provider端的端口相同，否则测试程序会报错，需要将其指定为未被占用的端口。

### 编写测试文件

---

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = ConsumerApplication.class)
@AutoConfigureStubRunner(stubsMode = StubRunnerProperties.StubsMode.REMOTE)
@ActiveProfiles("test")
public class ConsumerTest {

    @Autowired
    ProductService productService;

    @Test
    public void should_return_all_products() {
        //given

        //when
        List<Product> actual = productService.getAll();
        //then
        ...
    }
}
```

注意在测试文件的类名上添加stub runner的注解`@AutoConfigureStubRunner(stubsMode = StubRunnerProperties.StubsMode.LOCAL)`stubsMode包含LOCAL, CLASSPATH, REMOTE三种方式。

- LOCAL:线下仓库地址 eg. localhost
- CLASSPATH:项目目录下的问题
- REMOTE:远程仓库的地址 eg.亚马逊云

完整代码示例：https://github.com/FeiXie-Liam/contract-start-consumer
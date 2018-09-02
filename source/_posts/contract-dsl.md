---
title: contract dsl
date: 2018-08-31 22:37:06
tags:
 - contract
 - groovy
 - yaml
categories:
 - 后端

---

## 基本属性

---

### name

---

指定当前contract文件生成的测试名称，如果不指定name，默认为文件名。

例如文件名为someTest，默认测试名为：validate_someTest

<!--more-->

groovy:`name(some_test)` 

yaml:`name: ...`

测试名为:validate_some_test

### description

---

指定当前contract文件的描述，具体使用场景尚不明确。

groovy:`description(...)`

yaml:`description: ...`

### Ignore

---

指定当前contract文件被忽略，跳过stub和test文件的生成

groovy:`ignore()`

yaml:`ignore: true`

### Request & Response

---

在HTTP协议中Request部分仅要求method和url为必要部分，当consumer端调用request时，需要对request部分的请求进行匹配，匹配得上才会启用该stub。

Response部分则至少需要包含HTTP状态码部分。一个具体的Request & Response示例代码如下：

groovy:

```groovy
org.springframework.cloud.contract.spec.Contract.make {
    request {
        method 'GET'

        // Specifying `url` and `urlPath` in one contract is illegal.
        url('http://localhost:8888/foo')
    }

    response {
        status 200
    }
}
```

yaml:

```yaml
request:
	method: PUT
	urlPath: /foo
response:
	status: 200
```

## producer/consumer

---

在撰写契约时，对于request部分producer端生成的Test必须是具体的url(eg. product/1)，而在consumer端则期望是任意的某种结构下的url(eg. product/{{number}})。groovy dsl提供了动态属性来实现以上效果:

```groovy
value(consumer(...), producer(...))
value(c(...), p(...))
value(stub(...), test(...))
value(client(...), server(...))

$(consumer(...), producer(...))
$(c(...), p(...))
$(stub(...), test(...))
$(client(...), server(...))
```

## 正则表达式

---

为了达到匹配某个固定结构的语法，可以在契约文件中结合正则表达式，在contract dsl中除了可以自己通过`regex(...)`撰写正则表达式外，也自带了许多常用正则匹配方式，储存在RegexPatterns类中

```java
onlyAlphaUnicode()
alphaNumeric()
number()
anyBoolean()
email()
...
```

使用时，通过`byRegex(email())`类似的语法即可。

## bodyMatcher

---

bodyMatcher与producer/consumer的作用类似，是在request或response中，producer端和consumer端逻辑不一致时，动态调节契约的实现方案。其主要作用在与调节body的动态属性。语法如下所示：

```groovy
bodyMatchers {
    jsonPath('$.email', byRegex(email()))
    jsonPath('$.status', byType())
}
```

对于`$.email`语法的详细信息可以参考[JsonPath](https://feixie-liam.github.io/2018/08/29/JsonPath/)。

## 综合示例

---

撰写的契约文件如下所示：

```groovy
import org.springframework.cloud.contract.spec.Contract

Contract.make {
    request {
        method "post"
        url value(producer("/api/email-reset/users/2")
                , consumer(regex("/api/email-reset/users/\\d+")))
        body("""
                {
                    "email":"123@qq.com"
                }
            """
        )
        bodyMatchers {
            jsonPath('$.email', byRegex(email()))
        }
        headers {
            header("Content-Type", "application/json;charset=UTF-8")
        }
    }
    response {
        status 201
        body("""
            {
                "email":"123456789@qq.com",
                "status":"PENDING"
            }
        """)
        bodyMatchers {
            jsonPath('$.email', byRegex(email()))
            jsonPath('$.status', byType())
        }
        headers {
            header("Content-Type", "application/json;charset=UTF-8")
        }
    }
}

```

对应生成的Producer端的测试：

```java
@Test
	public void validate_createEmailResetRequest() throws Exception {
		// given:
			MockMvcRequestSpecification request = given()
					.header("Content-Type", "application/json;charset=UTF-8")
					.body("{\"email\":\"123@qq.com\"}");

		// when:
			ResponseOptions response = given().spec(request)
					.post("/api/email-reset/users/2");

		// then:
			assertThat(response.statusCode()).isEqualTo(201);
			assertThat(response.header("Content-Type")).isEqualTo("application/json;charset=UTF-8");
		// and:
			DocumentContext parsedJson = JsonPath.parse(response.getBody().asString());
		// and:
			assertThat(parsedJson.read("$.email", String.class)).matches("[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,6}");
			assertThat((Object) parsedJson.read("$.status")).isInstanceOf(java.lang.String.class);
	}
```

用于consumer端调用的stubs mapping文件如下所示：

```json
{
  "id" : "85d59d1e-5823-4093-be4d-1169085aa177",
  "request" : {
    "urlPattern" : "/api/email-reset/users/\\d+",
    "method" : "post",
    "headers" : {
      "Content-Type" : {
        "equalTo" : "application/json;charset=UTF-8"
      }
    },
    "bodyPatterns" : [ {
      "matchesJsonPath" : "$[?(@.email =~ /([a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,6})/)]"
    } ]
  },
  "response" : {
    "status" : 201,
    "body" : "{\"email\":\"123456789@qq.com\",\"status\":\"PENDING\"}",
    "headers" : {
      "Content-Type" : "application/json;charset=UTF-8"
    },
    "transformers" : [ "response-template" ]
  },
  "uuid" : "85d59d1e-5823-4093-be4d-1169085aa177"
}
```

可以看到契约自动生成的文件，针对于producer端和consumer端的url以及body是动态变化的。
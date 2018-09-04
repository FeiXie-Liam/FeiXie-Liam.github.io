---
title:Spring Annotation
date: 2018-09-04 21:20:01
tags:
 - annotation
 - spring
categories:
 - 后端
---

## @Target

---

描述注解的使用范围，超出范围时编译失败。

类型：

| 取值类型(element_type) | 描述                               |
| ---------------------- | ---------------------------------- |
| CONSTRUCTOR            | 描述构造器                         |
| FIELD                  | 描述域（变量）                     |
| LOCAL_VARIABLE         | 描述局部变量                       |
| METHOD                 | 描述方法                           |
| PACKAGE                | 描述包                             |
| PARAMETER              | 描述参数                           |
| TYPE                   | 描述类、接口（包括注解类型）或enum |

## @Retention

---

描述注释的生命周期，注解的生效范围。

| 取值范围（retention_type） | 描述                                    |
| -------------------------- | --------------------------------------- |
| SOURCE                     | 在源文件中生效，在class文件将去除       |
| CLASS                      | 在class文件生效，运行时无法获取         |
| RUNTIME                    | 运行时通过反射机制获取，保留在class文件 |

## 其他

---

@Documented —— javac生成API时显示注解信息

@Inherited —— 表明该注解可以由子类继承，并且子类可以继承父类注解。默认情况下子类不继承父类的注解。

## 自定义Controller参数注解

---

根据上述内容，首先定义注解类型

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Auth {
}

```

然后注册配置项

```java
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> argumentResolvers) {
        argumentResolvers.add(new AuthResolver());
    }
}
```

定义注解的解析实现：

```java
public class AuthResolver implements HandlerMethodArgumentResolver {

    public boolean supportsParameter(MethodParameter parameter) {
        return parameter.getParameterAnnotation(Auth.class) != null;
    }

    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest,
                                  WebDataBinderFactory binderFactory) {
        String username = webRequest.getHeader("username");
        String roleStr = webRequest.getHeader("roles");
        String id = webRequest.getHeader("id");
        User current = new User();
        if(roleStr == null || "".equals(roleStr)) {
            roleStr = "0";
        }

        List<Integer> roles = Arrays.stream(roleStr.split(","))
                .map(item-> Integer.valueOf(item))
                .collect(Collectors.toList());
        current.setUsername(username);
        current.setRoles(roles);
        if (id != null) {
            current.setId(Long.valueOf(id));
        }
        return current;
    }
}
```


---
title: spring boot 版本迁移中的坑
date: 2018-08-22 22:21:40
tags:

---

# Spring Boot 进行版本迁移中的坑

本文记录了在将项目从spring boot从1.5版本迁移到2.0.4版本中遇到的问题。

1. 在`build.gradle`中，首先需要将springBootVersion指定为2.x版本，并加入maven { url 'https://repo.spring.io/libs-snapshot' }的路径。

2. 对应的springCloudVersion也需要改为兼容2.x版本的`Finchley.SR1`，否则会报错。添加方式如下所示:

   ```gradle
   ext {
       springCloudVersion = 'Finchley.SR1'
   }
   ...
   dependencyManagement {
       imports {
           mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
       }
   }
   ```

3. 在迁移过程中，加入以下依赖可以在启动过程中提示application.yml中被弃用的方法，注意在迁移结束后删除该部分依赖。

   ```gradle
       compile('org.springframework.boot:spring-boot-properties-migrator')
       runtime("org.springframework.boot:spring-boot-properties-migrator")
   
   ```

4. 使用Cloud相关服务时，添加依赖的内容路径有变化，应该使用`compile('org.springframework.cloud:spring-cloud-starter-openfeign')`类似格式。与此同时代码中引用FeignClient时的路径也会变化。

5. 在新版本的Lombok中，@Data注解默认启用了private的无参构造函数，如果在@Data后面添加@NoArgsConstructor会报错构造函数已经被创建，需要将@NoArgsConstructor放在@Data之前，使用public的无参构造函数覆盖@Data的私有无参构造函数。

6. 在新版JPA中，JpaRepository与CrudRepository中的方法进行了大量改动，新版本删除了许多原有重复方法，使用时需要注意查看，以便正确调用接口。

7. 类似的，AuditorAware类中的方法返回类型也有改变，需要注意自定义重载方法的返回类型是否与其一致。

8. 在存在@Builder注解构造函数式，慎用@Data，尽量使用@Getter，@Setter否则容易出错。

9. 新版actuator在配置页面路径时，相应的配置路径进行了修改，需要注意更新，比如:

   

   ```yaml
   old:
   
   management:
     context-path: /system
     
     
   new:
   
   management:
   	server:
       	servlet:
         		context-path: /system
     	endpoints:
       	web:
         		base-path: /
   ```

10. 切换到新版本启动应用时，出现`org.hibernate.LazyInitializationException - could not initialize proxy - no Session`错误时，主要原因在于lazy机制导致数据库访问时，操作session已经关闭且释放。解决方案为在配置文件中加入`spring.jpa.properties.hibernate.enable_lazy_load_no_trans=true`

11. 启动应用时，出现`Schema-validation: missing table [hibernate_sequence]`错误，主要原因在与使用@GenerateType注解时，id的自动生成策略会默认通过数据库中的hibernate_sequence表，自动生成id，不存在该数据库则会报错，解决方案为在yml配置文件中添加`spring.jpa.properties.hibernate.id.new_generator_mappings=false`配置。

12. 在新版本中，出于安全性考虑，取消了management.security.enabled属性和security.basic.enabled属性，默认全局需要通过验证才能访问资源，需要通过WebSecurityConfigurerAdapter进行配置。

13. flyway在新版本中需要修改为5.x版本，如果使用plugin的话，需要在classpath中添加： 

    ```
    buildscript {
        dependencies {
    classpath("org.flywaydb.flyway:org.flywaydb.flyway.gradle.plugin:5.0.7")
    	}
    }
    
    apply plugin: "org.flywaydb.flyway"
    
    ```

14. 使用@ConfigurationProperties注解时，prefix属性不能使用驼峰命名法，需要在单词之间加入'-'分割，并全使用小写。

15. 在使用新版本JPA中的`T getOne(ID id);`方法时，如果根据id无法找到对应的实体对象，则会直接抛出异常错误，在以前的版本则会返回一个null对象

16. 当使用Optional包裹对象时，如果对象本身可能会是null，则使用Optional.ofNullable(),否则使用Optional.of()，如果胡乱使用可能会导致异常比如重载`AuditorAware`类的`getCurrentUser()`方法时，新版本需要返回Optional<T>对象，不使用Optional.ofNullable()则可能导致NullPointerException异常。

17. 新版JPA中repository的自带方法中，Pageable参数不能为null，老版本则无此限制。


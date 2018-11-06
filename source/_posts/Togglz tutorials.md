---
title: Togglz tutorials
date: 2018-11-05 08:37:08
tags:
 - togglz
 - java
 - spring
categories:
 - 后端

---

## Togglz

------

### install dependence

---

#### for spring MVC development:

build.gradle

> compile 'org.togglz:togglz-servlet:2.6.1.Final'
> compile 'org.togglz:togglz-spring-web:2.6.1.Final'
> runtime 'org.togglz:togglz-jsp:2.6.1.Final'
> compile 'org.togglz:togglz-console:2.6.1.Final'

<!--more-->

#### for spring boot development:

build.gradle

##### 2.x:

> compile("org.togglz:togglz-spring-boot-starter:2.6.1.Final")

##### 1.x:

> compile("org.togglz:togglz-legacy-spring-boot-starter:2.6.1.Final")

***note***: *The starter will not only add the `togglz-core` and `togglz-spring-core` modules to your project, it will also enable the auto configuration of the Togglz configration.*

### Configuration

---

Configuring Togglz requires you to implement two classes.

#### Feature definition

 The first class is the feature enum which declares the features you want to manage with Togglz. This enum is a standard Java enum which implements the `Feature` interface.

```java
import org.togglz.core.Feature;
import org.togglz.core.annotation.EnabledByDefault;
import org.togglz.core.annotation.Label;
import org.togglz.core.context.FeatureContext;

public enum MyFeatures implements Feature {
    @Label("First Feature")
    FEATURE_ONE,
    
    @Label("Second Feature")
    FEATURE_TWO;
    
    public boolean isActive() {
        return FeatureContext.getFeatureManager().isActive(this);
    }   
}
```

#### Implementing TogglzConfig

The next step is to configure the `FeatureManager` which is the central Togglz component that manages the state of your features. To do so, you have to create a class implementing the `TogglzConfig`.

```java
import java.io.File;

import org.togglz.core.Feature;
import org.togglz.core.manager.TogglzConfig;
import org.togglz.core.repository.StateRepository;
import org.togglz.core.repository.file.FileBasedStateRepository;
import org.togglz.core.user.FeatureUser;
import org.togglz.core.user.UserProvider;
import org.togglz.servlet.user.ServletUserProvider

@Component
public class MyTogglzConfiguration implements TogglzConfig {
	@Override
    public Class<? extends Feature> getFeatureClass() {
        return MyFeatures.class;
    }

    @Override
    public StateRepository getStateRepository() {
        return new FileBasedStateRepository(new File("features.properties"));
    }

   @Override
    public UserProvider getUserProvider() {
        return () -> new SimpleFeatureUser("admin", true);
    }


}
```

**Note**:*the relative path is direct to the project path(eg. /twu63team2)*

features.properties

```properties
FEATURE_TWO=false
FEATURE_ONE=false
```

**Important Note**: *if you use spring security, it will use **csrf** protection which cause that you can not use togglz admin console to edit the status of feature.*

**one way** to solve that problem is to **disable the csrf**.(not recommend)

security-app-context.xml:

```xml
<http>
    <csrf disabled="true"/>
</http>
```

**another way** is to import one more dependence in build.gradle

>compile 'org.togglz:togglz-spring-security:2.6.1.Final'

once you import this dependence, when you edit the toggle state in admin console, It will add a csrf token, and it will work!

### Usage

---

#### Java

```java
if( MyFeatures.FEATURE_ONE.isActive() ) {
  // new stuff here
}
```

#### JSP

```html
<%@ taglib uri="http://togglz.org/taglib" prefix="togglz" %>


This is some text that is always shown.

<togglz:feature name="FEATURE_ONE">
This is the text of the TEXT feature.
</togglz:feature>
```

### Test with togglz

---

if you use junit for test, you need to add test dependence for togglz in build.gradle

> testCompile 'org.togglz:togglz-junit:2.6.1.Final'

#### TogglzRule

The first important feature of the JUnit integration module is the `TogglzRule`. This rule allows to modify the feature state at runtime. This is especially useful if you want to test a special combination of feature states.

```java
public class SomeJunitTest {

  @Rule
  public TogglzRule togglzRule = TogglzRule.allEnabled(MyFeatures.class);

  @Test
  public void testToggleFeature() {

    // all features are active by default  
    assertTrue(MyFeatures.FEATURE_ONE.isActive());

    // you can easily modify the feature state using the TogglzRule
    togglzRule.disable(MyFeatures.FEATURE_ONE);
    assertFalse(MyFeatures.FEATURE_ONE.isActive());

  }

}
```

#### Feature variations

The JUnit integration module also allows to run tests with different combination of feature states. This works very similar to JUnit's `@Parameterized` annotation.

```java
@RunWith(FeatureVariations.class)
public class FeatureVariationsTest {

    @Variations
    public static VariationSet<MyFeatures> getPermutations() {
      return VariationSetBuilder.create(MyFeatures.class)
              .enable(MyFeatures.F1)
              .vary(MyFeatures.F2)
              .vary(MyFeatures.F3);
    }

    // will be executed 4 times
    @Test
    public void test() {
      assertTrue(MyFeatures.F1.isActive());
      assertTrue(MyFeatures.F2.isActive() || !MyFeatures.F2.isActive());
      assertTrue(MyFeatures.F3.isActive() || !MyFeatures.F3.isActive());
    }

}
```

- F1=on, F2=off, F3=off
- F1=on, F2=on, F3=off
- F1=on, F2=off, F3=on
- F1=on, F2=on, F3=on
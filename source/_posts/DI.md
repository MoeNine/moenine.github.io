---
title: 'Spring三种注入方法:@Autowired,@Resource和@Inject'
date: 2021-01-12 11:51:26
tags: Spring
---



## 描述

本篇将详细的讲述使用注解声明式来解决依赖关系的三个注解@Autowired,@Resource和@Inject



举个例子:

```java
@Autowired
ArbitraryClass arbObject;
```

这是使用注解的方式，相反的，如果我们使用直接将类实例化的方式就像下面这样:

```java
ArbitraryClass arbObject = new ArbitraryClass();
```



三个注解中的两个是属于Java的扩展包里， *javax.annotation.Resource* 和 *javax.inject.Inject*

另一个则是由Spring Framework提供的 *org.springframework.beans.factory.annotation*



这些注解通过字段注入(Field Injection)或setter注入来解决依赖问题，通过分析这三个注解的执行路径，使用一个简单有效的例子去演示它们之间的区别，例子将重点介绍如何在集成测试(Integration Testing)中使用这些注解，测试所需的依赖关系可以是任意文件或任意类。



## @Resource

@Resource是Jakarta EE中JSR-250注解集里的，这个注解的执行顺序如下：

<ol>
	<li>匹配类名</li>
  <li>匹配类型</li>
  <li>通过匹配@Qualifier</li>
</ol>

不管是在Setter或字段注入中，都是这个执行顺序。

### 字段注入

使用@Resource注解以字段注入的方式来解决依赖问题

#### 类名匹配

接下来的这个集成测试的例子演示按照名字匹配的字段注入

```java
@SpringBootTest
public class FieldResourceInjectionIntegrationTest {

    @Resource(name="namedFile")
    private File defaultFile;

    @Test
    public void givenResourceAnnotation_WhenOnField_ThenDependencyValid(){
        Assertions.assertNotNull(defaultFile);
        Assertions.assertEquals("namedFile.txt", defaultFile.getName());
    }

}
```

在第四行，我们使用了@Resource(name="")指定了名字，将名为namedFile的bean注入到了defaultFile变量中。

```java
    @Resource(name="namedFile")
    private File defaultFile;
```



这个name变量使注解采用了按照名字匹配的方式，namedFile这个Bean需要在*ApplicationContextTestResourceNameType*里被定义

注意：name的值必须与需要注入的Bean的名字(id)相对应

```java
@Configuration
public class ApplicationContextTestResourceNameType {

    @Bean(name="namedFile")
    public File namedFile() {
        File namedFile = new File("namedFile.txt");
        return namedFile;
    }
}
```

当我们没有定义bean到ApplicationContext中，会导致抛出*org.springframework.beans.factory.NoSuchBeanDefinitionException*

这可以通过更改*ApplicationContextTestResourceNameType* 传递到@Bean中的属性值或更改@Resource中的name属性来证明。

```java
Injection of resource dependencies failed; 

nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException:

No bean named 'namedFil' available
```



#### 类型匹配

为了演示类型匹配，我们可以直接删除*FieldResourceInjectionIntegrationTest* 里第四行@Resource的参数

```java
@Resource
private File defaultFile;
```

然后再次启动测试，测试依然会通过，因为当@Resource没有接受name参数时，Spring Framework因为要尝试解析依赖，会进行下一个优先级，使用类型匹配，



#### 使用@Qualifier匹配

在*ApplicationContextTestResourceNameType* 中 再加入一个Bean

```java
@Configuration
public class ApplicationContextTestResourceQualifier {

    @Bean(name="defaultFile")
    public File defaultFile() {
        File defaultFile = new File("defaultFile.txt");
        return defaultFile;
    }

    @Bean(name="namedFile")
    public File namedFile() {
        File namedFile = new File("namedFile.txt");
        return namedFile;
    }
}
```

这次我们使用*QualifierResourceInjectionTest* 来演示，在这个场景中，我们需要将指定的Bean注入到对应所需依赖变量里

```java
@SpringBootTest
public class QualifierResourceInjectionIntegrationTest {
    @Resource
    private File dependency1;

    @Resource
    private File dependency2;

    @Test
    public void givenResourceAnnotation_WhenField_ThenDependency1Valid(){
        Assertions.assertNotNull(dependency1);
        Assertions.assertEquals("defaultFile.txt", dependency1.getName());
    }

    @Test
    public void givenResourceQualifier_WhenField_ThenDependency2Valid(){
        Assertions.assertNotNull(dependency2);
        Assertions.assertEquals("namedFile.txt", dependency2.getName());
    }
}
```

接着我们运行测试，会发现抛出了*org.springframework.beans.factory.NoUniqueBeanDefinitionException* 

```java
Injection of resource dependencies failed; 

nested exception is org.springframework.beans.factory.NoUniqueBeanDefinitionException:

No qualifying bean of type 'java.io.File' available:

expected single matching bean but found 2: defaultFile,namedFile
```

这是因为ApplicationContext找到了两个定义为File类型的Bean，不知道该使用哪一个Bean来解决依赖

为了解决这个问题,我们在*QualifierResourceInjectionTest* 中添加以下代码

```java
@Resource
@Qualifier("defaultFile")
private File dependency1;

@Resource
@Qualifier("namedFile")
private File dependency2;
```

这时我们再进行测试，就没有问题了，这个测试的目的是为了展示当有多个bean在一个ApplicationContext中定义时，@Qualifier注解可以直接将指定的依赖注入。



###  Setter注入

基于Setter方法的注入

#### 类名匹配

唯一的区别就是*MethodResourceInjectionTest* 有一个Setter方法

```java
@SpringBootTest
public class MethodResourceInjectionIntegrationTest {
    private File defaultFile;

    @Resource(name="namedFile")
    protected void setDefaultFile(File defaultFile) {
        this.defaultFile = defaultFile;
    }

    @Test
    public void givenResourceAnnotation_WhenSetter_ThenDependencyValid(){
        Assertions.assertNotNull(defaultFile);
        Assertions.assertEquals("namedFile.txt", defaultFile.getName());
    }
}

```

通过对一个变量对应的Setter方法加一个注释来实现依赖注入,@Resource将Bean通过Setter参数传递给变量

```java
private File defaultFile;

@Resource(name="namedFile")
protected void setDefaultFile(File defaultFile) {
    this.defaultFile = defaultFile;
}
```

在这个例子中，我们依然使用namedFile这个Bean，@Resource的name参数必须与Bean名相对应

再次运行测试毫无疑问可以通过




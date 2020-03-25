# Spring Boot
Spring Boot 是基于自Spring4开始有的条件注册的一套快速开发整合包。

## Spring Boot的优点

- 集成了多种默认配置，提高了开发效率。
- 使用依赖注解方式的`JavaConfig`可以避免复杂的XML配置。
- 内置嵌入式HTTP服务器，可以轻松地开发和测试Web程序。

## 什么是 Spring Boot Starter
Spring Boot Starter是一个服务于特定功能并处理好依赖关系的库集合，可直接引入使用。

## Spring Boot 的自动配置是如何实现的

- 对在main函数里调用了`SpringApplication.run()`的`XXX`class解析其注解识别`@SpringBootApplication`然后对指定包(默认是与`XXX`class同级的包)中的类进行解析，并注册Bean到IOC容器中。
- 根据`@SpringBootApplication`里包含的三个注解`@SpringBootConfiguration`、`@EnableAutoConfiguration`、`@ComponentScan`进行深度解析。
- `@EnableAutoConfiguration`注解通过Spring 提供的 `@Import` 注解导入了`AutoConfigurationImportSelector`类（`@Import` 注解可以导入配置类或者Bean到当前类中）。`AutoConfigurationImportSelector`类中`getCandidateConfigurations`方法会将所有自动配置类的信息以 List 的形式返回。这些配置信息会被 Spring 容器作 bean 来管理。
```java
// AutoConfigurationImportSelector class
selectImports(AnnotationMetadata annotationMetadata)
-> List<String> configurations = getCandidateConfigurations(annotationMetadata,attributes);
   -> List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(), getBeanClassLoader());
      -> loadSpringFactories(classLoader)
         -> Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
```
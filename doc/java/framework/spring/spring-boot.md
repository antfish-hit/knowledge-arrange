## 简介
Spring Boot 是基于自Spring4开始有的条件注册的一套快速开发整合包。
## 优点
- 集成了多种默认配置，提高了开发效率。
- 使用依赖注解方式的`JavaConfig`可以避免复杂的XML配置。
- 内置嵌入式HTTP服务器，可以轻松地开发和测试Web程序。

## 什么是 Spring Boot Starter
Spring Boot Starter是一个服务于特定功能并处理好依赖关系的库集合，可直接引入使用。

## Spring Boot 自动装配的原理
### 简短说明
Spring Boot通过`@EnableAutoConfiguration`开启自动装配，通过`SpringFactoriesLoader`最终加载`META-INF/spring.factories`中的自动配置类实现自动装配，自动配置类其实就是通过`@Conditional`按需加载的配置类，想要其生效必须引入`spring-boot-starter-xxx`包实现起步依赖。
### 详细说明
1. 对在main函数里调用了`SpringApplication.run()`的`XXX`class解析其注解识别`@SpringBootApplication`。
2. 根据`@SpringBootApplication`里包含的三个注解`@SpringBootConfiguration`、`@EnableAutoConfiguration`、`@ComponentScan`进行深度解析。
    1. `@Configuration`：允许在上下文中注册额外的bean或导入其他配置类
    2. `@ComponentScan`：扫描被`@Component`(`@Service`, `@Controller`)注解的bean，注解默认会扫描启动类所在的包下所有的类，可以自定义不扫描某些bean。
    3. `@EnableAutoConfiguration`：启用Spring Boot的自动装配机制
        1. 通过Spring 提供的 `@Import` 注解导入了`AutoConfigurationImportSelector`类（`@Import` 注解可以导入配置类或者Bean到当前类中）。
        2. `AutoConfigurationImportSelector`类实现了`ImportSelector`接口的`selectImports`方法，该方法主要用于**获取所有符合条件的类的全限定类名，这些类需要被加载到IoC容器中**。
            1. `selectImports`方法中调用`getAutoConfigurationEntry()`方法，加载自动配置类。
                1. 判断自动装配开关是否打开。默认`spring.boot.enableautoconfiguration=true`，可在`application.yml`或`application.properties`中设置。
                2. 获取`EnableAutoConfiguration`注解中的`exclude`和`excludeName`。
                3. 获取需要自动装配的所有配置类，读取所有Spring Boot Starter下的`META-INF/spring.factories`
                4. 根据配置类的注解`@ConditionalOnXXX`进行筛选，只注册满足条件的。
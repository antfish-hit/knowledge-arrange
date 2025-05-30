## 简介
Spring是一款开源的Java开发框架，旨在提高开发人员的开发效率以及系统的可维护性。
## 核心概念
### IoC
Spring IoC是Spring框架统一管理Bean的核心技术，所有定义的Bean都会由IoC容器统一创建并管理，本质是一个用户存储Bean对象的Map。
### AOP
AOP是基于动态代理，通常用于封装与业务无关的通用功能以减少系统重复，降低模块间耦合度，提高系统拓展性和可维护性的技术。
## Spring Bean
### 将一个类声明为Bean的注解有哪些
`@Component`/`@Repository`/`@Service`/`@Controller`/`@Bean`
### `@Component`和`@Bean`的区别是什么
`@Component`作用于类上，用于自动扫描，`@Bean`作用于方法上，用于手动注册。
### 注入Bean的注解有哪些
`@Autowired`/`@Resource`/`@Inject`
### `@Autowired`和`@Resource`的区别
`@Autowired`默认按类型注入，`@Resource`默认按类名（首字母小写）注入。
### 注入Bean的方式
- 构造器
- Setter方法
- 字段注入
### Bean的作用域
- singleton : 全局（ApplicationContext）单例bean
- prototype：每次获取bean都会创建新bean
- request：每次HTTP请求都会创建新bean，仅在当前request内有效（仅限Web）
- session：每次HTTP请求都会创建新bean，仅在当前session 内有效（仅限Web）
- application：全局（ServletContext）单例bean（仅限Web）
- websocket：每次WebSocket会话创建新bean（仅限Web）
### Spring 中的单例 bean 的线程安全问题解决方法

- 取决于其作用域和状态，protptype必定线程安全。
- 解决方法：在类中用ThreadLocal保存可变成员变量。
### Spring中Bean的生命周期
- 实例化
- 属性赋值
- 初始化
	- 如果实现了`Aware`接口，就调用相应的set方法。
	- 如果实现了`BeanPostProcessor` 接口，执行`postProcessBeforeInitialization()` 方法进行前置处理
	- 如果实现了`InitializingBean`接口，执行`afterPropertiesSet()`方法
	- 如果 配置了 `init-method`，执行对应的方法
	- 如果实现了`BeanPostProcessor`接口，执行`postProcessAfterInitialization()` 方法进行后置处理
- 销毁
	- 如果实现了`DisposableBean`接口，执行`destroy()`方法
	- 如果配置了`destroy-method`，执行对应的方法
![](_v_images/20200322152845526_10137.png)
### 循环依赖如何解决
- 通过使用三级缓存来解决
- 一级缓存存放已经完成实例化、属性填充、初始化的单例bean
- 二级缓存存放未属性填充的单例bean（可以防止AOP的情况下，每次获取bean都产生新的代理对象）
- 三级缓存存放用于实例化bean的`ObjectFactory`对象（`ObjectFactory`保存了刚实例化的bean）
- 如果发生循环依赖，就去三级缓存中拿到对应的`ObjectFactory`对象并调用`getObject`方法来获取循环依赖对象的前期暴露对象，并放到二级缓存中，这样在循环依赖时，就不会重复初始化了
- 涉及AOP的场景，`ObjectFactory`对象调用`getObject`方法拿到的可能是代理对象，如果没有二级缓存用来存放代理对象，会导致每次获取代理对象都是不一样的，保证不了代理对象的单例特性
- 三级缓存实际上可以理解为二级缓存，只不过第二级缓存又拆分成了二级缓存的组合，一级存完整的bean，二级存未初始的bean（原始bean/代理对象），三级存`ObjectFactory`（保存了刚实例化的bean，如果涉及AOP，则创建并返回代理对象）
## Spring AOP
### 常见通知类型
- `Before`（前置通知）：目标对象的方法调用之前触发
- `After`（后置通知）：目标对象的方法调用之后触发
- `AfterReturning`（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- `AfterThrowing`（异常通知）：目标对象的方法运行中抛出异常后触发，与`AfterReturning`互斥
- `Around`（环绕通知）：目标对象的方法调用前后都会触发
### 多个切面的执行顺序如何控制
通常使用`@Order`注解定义切面顺序
## SpringMVC
### 简介
参照MVC模式，将业务逻辑、数据和显示分离，并整合整个工作流程的框架。相比之前旧的Java MVC框架，使用更简单方便，开发效率更高，运行速度更快。
### 核心组件
- `DispatcherServlet`：核心的中央处理器，负责接收请求、分发，并给予客户端响应
- `HandlerMapping`：处理器映射器，根据请求的URL查找对应的`Handler`
- `HandlerAdapter`：处理器适配器，适配执行`HandlerMapping`找到的`Handler`
- `Handler`：请求处理器，一般指Controller
### 工作流程
- 客户端发送请求，`DispatcherServlet`拦截请求
- `DispatcherServlet`根据请求信息调用`HandlerMapping`，`HandlerMapping`根据请求URL查找对应的`Handler`
- `DispatcherServlet`调用`HandlerAdapter` 执行`Handler`
- `Handler`处理完请求返回model数据给`DispaterServlet`
- `DispaterServlet`把model转成JSON格式返回给客户端
### 统一异常处理方式
使用`ControllerAdvice`和`ExceptionHandler`两个注解统一处理异常
## Spring 事务
### 特性（ACID）
- 原子性（Atomicity）：事务最小，不可分割，动作要么全部完成，要不完全不起作用
- 一致性（Consistency）：执行事务前后，数据保持一致
- 隔离性（Isolation）：并发事务之间相互独立
- 持久性（Durability）：事务提交后，对数据库中数据的改变是持久的
### 脏读、幻读、不可重复读
- 脏读：一个事务读取了另一个事务未提交的数据
- 幻读：同一事务中前后分别执行相同的查询时，由于中间其他事务**插入**了新的数据，导致后续的查询返回了额外的数据。
- 不可重复读：同一事务中前后分别执行相同的查询时，由于中间其他事务**修改**了数据，导致后续的查询结果不同。
### 5种隔离级别
- `TransactionDefinition.ISOLATION_DEFAULT`: 使用后端数据库默认的隔离级别，Mysql 默认采用的REPEATABLE_READ隔离级别。
- `TransactionDefinition.ISOLATION_READ_UNCOMMITTED`: 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
- `TransactionDefinition.ISOLATION_READ_COMMITTED`: 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
- `TransactionDefinition.ISOLATION_REPEATABLE_READ`: 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- `TransactionDefinition.ISOLATION_SERIALIZABLE`: 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### 7种事务传播行为
- 支持当前事务的情况：
    - `TransactionDefinition.PROPAGATION_REQUIRED`： 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。
    - `TransactionDefinition.PROPAGATION_SUPPORTS`： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
    - `TransactionDefinition.PROPAGATION_MANDATORY`： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。
- 不支持当前事务的情况：
    - `TransactionDefinition.PROPAGATION_REQUIRES_NEW`： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
    - `TransactionDefinition.PROPAGATION_NOT_SUPPORTED`： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
    - `TransactionDefinition.PROPAGATION_NEVER`： 以非事务方式运行，如果当前存在事务，则抛出异常。
- 其他情况：
    - `TransactionDefinition.PROPAGATION_NESTED`： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于`TransactionDefinition.PROPAGATION_REQUIRED`。
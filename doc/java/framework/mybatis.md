## 简介
是一个半ORM数据库操作Java框架。
## 优缺点
- 优点：简单易上手，SQL灵活
- 缺点：数据库移植性差
## 使用场景
- 要求灵活SQL的场景。
- 对性能要求高或需求变化较多的项目。
## #{}和${}的区别

- #{}：预编译处理，Mybatis 在处理#{}时，会将 sql 中的#{}替换为?号，调用 `PreparedStatement` 的set 方法来赋值。
- ${}：字符串替换，会引入SQL注入问题。
## 简述 Mybatis 的插件运行原理，以及如何编写一个插件
Mybatis 仅可以编写针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这 4 种接口的插件。
Mybatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
实现 Mybatis 的 Interceptor 接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，还要在配置文件中配置编写的插件。
## Mybatis 是否支持延迟加载？如果支持，它的实现原理是什么
Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis 配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。
它的原理是，使用CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName()，拦截器 invoke()方法发现 a.getB()是 null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName()方法的调用。
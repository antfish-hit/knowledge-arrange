# MyBatis

## 定义
是一个半ORM数据库操作Java框架。

## 优缺点

- 优点：简单易上手，SQL灵活
- 缺点：数据库移植性差

## 使用场景

- 要求灵活SQL的场景。
- 对性能要求高或需求变化较多的项目。

## #{}和${}的区别是什么

- #{}：预编译处理，Mybatis 在处理#{}时，会将 sql 中的#{}替换为?号，调用 `PreparedStatement` 的set 方法来赋值。
- ${}：字符串替换，会引入SQL注入问题。

## Mybatis 是如何进行分页的？分页插件的原理是什么

Mybatis 使用 RowBounds 对象进行分页，它是针对 ResultSet 结果集执行的内存分页，而非物理分页，可以在 sql 内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。
分页插件的基本原理是使用 Mybatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 sql，然后重写 sql，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

## 简述 Mybatis 的插件运行原理，以及如何编写一个插件
Mybatis 仅可以编写针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor 这 4 种接口的插件，Mybatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke()方法，当然，只会拦截那些你指定需要拦截的方法。
实现 Mybatis 的 Interceptor 接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，还要在配置文件中配置编写的插件。

## Mybatis 是否支持延迟加载？如果支持，它的实现原理是什么
Mybatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载，association 指的就是一对一，collection 指的就是一对多查询。在 Mybatis 配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName()，拦截器 invoke()方法发现 a.getB()是 null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName()方法的调用。这就是延迟加载的基本原理。

## Mybatis 的一级、二级缓存

- 一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。
- 二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口(可用来保存对象的状态),可在它的映射文件中配置`<cache/>`。
- 对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了 C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。
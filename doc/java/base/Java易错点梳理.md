# Java易错点梳理

## 正确使用 equals 方法
应使用常量或确定有值的对象来调用equals或者使用`java.util.Objects#equals`，例：
```java
"string".equals(variable); //true
variable.equals("string"); //false
Objects.equals(variable, "string"); //true

// java.util.Objects#equals 源码
public static boolean equals(Object a, Object b) {
    // 可以避免空指针异常。如果a==null的话此时a.equals(b)就不会得到执行，避免出现空指针异常。
    return (a == b) || (a != null && a.equals(b));
}
```

## 整型包装类值的比较
当使用自动装箱方式创建一个Integer对象时，当数值在**-128 ~127**时，**会将创建的 Integer 对象缓存起来**，当下次再出现该数值时，**直接从缓存中取出对应的Integer对象**。所以不推荐用`==`比较引用类型，而是使用`equals`。

## 浮点数之间的等值判断
浮点数之间的比较不用`==`和`equals`来判断，推荐使用`BigDecimal`。在使用`BigDecimal`时，为了防止精度丢失，推荐使用它的`BigDecimal(String)`构造方法来创建对象。

## 基本数据类型与包装数据类型的选用

- 所有的 POJO 类属性必须使用包装数据类型。(数据库的查询结果可能是 null，因为自动拆箱，用基本数据类型接收有 NPE 风险。)
- RPC 方法的返回值和参数必须使用包装数据类型。(比如显示成交总额涨跌情况，即正负 x%，x 为基本数据类型，调用的 RPC 服务，调用不成功时，返回的是默认值，页面显示为 0%，这是不合理的，应该显示成中划线。)
- 所有的局部变量使用基本数据类型。

## 数组和集合的相关问题

- 数组转集合不能使用`Arrays.asList()`，因为返回类型`ArrayList`是`Arrays`下实现了`AbstractList`的内部类，并非常用的`java.util.ArrayList`，推荐使用转换方式：
    - `new ArrayList<>(Arrays.asList(...)`
    - Java 8 `Stream`：`Arrays.stream(myArray).collect(Collectors.toList())`
- `Collection.toArray()`是一个泛型方法：`<T> T[] toArray(T[] a)`，如果`toArray`方法中没有传递任何参数的话返回的是`Object`类型数组，由于JVM优化，`new T[0]`作为`Collection.toArray()`方法的参数现在使用更好，`new T[0]`就是起一个模板的作用，指定了返回数组的类型为`T`(此为原集合中元素的类型)，0是为了节省空间，因为它只是为了说明返回的类型。
- 不要在`foreach`循环里进行元素的`remove/add`操作，如果要进行`remove`操作，可以调用迭代器的`remove`方法。
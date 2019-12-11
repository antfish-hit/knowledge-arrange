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
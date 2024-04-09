# Java基础知识梳理
尽量以简练而精确的内容对Java基础知识进行再梳理。

## 面向对象的特征

   - 封装：隐藏具体实现，只对外提供**调用接口(函数)**。
   - 继承：代码复用。
   - 多态：在不影响原代码的情况下扩展程序以处理新情况的方法。核心是**动态绑定**（运行时确定类型，绑定对象）策略，即以实例的准确类型确定函数调用的绝对地址。

## 重载与重写的区别

   - 重载：方法名相同，参数列表不同。
   - 重写：方法名相同，参数列表也相同，返回值和抛出异常的范围小于父类，访问修饰符大于父类(private方法不能重写)。

## String为什么是不可变的
String类中使用final关键字修饰字符数组来保存字符串。

## StringBuffer和StringBuilder的区别
StringBuffer对方法加了同步锁，是线程安全的，而StringBuilder没有。

## hashCode()和equals()

   - equals返回true时，hashCode的返回值也必须相同 。
   - hashCode的返回值相同不代表equals返回true。
   - 重写了equals就必须重写hashCode()。

## 当try语句和finally语句中都有return语句时，忽略try中的return而执行finally的。

## 可以使用`transient`关键字阻止序列化变量。

## 浅拷贝和深拷贝的区别
  - 浅拷贝：基础类型复制值，引用类型复制引用中保存的内存地址
  - 深拷贝：基础类型复制值，引用类型复制引用指向的对象(其包含的所有数据)

## 为什么说 Java 语言“编译与解释并存”
因为 Java 语言既具有编译型语言的特征，也具有解释型语言的特征。由 Java 编写的程序需要先经过编译步骤，生成字节码（.class 文件），这种字节码必须由 Java 解释器来解释执行。
![](vx_images/562735214240450.png)

- JIT（Just in Time Compilation）编译器，属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。
-  AOT（Ahead of Time Compilation），这种编译模式会在程序被执行前就将其编译成机器码，属于静态编译。
    - 优点：
        - 避免了 JIT 预热等各方面的开销，可以提高 Java 程序的启动速度，避免预热时间长
        - 能减少内存占用和增强 Java 程序的安全性（AOT 编译后的代码不容易被反编译和修改），特别适合云原生场景
    - 缺点：
        - AOT 编译无法支持 Java 的一些动态特性，如反射、动态代理、动态加载、JNI（Java Native Interface）等

## references
- [Java基础常见面试题总结(上)](https://javaguide.cn/java/basis/java-basic-questions-01.html)
- [Java基础常见面试题总结(中)](https://javaguide.cn/java/basis/java-basic-questions-02.html)
- [Java基础常见面试题总结(下)](https://javaguide.cn/java/basis/java-basic-questions-03.html)
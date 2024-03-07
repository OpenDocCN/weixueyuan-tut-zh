# Java Number 类

Number 是一个抽象类，也是一个超类（即父类）。Number 类属于 java.lang 包，所有的包装类（如 Double、Float、Byte、Short、Integer 以及 Long)都是抽象类 Number 的子类。

Number 类定义了一些抽象方法，以各种不同数字格式返回对象的值。如 xxxValue() 方法，它将 Number 对象转换为 xxx 数据类型的值并返回，其中 doubleValue() 方法返回 double 类型的值，floatValue() 方法返回 float 类型的值。

抽象类不能直接实例化，而是必须实例化其具体的子类。如下代码演示了 Number 类的使用：

```
Number num=new Double(12.5);
System.out.println("返回 double 类型的值："+num.doubleValue());
System.out.println("返回 int 类型的值："+num.intValue());
System.out.println("返回 float 类型的值："+num.floatValue());
```

执行上述代码，输出结果如下：

```
返回 double 类型的值：12.5
返回 int 类型的值：12
返回 float 类型的值：12.5
```
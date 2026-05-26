[TOC]

# JAVA基础

## 核心内容

- 语法
- 面向对象
- String
- 异常
- 反射
- 注解

## 重点

- 面向对象三大特性
- String不可变性
- == 和 equals
- 接口和抽象类

## 1. 面向对象三大特性

### 封装

- 隐藏内部实现，只暴露必要接口
- `private`属性 + `public`方法

### 继承

- 子类复用父类代码，但只能单继承

### 多态

- 父类引用指向子类对象，运行时动态绑定
- 条件：继承 + 重写 + 向上转型

## 2. 重载 VS 重写

| 区别         | 重载     | 重写     |
| :------------ | :-------- | :-------- |
| **发生位置** | 同一个类 | 父子类   |
| **方法名**   | 相同     | 相同     |
| **参数**     | 必须不同 | 必须相同 |
| **返回类型** | 无关     | 必须兼容 |
| **绑定时机** | 编译时   | 运行时   |

## 3. 接口 VS 抽象类

### 相同

- 都不能实例化，都有抽象方法

### 不同

- 抽象类单继承，接口多实现
- 抽象类有构造器，接口没有
- 接口方法默认public，Java8 后可有 default/static 方法

## 4. String不可变性

- String底层是 `final char[]`，每次修改都创建新对象
- 拼接用StringBuilder（线程不安全但快）或StringBuffer（线程安全但慢）

## 5. == VS equals

### ==

- 基本类型比值，引用类型比内存地址

### equals

- 默认比地址，String/Integer 等重写为比内容

## 6. 深拷贝 VS 浅拷贝

### 浅拷贝

- 新对象，但内部引用类型仍指向同一个

### 深拷贝

- 完全复制，内部引用对象也是新的

### 实现

- Cloneable接口 + 重写clone()

## 7. 异常体系

### Throwable

- Error：不可恢复，如OOM
- Exception
  - 受检异常：必须try-catch，如IOException
  - 非受检异常/RuntimeException，可不处理，如NullPointerException

## 追问

### 1. 面向对象三大特性

#### 封装的目的是什么？

- 提高安全性：防止外部直接修改属性

- 降低耦合：内部变化不影响外部调用

#### Java为什么不能多继承？

 - 菱形继承问题
   - 如果两个父类有同名方法，子类不知道继承哪一个
   - 接口可以多实现是因为方法没有默认实现（Java8前）

#### ==多态的底层实现原理是什么？==

- JVM通过方法表。子类方法表中继承自父类的方法保持相同索引偏移量，运行时直接通过偏移量调用，不需要遍历查找

#### 编译时绑定和运行时绑定的区别？

- 编译时绑定
  - private/static/final方法、重载，编译期确定

- 运行时绑定
  - 重写的方法，运行时根据实际对象类型确定

### 2. 重载 VS 重写

#### ==返回值不同算重载吗？==

- 不算
- 重载只跟方法名 + 参数列表有关，返回值不同编译器无法区分

#### 为什么重写要求返回值类型必须兼容？

- 父类引用指向子类对象时，父类只知道返回类型是父类类型，如果子类返回不兼容的类型，调用方无法处理

#### 静态方法能重写吗？

- 不能
- 静态方法属于类，编译时绑定，叫方法隐藏而不是重写

### 3. 接口 VS 抽象类

#### ==Java8后接口可以有default方法，和抽象类有什么区别？==

- 接口不能有状态（实例变量），抽象类可以

- 接口多实现，抽象类单继承

- 接口默认方法冲突需要子类自己解决

#### 接口中可以构造方法吗？

- 不能
- 接口不是类，没有实例化过程

#### 什么时候用接口，什么时候用抽象类？

- 接口
  - 定义能力/规范，如Comprable

- 抽象类
  - 定义公共基础，如模板方法模式，共用的代码写抽象类里

### 4. String不可变性

#### ==String真的不可变吗？用反射能修改吗？==

- 能
- 反射获取value字段，setAccessible(true)，直接修改char数组内容
- 但不推荐，会破坏String的缓存机制（hashCode等）

#### 为什么StringBuilder比StringBuffer快？

- StringBuffer的每个方法都有synchronized，同步有性能开锁

#### 字符串拼接用 + 和StringBuilder的区别？

- 编译期优化`"a" + "b"`直接变成`"ab"`
- 循环内拼接用 + 会创建多个StringBuilder对象，循环外用手动创建更高效

### 5. == VS equals

#### 基本类型和包装类用==比较会怎么样？

- 包装类自动拆箱，比较的是值

- `Integer a = 127, b = 127;`
- `a == b`为true（缓冲池）
- `Integer c = 128, b = 128`
- `c == d`为false（超出-127~127范围）

#### hashCode和equals为什么要一起重写？

- HashMap中，两个对象equals为true但hashCode不同，会存在不同桶里，导致get找不到
- ==约定：equals相等必须hashCode相等==

#### 两个对象hashCode相等，equals一定相等吗？

- 不一定（哈希冲突）
- String`Aa`和`BB`的hashCode都是2112，但内容不同

### 6. 深拷贝 VS 浅拷贝

#### Cloneable接口是空接口，有什么作用？

- 标记接口
- 没有clone()方法的默认实现会抛异常，标记告诉JVM我可以用默认的浅拷贝

#### ==如何实现深拷贝？两种方式各有什么问题？==

- Cloneable方式
  - 每层都要实现clone，逐层调用，麻烦且易漏

- 序列化方式
  - 对象要实现Serializable，性能差，transient字段不会被拷贝

#### 推荐哪种深拷贝方式？

- 简单对象用构造器复制
- 复杂对象用序列化（JSON/Serialization）或拷贝库（如Apache Commons 的SerializationUtils）

### 7、异常体系

#### Error和Exception的区别？

- Error是系统级（OOM、StackOverflow），程序无法恢复
- Exception是程序级，可以处理

#### 运行时异常和非运行时异常分别举例？

- 运行时
  - NullPointer、ArrayIndexOutBounds、ArithmeticException

- 非运行时
  - IOException、SQLException、ClassNotFoundException

#### ==try-catch会影响性能吗？==

- 会，但不明显
- 真正影响性能的是异常的创建（fillInStackTrace耗时）
- 所以不要用异常做流程控制，也不要循环内new异常对象

#### finally块一定执行吗？

- 基本是
- 但如果try或finally中调用了System.exit()，或者线程被interrupt/杀死，不会执行
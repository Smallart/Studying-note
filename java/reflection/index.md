# 反射-[-[Reflection]-]

[-[Java]-]是一门静态类型（[-[static typing]-]）的语言。所谓的静态语言有两大特点：一、在使用变量之前，要声明变量的类型。二、静态类型的语言会在编译时执行类型检查。所以在编写代码时或是查看代码时，我们可以很清楚的知道某个变量是什么类型的，并且使用该类型包含的方法。但是有些时候我们需要对不知道类型的变量进行操作，而该对象的类型是在运行时确定的，此时我们就需要通过反射来完成我们的某些目的。

> 反射：[-[Java]-]程序的语言特性之一，允许运行中的Java程序获取自身的信息，并且可以操作类或对象的内部属性。

## 反射的应用场景

反射通常使用在程序运行时期对于应用程序进行**检查**和**修改**，也就是对程序的扩展性上有了提升。所以反射的主要应用场景有**通用框架**、**调试与测试工具**、[动态代理]()以及[注解]()。

反射的缺点：**性能开销**、**安全限制**和**破坏封装**。

* 性能开销：由于反射是在运行时才其进行操作，所以[-[JVM]-]对于代码编译期的优化是无法起到作用的。并且包含反射的执行代码要比不包含的要差，所以对于经常被调用的代码块是不推荐使用反射。
* 安全限制：反射所需要的运行时允许，这个权限不在security manager的管理之下。
* 破坏封装：反射可以访问类中的私有属性和方法，所以反射的使用可能会导致意想不到的副作用。

## 反射的使用
### [-[Classes]-]
对于每种类型的对象，[-[JVM]-]都会为其实例化所对应的[-[java.lang.Class]-]唯一实例`（一个类在JVM中有且仅有以仅有一个Class对象）`。这个[-[Class]-]提供了获得该类的成员和类型信息，并且能通过该[-[Class]-]实例对象创建该类型的实例化对象。也就是说该[-[Class]-]对象可以视为反射[-[API]-]的入口。

获得类Class对象的几种方法：[-[Object.getClass()]-]、[-[.class]-]语法、[-[Class.forName()]-]
* [-[Object.getClass()]-]:此方法是由引用类型的顶级父类Object来提供的，所以当有某个类的实例对象时可以调用该方法来获得该类的[-[Class]-]对象。
* [-[.class]-]语法：通过`类名.class`来获得该类的Class对象。
* [-[Class.forName]-]：如果知道某个类的全限定类名，我们也可以通过[-[Class]-]提供的静态方法[-[Class.forName()]-]来获得该类的[-[Class]-]实例，但是这个方法不能对基本类型使用。
* [-[Type]-]字段获得包装类型的[-[Class]-]对象。

```java
    // Object.getClass()
    Integer integer = new Integer(1);
    Class clazz1 = integer.getClass();
    // .class
    Class clazz2 = Integer.class;
    // Class.forName
    Class clazz3 = Class.forName("java.lang.Integer");
    // .Type
    Class clazz4 = Integer.TYPE;
```
#### 获取每个类修饰成分和类型

一个类可能会一个或是多个修饰语修饰，不同的修饰语会影响该类在运行时的行为。
* 权限修饰符：[-[public、protected]-]和[-[private]-]
* 一些作用范围在类上的修饰符：[-[abstract、static]-]和[-[final]-]
* 注解

```java
    clazz.getModifiers
```
### [-[Members]-]

> 在运行过程中，[-[Reflection API]-]将[-[Java]-]类中的各个成分映射成为了一个个[-[Java]-]对象

在反射API中，类中的各个成分被分为了两大基类[-[Member]-]与[-[Constructor]-]。其中类的字段（[-[Field]-]）和方法（[-[Method]-]）都继承自[-[Memeber]-]，而构造器类继承自[-[Constructor]-]。

#### Fields

对于类的字段来说，字段主要有类型和值的设定。所以相关[-[API]-]就是主要对获取类型信息和取值和赋值操作。
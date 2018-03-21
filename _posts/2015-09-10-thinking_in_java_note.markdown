---
author: shellbye
comments: true
date: 2015-09-10 10:27:08+00:00
layout: post
slug: thinking_in_java_note
title: Thinking in Java note
categories:
- tech_world
tags:
- java
- job
---

1. 汇编和C等语言的主要抽象仍要求在解决问题时要基于计算机的结构，而不是基于所要解决的问题的结构来考虑。程序员必须建立起在机器模型（位于“解空间”内，这是对问题建模的地方，比如计算机）和实际待解决模型（位于“问题空间”内，这个问题存在的地方，比如一项业务）之间的关联。建立这种关联是费力的，而且不属于编程语言所固有的功能。  

2. 访问控制  
－－－－－－－包外－－包内－－继承－－内部类  
public－－－－yes－－yes－－yes－－yes  
default－－－－no－－yes－－yes－－yes  
protected－－－no－－no－－yes－－yes  
private－－－－no－－no－－－no－－yes  

3. 转型（cast）  
将导出类（子类）看做是他的基类的过程称为向上转型（upcasting）  

4. 绑定  
绑定：将一个方法调用同一个方法主题关联起来  
前期绑定：函数调用在编译时就确定，多出现在非面向对象的编程中。  
后期绑定：被调用的代码到运行时才确定，之前只是编译器只是确定方法存在。（动态绑定，java中的默认行为）  
static和final方法是前期绑定，其他都是运行时绑定  
多态缺陷：  
a)"覆盖"私有方法（子类看不到父类的私有方法，导致错误覆盖）  
b)域（域访问由编译器解析）和静态方法（属于类而不是对象）无法多态  

5. 构造函数  
父类的构造函数总是在子类的构造过程中被调用，调用构造函数的顺序：  
1)调用基类构造函数  
2)按声明顺序调用成员的初始化方法  
3)调用子类构造函数的主题  

6. 斜变返回类型  
子类中的被覆盖方法可以返回父类方法返回类型的某种子类型  

7. note  
引用可以单独存在，不需要对象，比如null  
堆栈中：存储对象引用，基本类型  
堆中：存储new出来的java对象  
类中的基本数据类型的成员会自动初始化，但是局部变量却不会被初始化  
如果一个表达式以字符串起头，那么后续所有操作数都必须是字符串型（因为＋号对字符串进行了重载）  
窄化的思想，是减少其可调用的方法，使对象变的更纯粹、更简单。  
异常丢失：前一个异常还没有正常处理，就又抛出了新的异常  
String中的toLowerCase()和trim()等方法中，若对象没有变化，就返回原始对象，否则返回新的String  
对象数组和基本类型数组的唯一的区别就是对象数组保存的是引用，基本类型数组保存基本类型的值  

8. 迭代器  
迭代器的概念可以使使用容器的时候不必关心容器的类型，因为迭代器常被称为轻量级对象。迭代器统一了对容器的访问方式。  

9. 正则笔记：  
\s 空白符号 \S 非空白符  
\d 数字 \D 非数字  
\w 词字符 \W 非词字符  
^ 一行的起始  
$ 一行的结束  
\b 词的边界 \B 非词的边界  
\G 前一个匹配的结束  
----------贪婪型 懒惰型  
一个或零个X: X?  X??  
零个或多个X: X*  X*?  
一个或多个X: X+  X+?  
恰好n个X: X{n}  X{n}?  
至少n个X: X{n,} X{n,}?  
n到m个X: X{n,m} X{n,m}?  

10. 所有的类都是在对其第一次使用时，动态加载到JVM中的。当程序创建第一个对类的静态成员引用时，就会加载这个类。这个证明构造函数也是类的静态方法  

11. 为了使用类而做的准备工作实际包含三个步骤：  
1)加载：这是由类加载器执行的，该步骤将查找字节码，并从这次字节码中创建一个Class对象  
2)链接：在链接阶段将验证类中的字节码，为静态域分配存储空间，并且如果必须的话，将解析这个类创建的对其他类的引用  
3)初始化： 如果该类有超类，则对其初始化，执行静态初始化器，和静态初始化块  

12. 仅使用类字面量（ClassName.class）来获得对类的引用不会引发初始化。但是，为了产生Class的引用，Class.forName()立即就进行了初始化  

13. 反射  
Class类与java.lang.reflect类库一起对反射的概念进行了支持，该类库包含了Field、Method以及Constructor类（均实现了Member接口）。这些类型的对象是由JVM在运行时创建的，用以表示未知类里的成员。这样就可以用Contructor创建对象，用get和set方法读取和修改与Field对象关联的字段，用invoke()方法调用与Method对象关联的方法。另外，还可以调用getFields(),getMethods()和getConstructors()等很便利的方法，以返回表示字段、方法已经构造函数的对象的数组。  
RTTI(Run Time Type Identification)和反射之间的区别只在于：对RTTI来说，编译器在编译时打开和检查.class文件；对于反射机制来说，.class文件在编译时是不可获取的，所以是在运行时打开和检查.class文件。  

14. 长度的获取  
数组的长度是通过获取成员变量length获得；  
List等容器用size()方法获得；  
String用length()方法获得；  

15. I/O note  
InputStream用来表示从不同数组源产生输入的类  
FilterInputStream和FilterOutPutStream是用来提供装饰器接口以控制特定输入流(InputStream)和输出流(OutputStream)的两个类。这两个类分别自I/O类库中的基类InputStreamOutputStream派生而来，这两个类是装饰器的必要条件  
nio对比io速度的提高来自于所使用的结构更接近于操作系统执行I/O的方式：通道和缓冲器。（ByteBuffer）  

16. 枚举(enum)  
ordonal()方法返回一个int值，这是每个enum实例在声明时的次序，从0开始。可以使用＝＝来比较enum实例，编译器会自动为你提供equals()和hashCode()方法。Enum类实现了Comparable接口，还实现了Serialization接口。enum是一个类，可以添加方法，或覆盖。enum的values()方法是由编译器添加的static方法。  

17. 元注解  
@Target用来定义注解将应用于什么地方(方法，域)  
@Rectetion用来定义该注解在哪一个级别可用(源代码，类文件，运行时)  
@Documented将此注解包含在Javadoc中  
@Inherited允许子类继承父类中的注解  
有注解，就得有注解处理器  

18. 并发  
从性能的角度看，如果没有任务会阻塞，那么在单处理器的机器上使用并发就没有任何意义  
java的线程机制是抢占式的，对应的是协作式的，每个任务会自动地放弃控制，需要让步语句  
Thread().yield()是对线程调度器的一种建议，表明可以被抢占  
Executor在客户端和任务执行之间提供了一个间接层；与客户端直接执行任务不同，这个中介对象将执行任务。
`ExecutorService exec = Executors.newCachedThreadPool();  
//....  
exec.shutdown();  
`
对shutdown的调用可以防止新任务被提交给这个Executor。  
TimeUnit.MILLISSECOND.sleep(1000);// 更简洁的睡眠  

19. java容器类的简化图：  
![full_container_taxonomy_thinking_in_java](/assets/full_container_taxonomy_thinking_in_java.png)  

20. java conllections简化图：
![collclasses](/assets/collclasses.png)  

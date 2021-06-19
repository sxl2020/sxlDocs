## Java中如何自定义一个注解？

Java注解与Java配置文件是Java程序开发中进行配置的两种方式。使用注解，可以使得注解代码化。

本文将学习SpringBoot的过程中遇到的一些底层注解进行积累和探究，形成了本文。

>在学习SpringBoot的自动配置过程中，了解到其关键在主配置类注解@SpringBootApplication
>
>本文从这个组合注解开始。

发现，该注解有以下多个注解组合而成

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

在正式探究上述几个注解之前，我产生了一个疑问：如何自定义一个注解？



### 基础知识

注解是Java 1.5引入的，Java自定义注解是通过运行时靠反射获取注解，注解相当于是一种嵌入在程序中的元数据，可以使用注解解析工具或编译器对其进行解析，也可以指定注解在编译期或运行期有效。在学习自定义注解，涉及了以下四个知识点：**元数据**、**注解的分类**、**内置注解**、**自定义注解**。

-------

#### 元数据 metadata

元数据从metadata一词译来，就是“关于数据的数据”的意思，即描述数据的结构信息。更通俗一点，就是**描述代码间关系，或者代码与其他资源（例如数据库表）之间内在联系的数据**。

- 元数据应用举例

在Java中元数据以标签的形式存在于Java代码中，元数据标签的存在并不影响程序代码的编译和执行，被用来生成其它的文件或只在运行时知道被运行代码的描述信息。在一些技术框架，如struts、hibernate就不知不觉用到了元数据。对struts来说，元数据指的是struts-config.xml；对hibernate来说就是hbm文件。

以上阐述的几种元数据都是基于xml文件的或者其他形式的单独配置文件。这样表示有些不便之处：**① 与被描述的文件分离，不利于一致性的维护；② 所有这样文件都是ASCII文件，没有显式的类型支持。**

基于元数据的广泛应用，JDK5.0 引入了 Annotation 的概念来描述元数据。在Java中，元数据以标签的形式存在于Java代码中，元数据标签的存在并不影响程序代码的编译和执行。

JDK5.0出来后，java语言中就有了四种类型（TYPE），即：

> **类(class)  枚举(enum) 接口(interface)    注解(@interface)**

它们是处在同一级别的。Java就是通过注解来表示元数据的。



#### 注解简介

注解是Java 1.5引入的，目前已被广泛应用于各种Java框架，如Hibernate，Jersey，Spring。注解相当于是一种嵌入在程序中的元数据，可以使用注解解析工具或编译器对其进行解析，也可以指定注解在编译期或运行期有效。

在注解诞生之前，程序的元数据存在的形式仅限于java注释或javadoc，但注解可以提供更多功能，它不仅包含元数据，还能作用于运行期，注解解析器能够使用注解决定处理流程。

**Annotation(注解)**就是Java提供了一种程序中的元素关联任何信息和任何元数据(metadata)的途径和方法。Annotation是一个接口，程序可以通过反射来获取指定程序元素的Annotation对象，然后通过Annotation对象来获取注解里面的元数据。注解API非常强大，被广泛应用于各种Java框架。



#### 注解分类

根据注解参数的个数进行分类，可以分为：

>1)标记注解 : 一个没有成员定义的Annotation类型被称为标记注解；
>
>2)单值注解 : 只有一个值；
>
>3)完整注解 : 拥有多个值。

根据注解使用方法和用途，可以分为：

>1) JDK内置系统注解
>
>2) 元注解
>
>3) 自定义注解



#### 内置注解

JavaSE中内置三个标准注解，定义在java.lang中：

##### **@Override**

限定重写父类方法，若想要重写父类的一个方法时，需要使用该注解告知编译器我们正在重写一个方法。如此一来，当父类的方法被删除或修改了，编译器会提示错误信息。

##### @Deprecated

标记已过时，当我们想要让编译器知道一个方法已经被弃用(deprecate)时，应该使用这个注解。Java推荐在javadoc中提供信息，告知用户为什么这个方法被弃用了，以及替代方法是什么；

##### @SuppressWarnings

抑制编译器警告，该注解仅仅告知编译器，忽略它们产生了特殊警告。如：在java泛型中使用原始类型。其保持性策略(retention policy)是SOURCE，在编译器中将被丢弃。



### 自定义注解

从上文基础知识的阐述中，可知道Java语言有四种类型，其中注解的类型使用(@interface)来表示。

#### @interface

@interface用来声明一个注解。注解类里的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型。可以通过default来声明参数的默认值。

```java
@interface Simple{
	//这里定义了一个空的注解
}
```

#### 元注解

元注解的作用就是负责注解其他注解。Java5.0定义了4个标准的meta-annotation类型，它们被用来提供对其它 annotation类型作说明。Java5.0定义的元注解有四个,这些类型和它们所支持的类在java.lang.annotation包中可以找到。

##### @Target

**用于描述注解的使用范围（即：被描述的注解可以用在什么地方）。**@Target表示支持注解的程序元素的种类，一些可能的值有TYPE, METHOD, CONSTRUCTOR, FIELD等等。如果Target元注解不存在，那么该注解就可以使用在任何程序元素之上。取值(ElementType)有：

>1.CONSTRUCTOR: 用于描述构造器
>2.FIELD: 用于描述域
>3.LOCAL_VARIABLE: 用于描述局部变量
>4.METHOD: 用于描述方法
>5.PACKAGE: 用于描述包
>6.PARAMETER: 用于描述参数
>7.TYPE: 用于描述类、接口(包括注解类型) 或enum声明

此时在空注解中加入@Target元注解如下所示:

```java
//此注解只能用在方法上
@Target(ElementType.METHOD) 
@interface TestMethod {}
```

##### @Retention

表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）表示注解类型保留时间的长短。取值(RetentionPoicy)有：

>1.SOURCE:在源文件中有效（即源文件保留）
>2.CLASS:在class文件中有效（即class保留）
>3.RUNTIME:在运行时有效（即运行时保留）

此时在上述注解中加入@Retention元注解如:

```java
// 此注解可以用于注解类、接口(包括注解类型) 或enum声明
@Target(ElementType.TYPE) 
//该注解运行时有效。注解处理器可以通过反射，获取到该注解的属性值，从而去做一些运行时的逻辑处理
@Retention(RetentionPolicy.RUNTIME)
@interface TestRn{
}
```

##### @Documented

表示使用该注解的元素应被javadoc或类似工具文档化.它应用于类型声明，类型声明的注解会影响客户端对注解元素的使用。如果一个类型声明添加了Documented注解，那么它的注解会成为被注解元素的公共API的一部分，@Documented是一个标记注解。

```java
//可以被例如javadoc此类的工具文档化
@Documented
@interface TestDoc{	
}
```

##### **@Inherited**

表示一个注解类型会被自动继承。如果用户在类声明的时候查询注解类型，同时类声明中也没有这个类型的注解，那么注解类型会自动查询该类的父类，这个过程将会不停地重复，直到该类型的注解被找到为止，或是到达类结构的顶层（Object）。

```java
//被子类继承的注解
@Inherited
@interface TestInheri{}
```



#### 自定义注解

使用@interface自定义注解时，自动继承了java.lang.annotation.**Annotation**接口，由编译程序自动完成其他细节。在定义注解时，不能继承其他的注解或接口。

##### 定义注解格式

@interface用来声明一个注解，其中的每一个方法实际上是声明了一个配置参数。方法的名称就是参数的名称，返回值类型就是参数的类型（返回值类型只能是**基本类型、Class、String、enum**）。可以通过default来声明参数的默认值。如下所示：

```
基本格式：
public @interface 注解名{定义体s}
```

##### 注解参数(即方法)

注解里面的每一个方法实际上就是声明了一个配置参数，其规则如下:

###### ①修饰符

只能用public或默认(default)这两个访问权修饰 ，默认为default

###### ②类型

注解参数只支持以下数据类型：

基本数据类型（int,float,boolean,byte,double,char,long,short)；

String类型；Class类型；enum类型；Annotation类型;

以上所有类型的数组

###### ③命名

对取名没有要求，如果只有一个参数成员,最好把参数名称设为"**value**",后加小括号。

###### ④参数

注解中的方法不能存在参数

###### ⑤默认值

可以包含默认值，使用default来声明默认值。

##### 实例说明

接下来，按照上述的说明，分别自定义几个注解，并给出使用的案例。

- **自定义 “程序员注解”**

```
// 程序员注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface Programmer{
	String value() default "马云";
}
```

- **自定义 “程序员类型注解”**

```
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface ProgrammerType {
    /**
     * 类型枚举  程序猿 射鸡师
     */
    public enum CoderType{MONKEYS,LION,CHOOK};
    /**
     * 颜色属性
     */
    CoderType type() default CoderType.MONKEYS;
}
```

- 自定义“ 程序员制造厂注解”

```
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@interface ProgrammerProductor {
    /**
     * 厂家编号
     * @return
     */
    public int id() default -1;
    /**
     * 厂家名称
     * @return
     */
    public String name() default "shsxt"; 
    /**
     * 厂家地址
     * @return
     */
    public String address() default "上海";
}
```

- 对上述三个注解的使用如下所示：

```
class Coder{
		@Programmer("老裴")
	    private String coderName;
		@ProgrammerType(type=CoderType.MONKEYS)
	    private String coderType;
	    // 注意，此处使用的ProgrammerProductor注解，并不是将这三个参数注入到下面的         // 变量中，而是仅仅做注释的作用。
		@ProgrammerProductor(id=1,name="程序猿乐园",address="荣乐东路")
	    private String coderProductor;
		public String getCoderName() {
			return coderName;
		}
		public void setCoderName(String coderName) {
			this.coderName = coderName;
		}
		public String getCoderType() {
			return coderType;
		}
		public void setCoderType(String coderType) {
			this.coderType = coderType;
		}
		public String getCoderProductor() {
			return coderProductor;
		}
		public void setCoderProductor(String coderProductor) {
			this.coderProductor = coderProductor;
		}	
}
```



### 参考说明：

作者：乐字节 链接：https://juejin.im/post/5d3ac8d5e51d4577407b1e3a

























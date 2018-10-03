---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories: 
  - java
tags: 
  - java
title: Java基础之注解(annotation)
---


### 什么是annotation
	1. annotation，中文一般称作注解
	2. 是指那些插入到源代码中，用于某种工具处理的标签
	3. 本质上annotation也是一个interface
	4. annotation并不会改变对编写程序的编译方式，也就是说，你将annotation放到代码上，并不会产生直接的作用，需要其他工具/代码处理。
	
### annotation 的分类
	1. 源码注解：annotation能够被编译器用来检测错误或者抑制告警，如@SuppressWarnings
	2. 编译时(Compile-time)注解：处理工具能够根据注解生成代码
	3. 运行时注解：运行时根据注解来动态改变程序的行为

### java定义的注解

|注解接口|应用场合|目的
|---------|-----------|-------|
|Deprecated|全部|将项标记为过时|
|SuppressWarnings|初包和注解外|阻止某个给定类型的告警信息|
|Override|方法|检查该方法是否覆盖了某一个超类方法|
|PostConstruct|方法|被标记的方法应该在构造之后调用|
|PreDestroy|方法|被标记的方法应该在移除之前被调用|
|Resource|类、接口、方法、域|在类或接口上：标记为在其他地方要用到的资源；在方法或域上：为“注入”而标记|
|Resources|类、接口|一个资源数组|
|Generated|全部|供代码生成工具使用
|Target|注解|指定可以应用注解的那些项|
|Retention|注解|指定这个注解可以保留多久|
|Documented|注解|指定这个注解应该包含在注解项的文档中|
|Inherited|注解|指定一个注解，当他应用于某个类的时候，能够自动被他的子类继承|
|Repeatable|注解|指定注解是否能够使用多次，since java8|

NOTE：应用场合为注解的这些注解成为元注解

下面我们主要关注Target，Retention，这两项是我们在自定义注解是需要特别关注的两个注解

### @Retention
被@Retention标记的注解，用来指定被标记的注解可以保留多久，他是通过RetentionPolicy来指定的

|保留规则|描述|
|---------|-----------|
|SOURCE|仅保留在源码中，被编译器忽略，即不保留在.class文件中
|CLASS|保留到类文件，被JVM忽略，即运行时获取不到，默认值|
|RUNTIME|保留到JVM，运行时可以获取到

### @Target
@Targe中定义了一个java.lang.annotation.ElementType的枚举数组，用来指定注解可以应用的项，ElementType的定义为

```java
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
``` 

### 注解的语法
#### 1. 注解的定义

```java
@Target({ ElementType.TYPE, ElementType.METHOD }) 	// #1
@Retention(RetentionPolicy.RUNTIME) 				// #2
@Documented											// #3
public @interface SampleAnnotation {				// #4
	enum COLOR{RED,BLUE};

	boolean isShow() default false;					// #5

	String nickName() default "terry";				// #6

	Class<?> testClass() default Void.class;		// #7

	COLOR color() default COLOR.RED;				// #8

	AnotherAnnotation ref() ;						// #9

	String [] hobbies();							// #10
}
```

- 1 指定该annotation可以用在TYPE和METHOD上，值为多个的情况要用{}括起来
- 2 指定该annotation保留到运行时
- 3 指定这个注解应该包含在注解项的文档中
- 4 annotation必须使用@interface来修饰
- 5 - #10：定义注解的元素,注解可以定义0个元素，也可以定义多个元素，元素可以使用以下几种类型
	- 基本类型（int, short, long, byte, char, double, float,boolean）
	- String
	- Class
	- 枚举类型
	- 注解类型
	- 上述类型组成的数组

#### 2. 注解的使用
上述注解可以如下使用

```java
@SampleAnnotation(isShow = true, ref = @AnotherAnnotation , hobbies = "pingpong")
public class Account {
}
```

注解中元素没有默认值的话，使用时必须指定元素的值，如果注解只有一个value()元素，那么，使用注解的时候可以不用指定元素名，如，假设SampleAnnotation定义如下：

```java
@Target({ ElementType.TYPE, ElementType.METHOD })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface SampleAnnotation {
	String value();
}
```

那么，我们可以直接这样使用：

```java
@SampleAnnotation("codetisan")
public class Account {
}
```

**关于如何在运行时获取反射以动态修改程序运行将在[Java基础之反射(reflect)]中描述**

参考：《JAVA核心技术》

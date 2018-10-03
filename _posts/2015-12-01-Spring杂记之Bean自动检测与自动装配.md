---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories: 
  - spring
tags: 
  - java
  - spring
title: Spring杂记之Bean自动检测与自动装配
---


为了减少xml配置，Spring提供了自动检测与自动装配的方式。
###自动检测
需要两步骤：
1. 在xml中，通过配置 < context:component-scan>来启用自动检测功能；
2. 在需要自动检测的Bean使用特殊的注解进行适当的标注，这些注解包括：

- @Componnet:通用的构造性注解，标识该类为Spring组件
- @Controller：标识将该类定义为Spring MVC controller
- @Repository：标识将该类定义为数据仓库(dao)
- @Service：标识将该类定义为服务
- 使用@Component标注的任意自定义注解，事实上，@Controller，@Repository，@Service也是使用@Component标注的注解

#### 过滤组件扫面
 < context:component-scan>可以通过< context:include-filter>和< context:exclude-filter>两个子元素来调整扫描策略，这两个子元素包含一个type和expression的属性，描述如下：

- annotation :扫描指定注解所标注的那些类
- assignable :扫描派生于expression属性指定类型的那些类
- aspectj   :扫描与expression属性所指定的AspectJ表达式所匹配的那些类
- custom    :使用自定义的org.springframework.core.type.TypeFilter实现类
- regex    :扫描类的名称与expression属性所指定的正则表达式所匹配的那些类

### 自动装配
Spring支持几种不同的自动装配的注解：

- Spring自带的@Autowired注解
- JSR-330的@Inject注解
- JSR-250的@Resource注解

####@Autowired 
1. @Autowired可以标注CONSTRUCTOR,FIELD,METHOD,ANNOTATION_TYPE
2. 默认情况下，使用@Autowired标注的属性或参数必须是可以装配的，如果没有，则会抛出NoSuchBeanDefinitionException，有多个Bean满足装配条件时也会抛出该异常；
3. 当希望没有合适的Bean装配是，允许属性或参数为null，则可以使用@Autowired(required=false)来配置自动装配是可选的；
4. 当有多个Bean满足装配条件时，可以通过@Qualifier("")来指定bean
5. @Autowired是Spring特定依赖，不是标注的Java注解
6. @Autowired默认是byType

#### @Inject
1. @Inject可以标注CONSTRUCTOR,FIELD,METHOD
2. @Inject没有类似@Autowired的required属性，因此@Inject标注的依赖关系必须存在，否则也会抛异常
3. @Inject中可以配合@Named("")来标识可以选择的Bean

#### @Resource
1. @Resource可以标注TYPE,FIELD,METHOD
2. @Resource自带一个name的属性，可以通过@Resource(name="")来指定要注入的Bean

| 注解       | 装配顺序                       | 可标注项                      |
| :----------| :----------------------------- | :---------------------------  |
| @Autowired | byType                         | 构造器,属性,方法,注解         |
| @Inject    | byType                         | 构造器,属性,方法              |
| @Resource  | byName->byType->byQualifier    | 类/接口/注解/枚举，属性，方法 |



参考：《Spring实战 第3版》

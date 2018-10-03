---
published: true
layout: postlayout
thumbimg: 1346208288725.jpg
categories: 
  - spring
tags: 
  - java
  - spring
title: Spring杂记之Bean注入(xml)
---


### I. 声明一个简单的Bean
####  1. 声明bean：

```java
package io.codetisan.spring;

public class Singer {
	public void sing() {
		System.out.println("when i was young i'd listen to the radio");
	}
}
```

#### 2. spring配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="singer" class="io.codetisan.spring.Singer" />

</beans>
```

#### 3. 测试代码：

```java
package io.codetisan.spring;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;


public class MainTest {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		Singer singer = context.getBean("singer",Singer.class);
		singer.sing();
	}
}
```

### II. 构造器注入
#### 1. 声明bean

```java
package io.codetisan.spring;

public class Song {
	private String name;

	public Song(String name) {
		this.name = name;
	}

	public String getName(){
		return this.name;
	}
}
```

```java
package io.codetisan.spring;

public class Singer {
	private String name;
	private Song song;

	public Singer(String name, Song song) {
		this.name = name;
		this.song = song;
	}

	public void sing() {
		System.out.println("I'm " + name + " I'm singing " + song.getName());
	}
}

```
#### 2. spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">
	<bean id="song" class="io.codetisan.spring.Song" >
		<constructor-arg value="Yesterday Once More" />
	</bean>
	<bean id="singer" class="io.codetisan.spring.Singer" >
		<constructor-arg value="Carpenters" />
		<constructor-arg ref="song"/>
	</bean>

</beans>
```

如果引用的bean只使用一次，也可以如下声明：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="singer" class="io.codetisan.spring.Singer">
		<constructor-arg value="Carpenters" />
		<constructor-arg>
			<bean class="io.codetisan.spring.Song">
				<constructor-arg value="Yesterday Once More" />
			</bean>
		</constructor-arg>
	</bean>

</beans>
``` 

#### 3.  测试代码
同上

III. 注入Bean属性
#### 1. 声明bean

```java
package io.codetisan.spring;

public class Song {
	private String name;

	public void setName(String name) {
		this.name = name;
	}

	public String getName() {
		return this.name;
	}
}

package io.codetisan.spring;

import java.util.List;

public class Singer {
	private String name;
	private Song song;
	private List<String> instruments;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Song getSong() {
		return song;
	}

	public void setSong(Song song) {
		this.song = song;
	}

	public void sing() {
		System.out.println("I'm " + name + " I'm singing " + song.getName()+" I can play with"+instruments.toString());
	}

	public List<String> getInstruments() {
		return instruments;
	}

	public void setInstruments(List<String> instruments) {
		this.instruments = instruments;
	}
}
```

#### 2. spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="song" class="io.codetisan.spring.Song">
		<property name="name" value="Yesterday Once More" />
	</bean>
	<bean id="singer" class="io.codetisan.spring.Singer">
		<property name="name" value="Carpenters" />
		<property name="song" ref="song" />
		<property name="instruments">
			<list>
				<value>guitar</value>
			</list>
		</property>
	</bean>

</beans>
``` 

#### 3.  测试代码
同上

spring提供了四种集合配置元素

|集合元素|用途|
|----------|-----|
| < list>|装配list类型的值，允许重复|
| < set>|装配set类型的值，不允许重复|
|< map>|装配map类型的值，key和value可以是任何值|
|< props>|装配properties类型的值，key和value必须都是string|

当装配类型为数组或者java.util.Collection任意实现的属性时，选择< list>和< set>都是可以的
map和props示例

```xml
	<map>
		<entry key="GUITAR" value-ref="guitar" />
	</map>
	<map>
		<entry key-ref="guitarkey" value-ref="guitar" />
	</map>
	
	<props>
		<prop key="GUITAR">strum</prop>
	</props>
``` 

IV. 工厂方法创建Bean
有时候，Bean只提供了静态工厂方法来实例化的方法，Spring可以通过< bean>元素的factory-method属性来装配工厂创建的Bean
#### 声明Bean

```java
package io.codetisan.spring;

public class Stage {
	private Stage() {
	}

	private static class StageSingletonHolder {
		private static Stage instance = new Stage();
	}

	public static Stage getInstance() {
		return StageSingletonHolder.instance;
	}
}
```

#### 2. spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="stage" class="io.codetisan.spring.Stage" factory-method="getInstance"/>

</beans>
```

#### 3. 测试代码

```java
package io.codetisan.spring;

import org.junit.Assert;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;


public class MainTest {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
		Stage stage = context.getBean("stage",Stage.class);
		Assert.assertNotNull(stage);
	}
}
```

### V. 初始化和销毁Bean
	当我们实例化一个Bean时，可能要执行一些初始化操作来确保该Bean处于可用状态，同样的，当Bean从容器中移除时，可能也需要做一些清理工作。
	Spring中可以通过init-method和destroy-method来完成这些功能
#### 1. 声明Bean

```java
package io.codetisan.spring;

public class Singer {
	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}


	public void sing() {
		System.out.println("I'm " + name );
	}

	public void beforeSing(){
		System.out.println("welcome...");
	}

	public void afterSing(){
		System.out.println("thank you...");
	}
}
```

#### 2. spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="singer" class="io.codetisan.spring.Singer" init-method="beforeSing" destroy-method="afterSing">
		<property name="name" value="Carpenters" />
	</bean>

</beans>
```

### VI. 使用Spring的命名空间p装配属性
Spring提供了一个p命名空间来简化xml的配置
如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="song" class="io.codetisan.spring.Song">
		<property name="name" value="Yesterday Once More" />
	</bean>
	<bean id="singer" class="io.codetisan.spring.Singer" p:name="Carpenters" p:song-ref="song"/>

</beans>
```

等同于

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="song" class="io.codetisan.spring.Song">
		<property name="name" value="Yesterday Once More" />
	</bean>
	<bean id="singer" class="io.codetisan.spring.Singer">
		<property name="name" value="Carpenters" />
		<property name="song" ref="song" />
	</bean>

</beans>
``` 

参考：《Spring实战 第3版》

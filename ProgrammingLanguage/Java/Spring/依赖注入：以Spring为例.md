# 依赖注入：以Spring为例

## IoC的核心理念

IoC的核心理念在于分离这两种职责：

1. 类对象的依赖解析（即：生成或者查找过程）
2. 类对象的使用

具体而言，就是把各个类对象实例的依赖解析过程统一交给容器负责，其他类只负责使用类对象，不再去承担具体的创建、查找类实例的职责。

要生成一个类，如果不考虑工厂方法，传统的做法总是类似于：
```Java
MyClass mc=new MyClass(1,"hello,world",true);

```
如果我们丢开这些语法形式，可以发现，通过new生成新对象实际上提供了两个基本信息：
1. 类名
2. 构造器参数

如果把这些信息写入到配置文件中，然后在主程序中读取配置，就可以利用反射技术动态生成这些类的实例。


比如,现有若干个Java类，其中一个POJO类如下所示：

```Java
package com.mycompany.mavenproject1;

public class MyBean {
	private int a;
	private String b;

	public MyBean(int a,String b){
		this.a=a;
		this.b=b;
	}

	/**
	 * @return the a
	 */
	public int getA() {
		return a;
	}

	/**
	 * @param a the a to set
	 */
	public void setA(int a) {
		this.a = a;
	}

	/**
	 * @return the b
	 */
	public String getB() {
		return b;
	}

	/**
	 * @param b the b to set
	 */
	public void setB(String b) {
		this.b = b;
	}
	
}

```

将其信息写入配置文件，随便其一个名字，比如叫`config.xml`
```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

	   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
">

	<bean id="myBean" class="com.mycompany.mavenproject1.MyBean">
		<constructor-arg name="a" value="2"/>
		<constructor-arg name="b" value="Hello,world"/>
	</bean >
	
	<!--其他bean的配置信息 -->
</beans>
```


新建一个Main类用于读取配置，利用反射创建相应的类即可:

```Java
	    
public static void main(String[] args) throws ClassNotFoundException {
	
	String myBeanClassName="";
	int myBeanConstructorA;
	String myBeanConstructorB;
	
	//...
	//...从配置文件中读取myBean类名称："com.mycompany.mavenproject1.MyBean";
	//...从配置文件中读取myBean类构造器参数a;	
	//...从配置文件中读取myBean类构造器参数b;	
	//...
	Class c=Class.forName(myBeanClassName);
	MyBean myBean;
	try {
		myBean = (MyBean) c.getConstructor(Integer.TYPE,String.class).newInstance(myBeanConstructorA,myBeanConstructorB);
		System.out.println(myBean.getA());
	} catch (InstantiationException ex) {
		//....	 
	}
	//...
}
	

```
由于这里读配置、并根据相关信息进行实例化是个非常普遍的过程，可以将之封装。为了不重复造轮子，利用Maven添加spring-context依赖：
```XML
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
    </dependencies>
```


即可使用如下的方式获取对象:

```Java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

/**
 *
 * @author snp
 */
public class Main {

	public static void main(String[] args) {
		ApplicationContext ctx=new FileSystemXmlApplicationContext("config.xml");
		MyBean myBean;
		myBean = (MyBean) ctx.getBean(MyBean.class);
		System.out.println(myBean.getA());
		System.out.println(myBean.getB());
	}
	
}
```

## Spring IoC容器XML配置

### constructor注入与静态方法注入

配置文件类似于：
```XML
<bean id="sss" class="SSS"></bean>
<bean id="xxx" class="XXX">
    <constructor-arg value="15"/>    <!--构造器注入：基本类型的参数-->
    <constructor-arg ref="sss"/>     <!--构造器注入：引用类型的参数-->
</bean>
<bean id="yyy" class="YYY" factory-method="getInstance"/>   <!--通过静态方法构建-->
```
Spring Bean默认为单例，当容器分配一个Bean时，总是返回该Bean类的同一个实例。如何修改这种默认特性呢？可以为Bean声明一个作用域：
```XML
<bean id="zzz" class="ZZZ" scope="prototype"></bean>
```
常见的作用域包括：
* prototype        #每次都是新的
* singleton       #默认
* request           #每次HTTP Request都产生新的
* session            #每次HTTP Session都产生新的
* global-session

### setter注入

配置文件类似于：
```XML
<bean id="sss" class="SSS">
    <property name="x" value="Jingle Bells"/>
    <property name="y" ref="yId"/>
</bean>
```


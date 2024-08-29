# Spring

## 前置知识

## Spring 概述

1. Spring是一个包装好的jar包
2. Spring 这里主要说的是Spring framework

##  控制反转(IoC) 原则和 面向切面编程 (AOP) 技术

> 依赖注入 (DI) 是 IoC 的一种特殊形式，其中对象仅通过构造函数参数、工厂方法参数或在对象实例构造或从工厂方法返回后设置在对象实例上的属性来定义其依赖项（即它们与之交互的其他对象，然后，IoC 容器在创建 Bean 时注入这些依赖项。这个过程从根本上说是 Bean 本身控制其依赖项的实例化或位置的反面（因此得名控制反转），通过直接构造类或使用服务定位器模式等机制来实现。

依赖注入（DI） 是IoC原则的一种实现，大致意思是 一个对象中的某一个属性是另一个对象的实例，但是不通过这个对象中直接的new ，而是通过构造函数，工厂方法参数 ... 来定义。其中需要的实例，就称为依赖项。

在传统的编程模式中，一个对象（通常称为“Bean”）通常会自己控制其依赖项的实例化或位置，这意味着对象会直接创建或查找它所需要的其他对象。这种方式称为“**直接控制**”，因为它不依赖于外部来提供所需的依赖项。

依赖注入与传统的直接控制方式相反。在依赖注入中，对象不自己寻找或创建所需的依赖项，而是依赖于外部的容器或工厂来提供这些依赖项。这种方式被称为“**控制反转**”，因为它将控制权从对象本身转移到了外部的容器或工厂。

> `org.springframework.beans` 和 `org.springframework.context` 包是 Spring 框架 **IoC 容器**的基础

> 在 Spring 中，构成应用程序主干并由 Spring IoC 容器管理的对象称为 bean。bean 是由 Spring IoC 容器实例化、组装和管理的对象。否则，bean 只是应用程序中众多对象之一。bean 及其之间的依赖关系反映在容器使用的配置元数据中。

> `org.springframework.context.ApplicationContext` 接口代表 Spring IoC 容器，负责实例化、配置和组装 Bean。容器通过读取配置元数据来获取有关要实例化、配置和组装的组件的指令。

配置元数据有几种方式：【带注解的组件类、带有工厂方法的配置类、外部 XML 文件】

IoC容器使用某种形式来使用配置元数据

### 使用基于java 的配置

1. 使用应用程序组件类上的基于注解的配置元数据定义 bean。
2. 用基于 Java 的配置类，在应用程序类外部定义 bean

### 使用XML 作为外部配置

> 基于 XML 的配置元数据将这些 Bean 配置为顶级 `<beans/>` 元素内的 `<bean/>` 元素
>
> 要**实例化容器**，需要将 XML 资源文件的路径提供给 `ClassPathXmlApplicationContext` 构造函数，该构造函数允许容器从各种外部资源（如本地文件系统、Java `CLASSPATH` 等）加载配置元数据。

实例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
		https://www.springframework.org/schema/beans/spring-beans.xsd">

	<bean id="..." class="...">  
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<bean id="..." class="...">
		<!-- collaborators and configuration for this bean go here -->
	</bean>

	<!-- more bean definitions go here -->

</beans>
```

```java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```

### 使用容器

容器管理bean

1. 检索bean的实例： `T getBean(String name, Class<T> requiredType)` 方法

## Bean

> Spring IoC 容器管理一个或多个 Bean。这些 Bean 是使用提供给容器的配置元数据创建的（例如，以 XML `<bean/>` 定义的形式）。
>
> Bean的元数据转换为一组属性，构成Bean的定义。
>
> 除了包含有关如何创建特定 Bean 的信息的 Bean 定义之外，`ApplicationContext` 实现还允许注册在容器外部（由用户）创建的现有对象。这是通过通过 `getBeanFactory()` 方法访问 `ApplicationContext` 的 `BeanFactory` 来完成的，该方法返回 `DefaultListableBeanFactory` 实现。`DefaultListableBeanFactory` 通过 `registerSingleton(..)` 和 `registerBeanDefinition(..)` 方法支持此注册。但是，典型的应用程序仅使用通过常规 Bean 定义元数据定义的 Bean。

### 命名Bean

> 每个 Bean 都有一个或多个标识符。这些标识符在托管 Bean 的容器中必须是唯一的。Bean 通常只有一个标识符。但是，如果它需要多个标识符，则额外的标识符可以被视为别名。

1. 基于xml配置元数据 `<bean/>` 元素的`id`属性或者`name`来指定要实例化的Bean的类型

   使用`id` 属性、`name` 属性或者两者来制定Bean的名字。

   id属性是指定一个唯一的`id` 

   别名在name属性中指定，多个别名用逗号和分号或者空格间隔。

   没有指定名字，容器为Bean生成一个唯一的名字。

   > Spring 为未命名的组件生成 Bean 名称：获取简单类名并将它的第一个字符转换为小写。

2. 基于java配置元数据

   > 可以在 `@Configuration` 注释或 `@Component` 注释的类中使用 `@Bean` 注释。
   >
   > `@Bean` 是一个方法级注释，是 XML `<bean/>` 元素的直接对应项

### 实例化bean

1. 基于xml配置元数据 ：使用`<bean/>`元素的`class`属性来指定要实例化的Bean的类型

   > - 在`<bean/>`元素的`class`属性中，通常指定要实例化的Bean的类型。
   > - 这类似于在Java代码中使用`new`运算符来创建对象。

   **两种使用`class`属性的方式**：

   - 第一种方式：直接指定要构造的Bean类，Spring容器将通过反射调用该类的**构造函数**来创建Bean实例。

     - 只需指定 Bean 类就足够了

       ```xml
       <bean id="exampleBean" class="examples.ExampleBean"/>
       ```

   - 第二种方式：指定一个包含静态工厂方法的类，在这种情况下，Spring容器将调用该类上的静态工厂方法来创建Bean实例。返回的对象类型可能是与工厂方法相同的类，或者是一个完全不同的类。

     - 在定义使用静态工厂方法创建的 bean 时，使用 `class` 属性指定包含 `static` 工厂方法的类，并使用名为 `factory-method` 的属性指定工厂方法本身的名称。

       能够调用此方法（带可选参数，如下所述）并返回一个活动对象，该对象随后将被视为通过构造函数创建

       ```xml
       <bean id="clientService"
       	class="examples.ClientService"
       	factory-method="createInstance"/>
       ```

       ```java
       public class ClientService {
       	private static ClientService clientService = new ClientService();
       	private ClientService() {}
       
       	public static ClientService createInstance() {
       		return clientService;
       	}
       }
       ```

     - 使用其他Bean的实例方法，使用实例工厂方法实例化会调用容器中现有 bean 的非静态方法来创建新的 bean

       使用此机制，请将 `class` 属性留空，并在 `factory-bean` 属性中指定当前（或父级或祖先）容器中包含要调用的实例方法的 **bean 的名称**以创建对象。使用 `factory-method` 属性设置工厂方法本身的名称

       ```xml
       <!-- 这个examples.DefaultServiceLocator 中有一个方法，返回一个实例-->
       <bean id="serviceLocator" class="examples.DefaultServiceLocator">
       </bean>
       <!-- 省略class factory-bean值是要用的实例方法的bean的名字，factory-method 要用的实例方法名 -->
       <bean id="clientService"
       	factory-bean="serviceLocator"
       	factory-method="createClientServiceInstance"/>
       ```

       ```java
       public class DefaultServiceLocator {
       
       	private static ClientService clientService = new ClientServiceImpl();
       
       	public ClientService createClientServiceInstance() {
       		return clientService;
       	}
       }
       ```

       > 在 Spring 文档中，“工厂 Bean” 指的是在 Spring 容器中配置的 Bean，它通过 **实例** 或 **静态**工厂方法创建对象。相比之下，`FactoryBean`（注意大小写）指的是 Spring 特定的 FactoryBean实现类。


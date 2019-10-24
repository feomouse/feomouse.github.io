---
title: javaweb之spring与springmvc
categories: java
---

### 前言

今天的主题是javaweb，而且主要是专注于java网站应用后台的技术。

在纷繁的javaweb技术中，我开始接触主流的开发技术流-ssh(springmvc, spring, hibernate)。因为大学主要学习的是.NET方向的，所以接触java，转行java对我也是一个不小的挑战。

### 从0开始

像是ssh这种技术流，是多种开发思想结合的结晶。

对于spring，是因为适应应用开发逐渐复杂，推崇高内聚低耦合的软件开发思想，于是我们趋于将软件的开发分成多部分，专注于领域知识，可复用，可拓展。所以有了插件这个概念，插件也还是代表软件，但是它可以是第三方提供的，所以我们有一些专业的领域需求软件时，可以用他人已经做好的，并插入到我们的应用软件中。而我们可以把第三方的插件管理交给spring，所以我们只需专注于我们自己的领域需求开发。

对于springmvc，则是由于MVC（模型-视图-控制器）范式在应用开发领域的流行，与spring结合开发出来的。

对于hibernate，则是因为需要将面向对象和数据库开发结合所产生的开发框架。

而这篇文章主要的，只是从项目结构的角度上来，思考一些知识和我的实践笔记。

### 总结点

1. 用eclipse创建maven-archetype webapp;
2. 在系统已经生成的src/main文件夹下创建java文件夹，所有的java代码，包括控制器，服务接口和类，持久层接口和类，还有模型类等都放在里面。编译后的class文件会在classpath下,也就是编译后生成项目的WEB-INF/classese文件夹下；
3. 把spring和springmvc的xml配置文件都放在系统已经生成的src/main/resources文件夹下。编译后会直接放在classpath下，也就是编译后生成项目的WEB-INF/classese文件夹下（需要在web.xml里编写配置，如下）；
4. jsp页面和web.xml文件放在系统已经生成的src/main/webapp/WEB-INF文件夹下，编译后会直接放在生成项目的WEB-INF文件夹下；
5. maven依赖第三方插件编译后会在生成项目的WEB-INF/lib文件夹下；
6. web.xml可以当成是入口配置文件，也就是查找spring和springmvc配置文件的地址可以在里面配置，对用户请求的路由的servlet映射到springmvc里的dispatchservlet类也在里面配置，从而达到使javaweb使用springmvc框架的目的；

再贴spring和springmvc结合的最基本的配置文件：

src/main/webapp/WEB-INF/web.xml

```
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
	<display-name>Archetype Created Web Application</display-name>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:spring-mvc.xml</param-value>
	</context-param>
	<servlet>
		<servlet-name>name</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	  <init-param>
	    <param-name>contextConfigLocation</param-name>
	    <param-value>classpath:spring-mvc.xml</param-value>
	  </init-param>
	  <load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
	  <servlet-name>name</servlet-name>
	  <url-pattern>/</url-pattern>
	</servlet-mapping>
</web-app>

```

src/main/resources/spring-mvc.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        https://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd"
        >

    <context:component-scan base-package="controllers"/>
    <context:component-scan base-package="domin"/>
    <context:component-scan base-package="services"/>
    
    <mvc:annotation-driven/>

    <bean id="viewResolver" 
    	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="/WEB-INF/view/"/>
      <property name="suffix" value=".jsp"/>
    </bean>
</beans>
```

src/main/java/controllers/NameController.java

```
package controllers;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import services.IPersonService;

@Controller
public class NameController {
	@Autowired
	private IPersonService pservice;
	
    @RequestMapping(value="/get-name")
    public String getName() {
    	return pservice.getPersonName();
    }
}

```

src/main/java/domin/Person.java

```
package domin;

public class Person {
  private String name;
  
  public String getName() {
	  return name;
  }
  
  public void setName(String name) {
	  this.name = name;
  }
}

```

src/main/java/services/IPersonService.java

```
package services;

public interface IPersonService {
  public String getPersonName();
}

```

src/main/java/services/PersonService.java

```
package services;

import org.springframework.stereotype.Service;

import domin.Person;

@Service
public class PersonService implements IPersonService {
	public String getPersonName() {
		Person person = new Person();
		
		person.setName("feomouse");
		
		return person.getName();
	}
}
```

src/webapp/WEB-INF/view/feomouse.jsp

```
<!DOCTYPE HTML>
<<html>
	<body>
	<h2>Hello feo!</h2>
	</body>
</html>

```

### 总结

以上只是学习笔记，大体重点是实现了基本的视图层和控制器层，至于服务层和持久层，之后会继续学习。
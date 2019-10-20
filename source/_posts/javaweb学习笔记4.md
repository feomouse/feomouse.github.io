---
title: javaweb学习笔记4
categories: javaweb
---

### 前言

在之前，我实现了基本的springmvc，spring，hibernate和mybatis组合的javaweb增删改查。现在，在mvc框架方面，还有一个struts2的框架，其也是用来处理servlet请求并返回渲染视图的框架。

### 实现

对于struts2，其配置比springmvc配置复杂一些，需要写专门的配置文件。在web.xml文件里，需要指定将 请求处理转移到StrutsPrepareAndExecuteFilter类中以使用struts2框架。在配置文件里，因为我使用的是注解而不是配置文件类来配置action（相当于controller），所以要加上	

```
<constant name="struts.convention.action.packages" value="controllers" />
```

还有需要在配置文件里指定返回视图的根目录：

```
<constant name="struts.convention.result.path" value="/WEB-INF/view/struts2View/" />
```

还有便是struts2接收请求参数的话，如果是get请求，放在query里的字段，需要在action里添加相应的同名字段和get set方法。

如果是post方法，将json放在body里的话，需要添加struts2-json-plugin插件以解析json数据，对应的键值也要添加至action的字段和get set方法里。对应的，还需要在action添加@ParentPackage("json-default")注解和对应方法添加的Action注解里添加interceptorRefs= {@InterceptorRef("json")}以表示接收body里的json数据并解析。

以下是实现代码：

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
	
	<!-- 适配struts2框架配置 -->
	<filter>
		<filter-name>struts2</filter-name>
		<filter-class>
			org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter
		</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>struts2</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
</web-app>
```

src/main/resources/struts.xml（struts2配置文件）

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">
    
<struts>
    <constant name="struts.enable.DynamicMethodInvocation" value="true" />
    <constant name="struts.devMode" value="false" />
    <constant name="struts.i18n.encoding" value="UTF-8"/>
	<constant name="struts.convention.action.packages" value="controllers" />
	<constant name="struts.convention.result.path" value="/WEB-INF/view/struts2View/" />
</struts>
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
    <context:component-scan base-package="doa" />
    
    <!-- 开启注解处理器 -->
    <context:annotation-config />
    
    <mvc:annotation-driven/>
</beans>
```

src/main/java/controllers/NameAction.java（struts2控制器）

```
package controllers;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.apache.struts2.convention.annotation.Action;
import org.apache.struts2.convention.annotation.InterceptorRef;
import org.apache.struts2.convention.annotation.ParentPackage;
import org.apache.struts2.convention.annotation.Result;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Controller;

import com.opensymphony.xwork2.ActionContext;
import com.opensymphony.xwork2.ActionSupport;

import domin.Person;
import services.IPersonService;

@Controller
@Scope("prototype")
@ParentPackage("json-default")
public class NameAction extends ActionSupport {
	@Autowired
	private IPersonService pservice;
	
	private String name;
	private Integer id;
	private String sex;
	private String oldName;
	private String newName;
	
	public void setOldName(String oldName) {
		this.oldName = oldName;
	}
	
	public void setNewName(String newName) {
		this.newName = newName;
	}
	
	public String getOldName() {
		return oldName;
	}
	
	public String getNewName() {
		return newName;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public void setId(Integer id) {
		this.id = id;
	}
	
	public void setSex(String sex) {
		this.sex = sex;
	}
	
	public Integer getId() {
		return this.id;
	}
	
	public String getName() {
		return this.name;
	}
	
	public String getSex() {
		return this.sex;
	}
	
	@Action(value="/", results= {@Result(name="getIndex", location="index.jsp")})
    public String getIndex() {
        return "getIndex";
    }

	@Action(value="/get-sex", results= {@Result(name="getPersonSex", location="getsex.jsp")})
    public String getPersonSex() {
		ActionContext context = ActionContext.getContext();
		String res = pservice.getPersonSexByName(this.name);
		Map<String,Object> request = (Map) context.get("request");		
		request.put("res", res);
    	return "getPersonSex";
    }
	
	@Action(value="/insert-person", 
			results= {@Result(name="insertPerson", location="insertperson.jsp")},
	 		interceptorRefs= {@InterceptorRef("json")})
	public String insertPerson() {
		ActionContext context = ActionContext.getContext();
    	boolean res = pservice.insertPerson(new Person(this.id, this.name, this.sex));
    	context.put("res", res);
       	return "insertPerson";
    }
	
	@Action(value="/delete-person", 
			results= {@Result(name="deletePerson", location="deleteperson.jsp")},
	 		interceptorRefs= {@InterceptorRef("json")})
    public String deletePerson() {
		ActionContext context = ActionContext.getContext();
    	String res = pservice.deletePerson(this.name) == false ? "no" : "sus";
    	context.put("res", res);
       	return "deletePerson";
	}
	
	@Action(value="/update-name", 
			results= {@Result(name="updatePersonName", location="updatepersonname.jsp")},
	 		interceptorRefs= {@InterceptorRef("json")})
    public String updatePersonName() {
		ActionContext context = ActionContext.getContext();
    	String res = pservice.setPersonName(this.oldName, this.newName);
       	context.put("res", res);
    	return "updatePersonName";    
	}
}
```

以上对应的jsp视图代码没有贴出来，因为不是重点。

### 总结

以上是基本的struts2是控制器实现，在jsp视图方面，我会继续花时间去学习和实践。
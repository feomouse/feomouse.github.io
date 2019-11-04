---
title: javaweb之hibernate基本实现
categories: 持久层技术
---

### 前言

这一篇文章是接上一篇javaweb学习笔记1的。还记得我上一篇实现了基本的将javaweb，jsp，spring，springmvc结合，实现了基础的视图层和web控制层的实现，并外加了简单的服务层和模型层，但是没有集入持久层，也就是上一篇所说的ssh中的h-hibernate数据映射持久层。

今天这一篇便将hibernate集入至ssh架构中，完成一个简单的后台web应用。但是因主题是hibernate，所以在视图层和web控制层为了方便，省略了jsp视图的集成，而改而使用rest控制器来方便实现。以后会再将视图层模板技术集成进行学习。

### 先将View层从返回jsp换成json数据

这一步骤的实现还是比较简单明了的。

在上一篇当中，我们的spring-mvc.xml配置文件当中，除了引入注解，指定遍历浏览的包，还有就是引入视图解析器。如下：

```
...略

<bean id="viewResolver" 
  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name="prefix" value="/WEB-INF/view/"/>
  <property name="suffix" value=".jsp"/>
</bean>

```

如以上配置代码，我们如果要将控制器返回的view从视图模板改成简单的json数据，有几个步骤。

* 首先要将上述配置注释。
* 在maven的pom.xml文件中加入依赖jackson相关包（jackson-databind,jackson-core,jackson-annotations）以解析客户端传来的json格式数据。
* 将之前的@Controller注解换成@RestController注解。

在以上的工作都干好后，基本上就可以将之前返回jsp变成返回json数据，而且也可以接收客户端在body里传过来的json数据（@RequestBody注解参数获取数据）。

### 加入hibernate

加入hibernate，大致有以下步骤。

* 首先就要通过maven添加相关依赖包（hibernate-core，hibernate-ehcache，hibernate-jpa-2.1-api，mysql-connector-java）。
* 接着就要添加一个持久访问层，一般我们称之为DOA层，里面包含了我们自己定义的，持久层需要对外曝露的接口定义，并在对应的接口实现中操作调用hibernate提供给我们的数据库访问接口。
* 在之前domin层，也就是模型层中定义的POJO模型实体类中，添加一些hibernate和jpa提供的注解，以指明实体类对应于数据库的表，字段，字段类型，约束等。
* 最后也是很重要的一步，就是添加hibernate的配置文件hibernate.cfg.xml。里面指明了数据库驱动，连接字符串，用户名，密码，数据库等基本配置信息。但还有一个很重要的，便是在里面添加定义需要hibernate映射的实体类。

大致的实现步骤便是上述所示。所以变更的内容较上一篇内容变更，在services，doa，domin都用重要的改变。对于services层则是调用doa层接口，对于doa是新加的数据库持久访问层，而domin则与数据库表有了映射。

具体关键实现代码如下。

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
    
    <mvc:annotation-driven/>
</beans>
```

src/main/resources/hibernate.cfg.xml（hibernate配置文件）

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
   "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
   "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
   
<hibernate-configuration>
  <session-factory>
    <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
    <property name="connection.url">jdbc:mysql://127.0.0.1:3306/world?serverTimezone=UTC</property>
    <property name="connection.username">feo</property>
    <property name="connection.password">107409</property>
    <mapping class="domin.Person" />
  </session-factory>
</hibernate-configuration>
```

src/main/java/controllers/NameController.java（web应用层）

```
package controllers;

import java.util.List;
import java.util.Map;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import domin.Person;
import services.IPersonService;

@RestController
public class NameController {
	@Autowired
	private IPersonService pservice;
	
    @RequestMapping(value="/get-sex")
    public String getSex(@RequestParam(value="name") String name) {
    	return pservice.getPersonSexByName(name);
    }
    
    @RequestMapping(value="/insert-person", method=RequestMethod.POST)
    public void insertPerson(@RequestBody Person person) {
    	pservice.insertPerson(person);
    }
    
    @RequestMapping(value="/delete-person", method=RequestMethod.POST)
    public String deletePerson(@RequestBody Map<String, String> name) {
    	return pservice.deletePerson(name.get("name")) == false ? "no" : "sus";
    }
    
    @RequestMapping(value="/update-name", method=RequestMethod.POST)
    public String updatePersonName(@RequestBody List<Map<String, String>> nameInfo) {
    	return pservice.setPersonName(nameInfo.get(0).get("oldName"), nameInfo.get(1).get("newName"));
    }
}
```

src/main/java/services/IPersonService.java（服务层接口）

```
package services;

import domin.Person;

public interface IPersonService {
    public boolean insertPerson(Person person);

    public boolean deletePerson(String name);
    
    public String getPersonSexByName(String name);
    
    public String setPersonName(String oldName, String newName);
}
```

src/main/services/PersonService.java（服务层实现）

```
package services;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Service;

import doa.IHibernateDoa;
import domin.Person;

@Service
public class PersonService implements IPersonService {
	@Autowired
	private IHibernateDoa doaApi;
	
	public PersonService() {
		
	}
	
    public boolean insertPerson(Person person) {
    	return doaApi.insertPerson(person);
    }

    public boolean deletePerson(String name) {
    	return doaApi.deletePerson(name);
    }
    
    public String getPersonSexByName(String name) {
    	return doaApi.getPersonSexByName(name);
    }
    
    public String setPersonName(String oldName, String newName) {
    	return doaApi.setPersonName(oldName, newName);
    }
}
```

src/main/domin/Person.java（持久层模型）

```
package domin;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

import org.hibernate.annotations.Generated;
import org.hibernate.annotations.GenericGenerator;
import org.hibernate.annotations.Table;

@Entity( name = "Person" )
public class Person {
  private Integer id;	
	
  private String name;
  
  private String sex;
  
  public Person() {
	  
  }
  
  @Id
  @GeneratedValue(generator="increment")
  @GenericGenerator(name="increment", strategy="increment")
  public Integer getId() {
	  return id;
  }
  
  public void setId(Integer id) {
	  this.id = id;
  }
  
  @Column(name = "name")
  public String getName() {
	  return name;
  }
 
  public void setName(String name) {
	  this.name = name;
  }
  
  @Column(name = "sex")
  public String getSex() {
	  return sex;
  }
  
  public void setSex(String sex) {
	  this.sex = sex;
  }
}
```

src/main/java/doa/IHibernateDoa.java（持久层接口）

```
package doa;

import domin.Person;

public interface IHibernateDoa {
    public boolean insertPerson(Person person);

    public boolean deletePerson(String name);
    
    public String getPersonSexByName(String name);
    
    public String setPersonName(String oldName, String newName);
}
```

src/main/java/doa/HibernateDoa.java（持久层接口实现）

```
package doa;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.boot.MetadataSources;
import org.hibernate.boot.registry.StandardServiceRegistry;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.hibernate.resource.transaction.spi.TransactionStatus;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Repository;

import domin.Person;

@Repository
@Scope
public class HibernateDoa implements IHibernateDoa {
    private final StandardServiceRegistry registry;
    
    private final SessionFactory sessionFactory;
	
	public HibernateDoa() {
		registry = new StandardServiceRegistryBuilder().configure().build();
		
		sessionFactory = new MetadataSources(registry).buildMetadata().buildSessionFactory();
    }
	
    public boolean insertPerson(Person person) {
    	Session session = sessionFactory.openSession();
    	try {
        	session.beginTransaction();
        	session.save( person );
        	session.getTransaction().commit();
    	}
    	
    	catch (Exception e) {
    		if ( session.getTransaction().getStatus() == TransactionStatus.ACTIVE
    				|| session.getTransaction().getStatus() == TransactionStatus.MARKED_ROLLBACK ) {
    			session.getTransaction().rollback();
    		}
    	}
    	
    	finally {
    		session.close();
        	return true;
    	}
    }

    public boolean deletePerson(String name) {
    	Session session = sessionFactory.openSession();
    	boolean result = true;
    	
    	try {
        	session.beginTransaction();
        	session.createQuery("DELETE FROM Person WHERE name = :name")
        			.setParameter("name", name)
        			.executeUpdate();
        	
        	session.getTransaction().commit();
    	}
    	
    	catch (Exception e) {
    		if ( session.getTransaction().getStatus() == TransactionStatus.ACTIVE
    				|| session.getTransaction().getStatus() == TransactionStatus.MARKED_ROLLBACK ) {
    			session.getTransaction().rollback();
    			result = false;
    		}
    	}
    	
    	finally {
    		session.close();
        	return result;
    	}
    }
    
    public String getPersonSexByName(String name) {
    	Session session = sessionFactory.openSession();
    	Person p = null;
    	
    	try {
        	p = session.createQuery("from Person p WHERE p.name = :name", Person.class)
					.setParameter("name", name)
        			.getSingleResult();
    	}
    	
    	catch (Exception e) {

    	}
    	
    	finally {
    		session.close();
        	return p.getSex();
    	}
    }
    
    public String setPersonName(String oldName, String newName) {
    	Session session = sessionFactory.openSession();
    	int res = 0;
    	try {
    		session.beginTransaction();
    		
        	res = session.createQuery("UPDATE Person p SET p.name = :newName WHERE p.name = :oldName")
					.setParameter("newName", newName)
					.setParameter("oldName", oldName)
        			.executeUpdate();
        	
        	session.getTransaction().commit();
    	}
    	
    	catch (Exception e) {
    		if ( session.getTransaction().getStatus() == TransactionStatus.ACTIVE
    				|| session.getTransaction().getStatus() == TransactionStatus.MARKED_ROLLBACK ) {
    			session.getTransaction().rollback();
    		}
    	}
    	
    	finally {
    		session.close();
        	return res != 0 ? newName : oldName;
    	}
    }
}
```

### 总结

以上是对ssh的简单实现，增删改查的基本功能也有实现。可以说是麻雀虽小但该具体学习的也都具体学了。重点是怎么将hibernate集成至web应用中。
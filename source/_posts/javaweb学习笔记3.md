---
title: javaweb之mybatis基本实现
categories: java
---

### 前言

上次我实现了基本的springmvc，spring和hibernate的结合，实现了基本的怎删改查。因为现在流行的javaweb还有SSM，最后的这个M便是MyBatis，所以这次我实现了基本的基于MyBatis的增删改查。

### 实现

这次我没有使用mybatis-spring，只是在doa层保持了一个sessionFactory单例。对调用增删改查的方法，需要利用这个SessionFactory单例创建session，然后执行相应的操作方法或事务，最后再销毁session。

对于Mybatis，因为其虽说是个orm，但是需要编写sql执行操作。所以对应的，需要写wapper来将sql和接口方法对应起来定义。也就是说，在创建session之后，只有通过定义的wapper接口来执行数据库操作，其实也就是执行相应的sql语句。以下是重要的相关代码实现，包括doa接口和实现，wapper接口的定义，还有mybatis配置文件。

src/main/java/doa/mybatisWappers/IPersonWapper.java（wapper定义）

```
package doa.mybatisWappers;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import domin.Person;

public interface IPersonWapper {
	@Insert("insert into Person (id, name, sex) values (#{id}, #{name}, #{sex})")
    public int insertPerson(Person person);

	@Delete("delete from Person where name = #{name}")
    public int deletePerson(@Param("name") String name);
	
	@Select("select sex from Person where name = #{name}") 
    public String getPersonSexByName(@Param("name") String name);
    
	@Update("update Person set name = #{newName} where name = #{oldName}")
    public int setPersonName(@Param("oldName") String oldName, @Param("newName") String newName);
    
	@Select("select name from Person where id = #{id}")
    public String getNameById(@Param("id") Integer id);
}
```

src/main/java/doa/IMyBatisDoa.java（doa层接口）

```
package doa;

import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import domin.Person;

public interface IMyBatisDoa {
    public boolean insertPerson(Person person);

    public boolean deletePerson(String name);
	
    public String getPersonSexByName(String name);
    
    public String setPersonName(String oldName, String newName);
}
```

src/main/java/doa/MyBatisDoa.java（doa层实现函数）

```
package doa;

import java.io.IOException;
import java.io.InputStream;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Repository;

import doa.mybatisWappers.IPersonWapper;
import domin.Person;

@Repository
@Scope
public class MyBatisDoa implements IMyBatisDoa {
	
	private SqlSessionFactory sessionFactory;
	
	public MyBatisDoa() {
		this.sessionFactory = null;
		String resource = "mybatis-config.xml";
		InputStream inputStream;
		try {
			inputStream = Resources.getResourceAsStream(resource);
			sessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		} catch (IOException e2) {
			// TODO Auto-generated catch block
			e2.printStackTrace();
		}
	}

	public boolean insertPerson(Person person) {
		// TODO Auto-generated method stub
		try {
			  SqlSession session = sessionFactory.openSession();
			  IPersonWapper mapper = session.getMapper(IPersonWapper.class);
			  boolean res = mapper.insertPerson(person) != 0 ? true : false;
			  session.commit();
			  session.close();
			  return res;
		} catch (Exception ex) {
			
		}
		return false;
	}

	public boolean deletePerson(String name) {
		// TODO Auto-generated method stub
		try {
			  SqlSession session = sessionFactory.openSession();
			  IPersonWapper mapper = session.getMapper(IPersonWapper.class);
			  boolean res = mapper.deletePerson(name) != 0 ? true : false;
			  session.commit();
			  session.close();
			  return res; 
		} catch (Exception ex) {
			
		}
		return false;
	}

	public String getPersonSexByName(String name) {
		// TODO Auto-generated method stub
		try {
			  SqlSession session = sessionFactory.openSession();
			  IPersonWapper mapper = session.getMapper(IPersonWapper.class);
			  String res = mapper.getPersonSexByName(name);
			  session.commit();
			  session.close();
			  return res;
		} catch (Exception ex) {
			
		}
		return "err";
	}

	public String setPersonName(String oldName, String newName) {
		// TODO Auto-generated method stub
		try {
			  SqlSession session = sessionFactory.openSession();
			  IPersonWapper mapper = session.getMapper(IPersonWapper.class);
			  String res = mapper.setPersonName(oldName, newName) != 0 ? newName : oldName;
			  session.commit();
			  session.close();
			  return res;
		} catch (Exception ex) {
			
		}
        return "err";
	}
}
```

src/main/resources/mybatis-config.xml（mybatis配置文件）

```
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<environments default="development">
	  <environment id="development">
	    <transactionManager type="JDBC"></transactionManager>
		<dataSource type="POOLED">
		  <property name="driver" value="com.mysql.jdbc.Driver"/>
		  <property name="url" value="jdbc:mysql://127.0.0.1:3306/world?serverTimezone=UTC"/>
		  <property name="username" value="feo"/>
		  <property name="password" value="107409"/>
		</dataSource>
	  </environment>
	</environments>
	<mappers>
		<mapper class="doa.mybatisWappers.IPersonWapper" />
	</mappers>
</configuration>
```

### 总结

以上是实现mybatis的基本实现代码，没使用mybatis-spring，以后会试着使用。
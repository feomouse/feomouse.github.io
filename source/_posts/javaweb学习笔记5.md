---
title: javaweb之hibernate表关系创建
categories: 持久层技术
---

### 前言

在开发web应用后台的持久层时，ssh架构使用了hibernate作为与数据库交互的持久层ORM框架。而关系型数据库难的一点便是在表间会有一对一，一对多，多对多的表间关系，而这时映射到hibernate的对应的实体类设计的时候，也会遇到定义实体类间的一对一，一对多，多对多关系。所以这篇文章实现了基本的一对多和多对多关系，至于一对一关系，则到以后再实践。

### 数据库表设计

这次我的实践是用SQL DDL创建数据库，然后再根据现有的数据库来编写数据库表间对应的实体类关系。也就是说这次我没有使用生成工具，例如可以从数据库生成实体类定义，或者从实体类定义生成数据库表。而是根据前期例如实体关系图的设计，分开来定义数据库表和实体类，但是也是能正确地契合。

我分别创建了company表，apartment表，project表，a_p_relation表。

对于company表和apartment表，是一对多的关系。

对于apartment表和project表，是多对多的关系。而a_p_relation表则是apartment表和project表多对多关系的连接。

以下是具体的数据库创建SQL。

创建company表：

```
create table company (id INT PRIMARY KEY, name nvarchar(20), type nvarchar(10), location nvarchar(30));
```

创建apartment表：

```
create table apartment (id INT PRIMARY KEY, name nvarchar(15), location nvarchar(30), company_id INT, FOREIGN KEY (company_id) REFERENCES company(id));
```

创建project表：

```
create table project (id INT PRIMARY KEY, name nvarchar(20), start_time datetime, end_time datetime);
```

创建a_p_relation表（连接apartment和project的多对多关系）：

```
create table a_p_relation (apartmentid INT, projectid INT, PRIMARY KEY(apartmentid, projectid), FOREIGN KEY (apartmentid) REFERENCES apartment(id), FOREIGN KEY (projectid) REFERENCES project(id));
```

### 实体类设计

根据以上设计好的数据库表，我们可以照着设计hibernate对应的实体类。具体定义如下：

company表对应的实体类：

```
package domin;

import java.util.HashSet;
import java.util.Set;

import javax.persistence.CascadeType;
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.OneToMany;

import org.hibernate.annotations.GenericGenerator;

@Entity(name="company")
public class Company {
	@Id
	@GeneratedValue(generator="increment")
	@GenericGenerator(name="increment", strategy="increment")
	private Integer id;
	
	@Column(name="name")
	private String name;
	
	@Column(name="type")
	private String type;
	
	@Column(name="location")
	private String location;
	
	@OneToMany(mappedBy="company", cascade = {CascadeType.ALL})
	private Set<Apartment> apartments = new HashSet<Apartment>();
	
	public void setId(Integer id) {
	    this.id = id;	
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public void setType(String type) {
		this.type = type;
	}
	
	public void setLocation(String location) {
		this.location = location;
	}
	
	public void setApartments(Set<Apartment> apartments) {
		this.apartments = apartments;
	}
	
	public Integer getId() {
		return id;
	}
	
	public String getName() {
		return name;
	}
	
	public String getType() {
		return type;
	}
	
	public String getLocation() {
		return location;
	}
	
	public Set<Apartment> getApartments() {
		return apartments;
	}
	
	public void addApartment(Apartment apartment) {
		this.apartments.add(apartment);
	}
}
```

apartment表对应的实体类：

```
package domin;

import java.util.Set;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.JoinColumn;
import javax.persistence.JoinTable;
import javax.persistence.ManyToMany;
import javax.persistence.ManyToOne;

import org.hibernate.annotations.GenericGenerator;

@Entity(name="apartment")
public class Apartment {
	@Id
	@GeneratedValue(generator="increment")
	@GenericGenerator(name="increment", strategy="increment")
	private Integer id;
	
	@Column(name="name")
	private String name;
	
	@Column(name="location")
	private String location;
	
	@ManyToOne(fetch=FetchType.LAZY)
	@JoinColumn(name="company_id")
	private Company company;
	
	@ManyToMany
	@JoinTable(name="a_p_relation",
			joinColumns=@JoinColumn(name="apartmentid", referencedColumnName="id"),
			inverseJoinColumns=@JoinColumn(name="projectid", referencedColumnName="id"))
	private Set<Project> projects;
	
	public Apartment() {
		
	}
	
	public Apartment(String name, String location) {
		this.name = name;
		this.location = location;
	}
	
	public Apartment(Integer id, String name, String location, Company company, Set<Project> projects) {
		this.id = id;
		this.name = name;
		this.location = location;
		this.company = company;
		this.projects = projects;
	}
	
	public void setProjects(Set<Project> projects) {
		this.projects = projects;
	}
	
	public void addProjects(Project project) {
		this.projects.add(project);
	}
	
	public void setId(Integer id) {
		this.id = id;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public void setLocation(String location) {
		this.location = location;
	}
	
	public void setCompany(Company company) {
		this.company = company;
	}
	
	public Set<Project> getProjects() {
		return projects;
	}
	
	public Integer getId() {
		return id;
	}
	
	public String getName() {
		return name;
	}
	
	public String getLocation() {
		return location;
	}
	
	public Company getCompany() {
		return company;
	}
}
```

project表对应的实体类：

```
package domin;

import java.util.Date;
import java.util.Set;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import javax.persistence.ManyToMany;

import org.hibernate.annotations.GenericGenerator;

@Entity(name="project")
public class Project {
	@Id
	@GeneratedValue(generator="increment")
	@GenericGenerator(name="increment", strategy="increment")
	private Integer id;
	
	@Column(name="name")
	private String name;
	
	@Column(name="start_time")
	private Date startTime;
	
	@Column(name="end_time")
	private Date endTime;
	
	@ManyToMany(mappedBy="projects")
	private Set<Apartment> apartments;
	
	public Project() {
	    
	}
	
	public Project(String name, Date startTime, Date endTime) {
		this.name = name;
		this.startTime = startTime;
		this.endTime = endTime;
	}
	
	public void setApartments(Set<Apartment> apartments) {
	    this.apartments = apartments;	
	}
	
	public void addApartment(Apartment apartment) {
		this.apartments.add(apartment);
	}
	
	public void setId(Integer id) {
		this.id = id;
	}
	
	public void setName(String name) {
		this.name = name;
	}
	
	public void setStartTime(Date startTime) {
		this.startTime = startTime;
	}
	
	public void setEndTime(Date endTime) {
		this.endTime = endTime;
	}
	
	public Set<Apartment> getApartments() {
		return apartments;
	}
	
	public Integer getId() {
		return id;
	}
	
	public String getName() {
		return name;
	}
	
	public Date getStartTime() {
		return startTime;
	}
	
	public Date getEndTime() {
		return endTime;
	}
}
```

从以上的实体类定义代码可以看出，重点在于实体类如何定义才可以体现表与表间一对多或者是多对多的关系。

##### 一对多

对于department，company是一，所以实体类添加了除自身字段外体现关联的字段：

```
@OneToMany(mappedBy="company", cascade = {CascadeType.ALL})
private Set<Apartment> apartments = new HashSet<Apartment>();
```

对于company，department是多，所以实体类添加了除自身字段外体现关联的字段：

```
@ManyToOne(fetch=FetchType.LAZY)
@JoinColumn(name="company_id")
private Company company;
```

对于注解@OneToMany，参数mappedBy和cascade都是对多端准备的。也就是使实体类company知道多端是通过company字段和自身关联的，而且删除时连级删除。

而对于注解@ManyToOne，参数则表示可以懒加载，在获取一端，只有需要多端的数据时，才加载到内存中。而@JoinColumn则表示数据库表外键对应的字段。

##### 多对多

对于department和project表，双方都是多端，所以在设计实体类时，两端都是要维持对方的实体类列表，而且也都要添加@ManyToMany注解。

department实体：

```
@ManyToMany
@JoinTable(name="a_p_relation",
    joinColumns=@JoinColumn(name="apartmentid", referencedColumnName="id"),
    inverseJoinColumns=@JoinColumn(name="projectid", referencedColumnName="id"))
private Set<Project> projects;
```

project实体：

```
@ManyToMany(mappedBy="projects")
private Set<Apartment> apartments;
```

分析以上字段和注解的定义，可看出虽然双方都保持有对方的集合字段，但是在注解上，一方在添加基本的@ManyToMany外还要添加@JoinTable注解来表示数据库设计时表a_p_relation定义的双方的多对多关系的列字段和主键映射。

而另一方则只要一个@ManyToMany注解，但是要表明另一方实体类多对多的对应的字段。

### 持久层实现

在设计完实体类之后，便要实现持久层操作了。

src/main/java/doa/IHTableReDoa.java（持久层接口）：

```
package doa;

import java.util.List;
import java.util.Set;

import domin.Apartment;
import domin.Company;
import domin.Project;

public interface IHTableReDoa {
	public Set<String> getApartmentsNameByCompany(String companyName);
	
	public boolean addCompany(Company company);
	
	public boolean updateCompanyName(String oldName, String newName);
	
	public boolean deleteCompany(String companyName);
	
	public boolean addApartmentToCompany(Apartment apartment, String companyName);
	
	public boolean addProject(Project project, List<String> apartmentNames);
	
	public boolean attachProjectsToApartment(String apartmentName, List<String> projectsName);
}
```

src/main/java/doa/HTableReDoa.java（持久层实现）：

```
package doa;

import java.util.HashSet;
import java.util.List;
import java.util.Set;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.boot.MetadataSources;
import org.hibernate.boot.registry.StandardServiceRegistry;
import org.hibernate.boot.registry.StandardServiceRegistryBuilder;
import org.hibernate.resource.transaction.spi.TransactionStatus;
import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Repository;

import domin.Apartment;
import domin.Company;
import domin.Project;

@Repository
@Scope
public class HTableReDoa implements IHTableReDoa {
    private final StandardServiceRegistry registry;
    
    private final SessionFactory sessionFactory;
    
	public HTableReDoa() {
		registry = new StandardServiceRegistryBuilder().configure().build();
		
		sessionFactory = new MetadataSources(registry).buildMetadata().buildSessionFactory();
    }
	
	public Set<String> getApartmentsNameByCompany(String companyName) {
    	Session session = sessionFactory.openSession();
    	Set<String> res = new HashSet<String>();
    	
    	try {
        	session.beginTransaction();
        	Company c = session.createQuery("from company where name = :companyName", Company.class).setParameter("companyName", companyName).getSingleResult();

        	session.getTransaction().commit();
        	
        	for(Apartment ap : c.getApartments()) {
        		res.add(ap.getName());
        	}
    	}
    	
    	catch (Exception e) {
    		if ( session.getTransaction().getStatus() == TransactionStatus.ACTIVE
    				|| session.getTransaction().getStatus() == TransactionStatus.MARKED_ROLLBACK ) {
    			session.getTransaction().rollback();
    		}
    	}
    	
    	finally {
    		session.close();
        	return res;
    	}
	}
	
	public boolean addCompany(Company company) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
    	
    	try {
        	session.beginTransaction();
        	session.save(company);
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
        	return res != 0 ? true :false;
    	}
	}
	
	public boolean updateCompanyName(String oldName, String newName) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
    	
    	try {
        	session.beginTransaction();
        	res = session.createQuery("update company c set c.name = :newName where c.name = :oldName").
            		setParameter("newName", newName).
            		setParameter("oldName", oldName)
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
        	return res != 0 ? true :false;
    	}
	}
	public boolean deleteCompany(String companyName) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
    	
    	try {
        	session.beginTransaction();
        	res = session.createQuery("delete from company where name = :name").
            		setParameter("name", companyName)
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
        	return res != 0 ? true :false;
    	}
	}
	
	public boolean addApartmentToCompany(Apartment apartment, String companyName) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
    	
    	try {
        	session.beginTransaction();
        	Company company = session.createQuery("from company where name =:name", Company.class).setParameter("name", companyName).getSingleResult();
        	apartment.setCompany(company);
        	company.addApartment(apartment);
            res = (Integer) session.save(company);
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
        	return res != 0 ? true :false;
    	}
	}
	
	public boolean addProject(Project project, List<String> apartmentNames) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
        Set<Apartment> apartments = new HashSet<Apartment>();
        Apartment tempApartment = null;
    	
    	try {
        	session.beginTransaction();
            for (String aName : apartmentNames) {
            	Apartment apartment = session.createQuery("from apartment where name = :name", Apartment.class).
            		setParameter("name", aName).
            		getSingleResult();
            	
            	Set<Project> projects = new HashSet<Project>();

            	for (Project pro : apartment.getProjects()) {
            	    projects.add(pro);
            	}
                projects.add(project);
            	apartment.setProjects(projects);
            	apartments.add(apartment);
            	session.save(project);

            }
            project.setApartments(apartments);
            session.saveOrUpdate(project);
            
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
        	return res != 0 ? true :false;
    	}
	}
	
	public boolean attachProjectsToApartment(String apartmentName, List<String> projectsName) {
    	Session session = sessionFactory.openSession();
        Integer res = 0;
    	
    	try {
        	session.beginTransaction();
            Apartment apartment = session.createQuery("from apartment where name = :name", Apartment.class).
            	setParameter("name", apartmentName).
            	getSingleResult();
            
            for (String projectName : projectsName) {
            	Project pro = session.createQuery("from project where name = :name", Project.class).
            		setParameter("name", projectName).
            		getSingleResult();
            	
            	pro.addApartment(apartment);
            	
            	apartment.addProjects(pro);
            }
            res = (Integer) session.save(apartment);
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
        	return res != 0 ? true :false;
    	}	
	}
}
```

以上代码的重点还是在数据的增删改查上面。但是重要的是，对比之前实现的单表增删改查，这次我们需要思考和维护好代表表间关联的，而外添加的实体类字段。因为我在实践中，多次因为忘了维护实体列表字段，而导致之前多对多关系数据被覆盖。

### 总结

此篇文章实践了orm的一对多和多对多表间关系的实体映射实现，希望会有所帮助。
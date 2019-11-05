---
title: javaweb之spring-aop与log4j2的简单结合实现
categories: web技术
---

### 前言

web技术发展到现在，一般都是不会只满足于实现业务逻辑代码。对于项目的非功能性需求，比如可拓展，可修改，可测试性等方面的要求，导致现在有许多的创新技术产生。比如aop。

aop也就是面向切面编程。其概念与技术的产生是因为软件解耦的思想已经投入了生产开发领域，于是为了将软件主体的业务逻辑，也就是变动较大的一部分逻辑代码与变动较小的一部分逻辑代码分离，以满足软件的非功能性需求，产生了面向切面。

### 大体理解

可以这么理解。

比如现在有多个个服务类，每个服务类里面封装了某个用例的业务逻辑，包含了十几个函数。所以大体上几个用例包含的逻辑函数定义会有几十个，甚至更多。

此时，有一项新的需求到来。需要在每个用例实现的方法里添加一项工作。也就是说这项工作有很高的重复性。那么如果要实现这个新的需求，我们需要到全部几十函数里面一个一个地进行更改，而且会有大量的重复代码。在人工成本，后期维护成本，时间成本上，会有大量的浪费。

于是aop就在这时起作用了。其把这项高度重复的工作封装到一个类中，并在运行时，这部分重复的逻辑，在对应需要执行这项逻辑的类方法执行的适当的时候，会自动执行。于是，避免了大量的重复代码，保持了原来主体业务代码的整洁，可维护性也大大提高。

在市面上的教材中，都会有advice,advisor和pointcut这几个概念。有时候还是蛮难理解的。但是其也极度简要地描述了aop的技术要素。

* advice主要就代指我上面所说的新添加的需求逻辑的实现代码，以及执行时刻，也就是函数执行前，返回后，还是抛出错误后；
* pointcut主要代指需要添加这项新需求的类方法，也就是原来的主体服务代码方法；
* advisor主要代指将advice运用到pointcut的一个代理的概念；

所以aop的工作主要就是将原来的方法，需要添加的重复的方法两者以指定时刻结合的方式结合，实现代码编写的分离，但是运行逻辑的一体。

### 具体尝试

此次我尝试将spring-aop与log4j2日志框架结合起来，简单实现一个在执行方法后写日志的功能。

重点有几个：

1. log4j2的引入；
2. spring-aop的引入；
3. 切面类的编写；

以下是spring-aop需要引入的maven包：

```
  <!-- https://mvnrepository.com/artifact/org.springframework/spring-aop -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>5.2.0.RELEASE</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
  <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjweaver</artifactId>
      <version>1.9.4</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
  <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>aspectjrt</artifactId>
      <version>1.9.4</version>
  </dependency>
```

以下是log4j2日志框架需要引入的包：

```
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.12.1</version>
  </dependency>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.12.1</version>
  </dependency>
```

之后着手配置的编写。

对于spring-aop，需要的spring配置文件中添加一些指定的标签：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    ......
    ......
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="
        ......
        ......
        http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd"
        >

    <context:component-scan base-package="controllers"/>
    <context:component-scan base-package="domin"/>
    <context:component-scan base-package="services"/>
    <context:component-scan base-package="doa" />
    <context:component-scan base-package="aspects" /><!-- 我添加的包含的切面类的包-->
    
    <!-- 开启注解处理器 -->
    <context:annotation-config />
    
    <mvc:annotation-driven/>
    
    <aop:aspectj-autoproxy/><!-- 添加的切面代理-->
    
    ......
    ......
</beans>
```

以上是我基于原来的springmvc配置文件添加的配置标签内容；

对于log4j2，我在resources文件夹下添加了一个log4j2.xml的配置文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="warn" name="MyApp" packages="">
  <Appenders>
    <File name="MyFile" fileName="/logs/app.log">
      <PatternLayout>
        <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
      </PatternLayout>
    </File>
    <Async name="Async">
      <AppenderRef ref="MyFile"/>
    </Async>
  </Appenders>
  <Loggers>
    <Root level="info">
      <AppenderRef ref="Async"/>
    </Root>
  </Loggers>
</Configuration>
```

主要是定义了一个日志输入流地址-文件/logs/app.log，输出格式，还有对应的日志输出级别定义。

以下是我之前申明的程序的主体业务逻辑方法-getSex，也就是切面需要运用到的逻辑方法。

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
    
    ......
    ......
}

```

以下是对应的切面类。

```
package aspects;

import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class WriterName {
    protected static final Logger logger = LogManager.getLogger();
    
    @Pointcut("execution(* controllers.NameController.getSex(..))")
    private void addHeading() {}
    
    @After("addHeading()")
    public void returnRes() {
        logger.info("get sex operation");
    }
}
```

里面定义了pointcut和advice。需要注意的有几点：

* 对应切面类需要添加@Aspect和@Component注解（@Aspect标注为切面，@Component标注让spring可以注入）；
* 获取日志对象；

```
  protected static final Logger logger = LogManager.getLogger();
```

* @Pointcut定义切点，其参数申明将advice应用到哪个方法中，这里是getSex；
* @After为advice申明逻辑在函数内部具体的切入时间点，以及对应的执行逻辑，也就是getSex方法执行过后会调用切面里面定义的returnRes方法；

```        
  logger.info("get sex operation");
```

以上逻辑表示将get sex operation字符串输出到日志文件中。

### 总结

最后测试成功，也算是在aop编程领域入了门。之后也会继续学习的。
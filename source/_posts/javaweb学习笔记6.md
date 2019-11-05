---
title: javaweb之springmvc视图与控制器数据传输
categories: web技术
---

### 前言

在mvc模式的下进行web编程，控制器和视图的逻辑是分离的，也就是java代码并不会侵入到视图的模板代码，所以现在控制器承担的工作有：

* 接受客户端发送而来的数据；
* 为视图层设置数据模型
* 选择视图层返回

这也是现有典型mvc模式的流程。

### springmvc实现v层与c层的数据传递

简单地讲，现在springmvc框架下的控制器编写已经十分简化，而且实现上面三项已讨论的工作也十分简单。

1. 为控制器类添加标注@Controller声明其为控制器类；
2. 为需要返回视图的的控制器方法添加一个Model类型的参数，在控制器中将这个参数作为map类型的变量操作，设置数据，然后作为viewmodel数据传递给视图；
3. 自定义控制器方法接受客户端数据类型的ViewModel类(POJO数据类)； 
4. 为需要接收客户端传来数据的控制器方法添加@ModelAttribute注解的，ViewModel类型的参数，这个参数中的键值对便是客户端传来的数据；

至此，简单的springmvc控制器Controller与视图View（jsp或其他类型的模板视图文件）间的双向数据传递便可以进行了。

### 数据校验

对于客户端传来的数据，我们有时候需要校验其格式有效性。

以前我们可以在控制器中获取数据后直接校验，或者专门编写一个校验类来校验数据。但是现在这个工作可以委托给第三方了。比如hibernate-validator是一个很好的选择。

hibernate-validator依赖：

```
  ......
  <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>6.0.17.Final</version>
  </dependency>
  <dependency>
      <groupId>org.hibernate.validator</groupId>
      <artifactId>hibernate-validator-cdi</artifactId>
      <version>6.0.17.Final</version>
  </dependency>
  ......
```

那好处呢？最明显的便是一些简单的数据校验工作，我们在设计viewmodel类的时候便可以以注解的形式和数据类一起声明。

于是在控制器中，我们只需要对接受的viewmodel参数添加一个@Valid注解便可以实现验证；具体实现如下。

viewmodel类：

```
package domin.view;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

import org.hibernate.validator.constraints.Length;

public class MyViewModel {
    @NotNull
    private Integer id;
    @NotNull
    @Length(max=15, message="长度不可超15")
    @NotBlank
    private String name;
    @Length(max=50, message="长度不可超50")
    @NotBlank
    private String description;
    
    public MyViewModel() {
        
    }
    
    public MyViewModel(Integer id, String name, String description) {
        this.id = id;
        this.name = name;
        this.description = description;
    }
    
    public Integer getId() {
        return this.id;
    }
    
    public String getName() {
        return this.name;
    }
    
    public String getDescription() {
        return this.description;
    }
    
    public void setId(Integer id) {
        this.id = id;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public void setDescription(String description) {
        this.description = description;
    }
}
```

控制器类：

```
package controllers;

import javax.validation.Valid;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;

import domin.view.MyViewModel;

@Controller
public class ModelViewController {
    private MyViewModel doaViewModel;
    
    public ModelViewController() {
        this.doaViewModel = new MyViewModel();
    }
    
    @RequestMapping(value="/getModel")
    public String getModelView(Model model) {
        model.addAttribute("myModel", doaViewModel);
        
        return "ShowModel";
    }
    
    @RequestMapping(value="/eidtModel")
    public String updateModelView(Model model) {
        model.addAttribute("model", new MyViewModel());
        return "EditModel";
    }
    
    @RequestMapping(value="/addModel")
    public String addModelView(@Valid @ModelAttribute MyViewModel model, BindingResult bindingResult, Model models) {
        if(bindingResult.hasErrors()) {
            FieldError fieldError = bindingResult.getFieldError();
            models.addAttribute("errorCode", fieldError.getCode());
            models.addAttribute("errorName", fieldError.getObjectName());
            models.addAttribute("errorField", fieldError.getField());
            
            return "ErrorPage";
        }
        
        doaViewModel = model;
        
        return "redirect:/getModel";
    }
}
```

具体的验证实现在第三个方法addModelView中。我们还需要在方法参数中添加一个BindingResult类型的变量作为储存错误的地方。

如果验证出错，则可以对bindingResult变量进行操作。或者代码编写者自己编写错误处理逻辑。

以下是编写的视图文件：

EditModel.jsp

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
  <form:form modelAttribute="model" action="/springmvc-feo/addModel" method="post">
     <p>
        <label for="id">id:</label>
        <form:input id="id" path="id" />
     </p>
     <p>
        <label for="name">name:</label>
        <form:input id="name" path="name" />
      </p>
     <p>
        <label for="description">description:</label>
        <form:input id="description" path="description" />
     </p>
     <p>
        <input id="submit" type="submit" value="Edit" />
     </p>
  </form:form>
</body>
</html>
```

ShowModel.jsp

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
  <div>${myModel.id}</div>
  <div>${myModel.name}</div>
  <div>${myModel.description}</div>
</body>
</html>
```

ErrorPage.jsp

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
<title>Insert title here</title>
</head>
<body>
   <div>${errorCode}</div>
   <div>${errorName}</div>
   <div>${errorField}</div>
</body>
</html>
```

可见控制器返回的字符串是视图的文件名，里面的变量是el模板语言类型的变量。

el模板语言依赖
```
  <dependency>
      <groupId>javax.el</groupId>
      <artifactId>javax.el-api</artifactId>
      <version>3.0.1-b06</version>
  </dependency>
```

因为是在spring-mvc.xml文件里配置了：

spring-mvc.xml

```
......
  <bean id="viewResolver" 
    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/view/modelViews/"/>
    <property name="suffix" value=".jsp"/>
  </bean>
......
```

所以返回的字符串是可以和视图文件映射起来的。

像验证注解这些就不谈了。这篇文章只是简单地谈一下j2ee中springmvc的控制器和视图数据交互验证的实现。

### 总结

这篇文章可以让我们简单上手springmvc。以及简单的验证实现。
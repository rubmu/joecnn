---
title: Struts2 action 传参
date: 2013/12/26
tags: [java, struts]
---

表单传参数给action的几种方法和简单的验证，抛出异常。
  
### 一、参数通过类属性

```java
private String name;
private String age;
public String parm() {
    System.out.println("name : "+getName());
    System.out.println("age : "+getAge());
    return SUCCESS;
}
```

对应 URl "basePath/parm/parm?name=xxx&&age=12" ，structs会将对应的参数填入属性中。
  
### 二、通过实体类

```java
private user user;
public String  parm() {
    System.out.println("name : "+user.getName());
    System.out.println("age : "+user.getAge());
    return SUCCESS;
}  
```

定义实体类Model或DTO，在url中传入值 “basePath/parmModel/parm?user.name=xxx&&user.age=12”,structs会实例化Model，将参数传入。
  
### 三、通过modelDriven

1. 实现modeldriven接口
    `public class action5 extends ActionSupport implements ModelDriven<user>`
2. 重写 getModel 方法

```java
@Override 
public user getModel() {
    if (user == null) {
        user = new user();
    }
    return user;
}
```

其余与实体类传参相同（不常用） 
  
### 四、简单数据验证

```java
public String add() {
        if (name == null || name.length() <= 0 || name != "admin") {
            this.addFieldError("name", "holi shit !! what you enter for!!");
            this.addFieldError("name", "我去年买了个表"); 
            return ERROR;
        }
        return SUCCESS;
    }
```

在方法中使用 addFieldError ，将错误信息加入 valueStack 中，前台页面使用 OGNL 标签获取 error！

```xml
    <s:fielderror fieldName="name" /> 
    <s:property value="errors.name[0]"/> 
    <s:debug></s:debug>  
```

> 注意：在使用 ONGL 时，需要在顶部引用标签库。 `<%@ taglib uri="/struts-tags" prefix="s" %>`, 便签库文件在 struts-core 包的 META-INF  下的 struts-tags.tld  
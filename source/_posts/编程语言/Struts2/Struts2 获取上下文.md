---
title: Struts2 获取上下文
date: 2013/12/26
tags: [java, struts]
---

在 action 中获取上下文（request, response, session, application），主要有四种方法，前两种获取的为 Map<string, Object> 类型，后两种获取的为HttpContext 原类型。 
  
### 一、在 struts 容器中获取上下文 

```java
requestMap=(Map)ActionContext.getContext().get("request");
sessionMap=ActionContext.getContext().getSession();
applicationMap=ActionContext.getContext().getApplication();
```

可以将此获取下在构造函数中，获取后可以向容器中设置。

```java
requestMap.put("R1", "R1");
sessionMap.put("S1", "S1");
applicationMap.put("A1", "A1");  
```

在前台页面可以去 action context 中获取值：

```xml
    <s:property value="#request.R1"/> || <%= request.getAttribute("R1")%><br/> 
    <s:property value="#session.S1"/> || <%= session.getAttribute("S1")%><br/> 
    <s:property value="#application.A1" /> || <%= application.getAttribute("A1") %><br/>  
```

带“#”的OGNL 取的是 action context 的内容，而后一种是普通的 jsp 取值方法。 
  
### 二、使用 IoC ，让 struts 注入上下文（常用）

```java
public class action2 extends ActionSupport implements RequestAware,SessionAware,ApplicationAware {
    // 实现三个接口，当struts实例化action时，向其中注入 context上下文 
    @Override 
    public void setApplication(Map<String, Object> arg0) { 
        this.requestMap=arg0; 
    } 
    @Override 
    public void setSession(Map<String, Object> arg0) { 
        this.sessionMap=arg0; 
    } 
    @Override 
    public void setRequest(Map<String, Object> arg0) { 
        this.applicationMap=arg0; 
    }
}
```

用此方法的好处是，不必一一的获取上下文，依靠spring ioc的注入。
  
### 三、获取原始的HttpContext 

```java
private HttpServletRequest request = ServletActionContext.getRequest() ; 
private HttpSession session = request.getSession() ; 
private ServletContext application = session.getServletContext() ;  
```
  
### 四、IoC获取原始HttpContext 

```java
public class action4 extends ActionSupport implements ServletRequestAware  
    // 实现接口 ServletRequestAware ； 
    @Override 
    public void setServletRequest(HttpServletRequest arg0) { 
        request=arg0; 
        session=request.getSession(); 
        application=session.getServletContext(); 
    }  
```

以上为主要获取 HttpContext 上下文方法，常用IoC的方式以减少依赖。

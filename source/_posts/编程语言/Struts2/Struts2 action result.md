---
title: Struts2 action result
date: 2013/12/26
tags: [java, struts]
---

对 action 执行后返回的 result 
  
### （1） 常用几种 result type 
1. Dispatcher 拦截器，不配置type时 默认，相当于 transfer 服务器跳转。  
`<result type="dispatcher">/index.jsp</result>`  
  
2. Redirect 客户端跳转，相当于再次请求另外一个页面，在url中显示的是 xxx.jsp 
`<result type="redirect" name="xxx">index.jsp</result>`
3. Chain 跳转至 action，配置对应的 package 名字 和 action 名字 。 

```xml
<result type="chain" name="ccc">  
    <param name="actionName">action</param>
    <param name="namespace">/</param>
</result>  
```

4. RedirectAction ，客户端跳转至action。跳转后 url 显示的action 名字。 

```xml
<result type="redirectAction" name="r2">
    <param name="actionName">action</param> 
    <param name="namespace">/</param> 
</result>  
```

5. Stream 文字流，可用于Ajax返回纯文本，或者二进制流（下载文件）。 

```xml
<result type="stream" name="sss">  
    <param name="contentType">text/html</param> 
    <param name="inputName">inputStream</param> 
</result> 
```

在action中定义 inputStream , `InputStream inputStream =  new StringBufferInputStream("json");`  向页面直接输出 string。 
  
### （2）全局 result ：global-results 

1. 定义全局 result ，在此 package 中的 action 对此 result 均可访问。常用于错误、异常处理 

```xml
<global-results>  
    <result name="error">/Error.jsp</result> 
</global-results>  
```

2. 另外 package 要使用此全局 result ，需要继承自此 package        `<package name="module1" namespace="/" extends="action">`  


### （3） 动态 result 

1. result 取自 valuestack 值栈中 
`<result name="xxx">${result}</result>`  
2. 动态设置跳转页面参数，仅在 redirect 中使用，因为在 dispatcher、chain 为同个 request ，共享同一个valueStack。  
`<result name="rrr" type="redirect">/index.jsp?result=${result}</result>`  
改参数被塞入  stack context 中的 parameters  `<s:property value="#parameters.result"/>`

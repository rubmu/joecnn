---
title: Struts2 quickstart
date: 2013/12/26
tags: [java, struts]
---

1. 新建 web project .当修改project名时，要同时修改 properties -> myeclipse -> web -> ontext-root.不然发布后的文件名不会修改。 
2. 找到 struct2 -all ,打开 app 目录中（此为示例） ，解压任意一个，将 structs.xml 拷入项目。 
3. 将 \WEB-INF\lib 下引用的包拷入项目中  
4. 修改 web.xml ，将示例中的 web.xml 下的 filter 配置拷入。

```xml
    <filter> 
        <filter-name>struts2</filter-name> 
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-        class> 
    </filter> 
    <filter-mapping> 
        <filter-name>struts2</filter-name> 
        <url-pattern>/*</url-pattern> 
    </filter-mapping>  
```

5. 修改 structs.xml 配置，以下为最简单的配置，修改完后部署到 tomcat 上，访问 http://localhost:8080/myproject/helloworld 即可。 

```xml
 <package name="default"namespace="/"extends="struts-default"> 
     <default-action-ref name="index"/> 
         <action name="helloworld"> 
            <result> 
                /index.jsp 
            </result> 
        </action> 
</package>
```

namespace 表示访问路径解析，如设置/index ,则访问的时候也需要加入/index，如果namespace为空，表示所有包含action的均会被解析，用于处理其他namespace处理不到的请求. 
 
6. 为 structs 包添加源码查看和api 
 右键 structs2-core.jar -> properties -> 1. java source attachment 源码   struts-2.2.3.1-all/struts-2.2.3.1/src/core/src/main/java  
2. javadoc location api文档   struts-2.2.3.1-all/struts-2.2.3.1/docs/structs2-core/apidocs 
 
7. 开启 structs 开发模式，这样在修改配置后不需要 redeploy <constant name="struts.devMode"value="true"/>  
 
8. 为 structs.xml 添加提示 
 windows -> preferences -> xml catalog -> add -> type 选择 uri , key 填写 "http://struts.apache.org/dtds/struts-2.0.dtd"  
location 为 struct-core.jar 解压后中的 struct2.dtd 

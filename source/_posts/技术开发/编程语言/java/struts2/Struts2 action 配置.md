---
title: Struts2 action 配置
date: 2013/12/26
tags: [java, struts]
---

### 一、action中未定义对应的method时，默认调用execute方法。 
1. action 中定义 action1.execute() 方法，structs默认执行此方法（不建议） 

```xml
<package name="cq.action"namespace="/"extends="struts-default"> 
    <action name="index"class="cq.action.action1"> 
        <result name="success"> 
               /index.jsp 
        </result> 
    </action> 
</package>
```

2. action 实现 action 接口，实现 exectue() ， structs 也会执行此方法（不建议)

3. action  继承 actionSupport ，此父类中已经定义了许多方法 （推荐）  

### 二、配置对应的方法 
4. 可配置 action 对应的 method

```xml  
<action name="show"class="cq.action.action2"method="show"> 
    <result> 
        /show.jsp 
    </result> 
</action>  
```
 
### 三、使用动态调用action （DMI） 

```xml
<action name="show"class="cq.action.action2">  
    <result> 
        /show.jsp 
    </result> 
</action>  
```

调用时使用 basePath/show!method , '！'后面对于方法（弊端是无法对应多个result）。 
使用时需要打开动态调用配置：`<constant name="struts.enable.DynamicMethodInvocation"value="true"/>`
 
### 四、通配符

```xml
<action name="*_*"class="cq.action.{1}Action"method="{2}"> 
    <result> 
      /{1}_{2}.jsp 
    </result> 
</action>
```

使用 * 通配符，{1} 对应第一个 *，通配符好处是可以减少配置量，但要求命名规范，如上述配置，action的命名为NameAction，方法为Get, 
则调用的url为  /Name/Get，对应的view 为 Name_Get.jsp。 
 
如何配置出.net MVC 模式的通配符呢？ 
 
### 五、包含xml，将其它文件包含进 struts.xml 配置中，更加容易分模块管理 
`<include file="login.xml"/>`
 
### 六、默认 Action ，当 struts 找不到对应 action 时调用此 action。 
`<defaut-action-ref name="index"></defaut-action-ref>`

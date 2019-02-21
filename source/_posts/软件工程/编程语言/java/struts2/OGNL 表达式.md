---
title: OGNL 表达式
date: 2013/12/26
tags: [java]
---

ONGL 是用于将 ValueStack（值栈，即整个 action） 和 StackContext（上下文 context） 内容取出的语法。 
  
1. 访问值栈中属性 
    `<s:property value="userName"/>`  
2. 访问值栈中的类，类属性，聚合类

```xml
<s:property value="user"/>
<s:property value="user.userName"/>
<s:property value="cat.frien"/>
```

3. 访问方法

```xml
<s:property value="userName.length()"/>  //普通方法
<s:property value="cat.frien.getName()"/>  //类的方法
<s:property value="show()"/>  //Action 中的方法
```

4. 访问静态

```xml
<s:property value="@service.staticSerivce@show()"/>  //静态方法
<s:property value="@service.staticSerivce@STR"/>  //静态属性
<s:property value="@@max(3,10)"/>  //静态Math方法
```

5. 访问集合

```xml
<s:property value="users"/>  //访问 list=new ArrayList<T>()
<s:property value="users[0]"/>  //访问 list 单个元素
<s:property value="users.{userName}"/>  //List 属性集合
<s:property value="users.{userName}[0]"/> || <s:property value="users[0].userName"/>  // List属性
-------------------------------------------------------------------------------------------------
<s:property value="cats"/>  // set=new HashSet() ，Set是无序的，所以无法访问单个元素
-------------------------------------------------------------------------------------------------
<s:property value="dogs"/>  // map=new HashMap<string, Object>()
<s:property value="dogs.dog1"/> ||  <s:property value="dogs['dog1']"/>  //访问 Map 单个元素
<s:property value="dogs.keys"/>  // Map的所有 Key
<s:property value="dogs.values"/>  // Map 所有 value
<s:property value="dogs.size()"/>  // 容器大小
```

6. 投影，条件过滤集合 
    `<s:property value="users.{?#this.userName!=null}.{name}"/>`  // ?# 表达式，选择符合条件的内容，^# 第一个满足的，$#最后一个满足的
7. stack context 中的内容使用 #xx
     `<s:property value="#session.asdf"/>`

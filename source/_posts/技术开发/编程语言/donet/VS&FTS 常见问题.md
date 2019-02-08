---
title: VS&FTS 常见问题
date: 2013/07/12
tags: [c#]
---

* TFS工作区缓存问题
> 在本地 *C:\Users\Kass\AppData\Local\Microsoft\Team Foundation\4.0\Cache\VersionControl.config* 中缓存了本地文件夹到旧服务的一些映射关系,  
删除 **ServerInfo** 节点下内容，即可清除TFS工作区缓存。  

* TFS添加成员
> 在系统用户、用户组添加成员后，更新组策略  
     `gpupdate /force`  

* VS重置配置
> `devenv.exe /setup /resetuserdata /resetsettings`
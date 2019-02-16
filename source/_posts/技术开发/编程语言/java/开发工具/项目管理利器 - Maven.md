---
title: 项目管理利器 - Maven
date: 2017/03/08
tags: [java, ide]
---

### 一、什么是maven？
maven是基于项目对象模型(pom)，通过一小段描述信息来管理项目的构建、报告、文档的软件项目管理工具。  
**是一款跨平台的开源工具。**

### 二、为什么使用maven？
方便管理项目依赖的jar包，与打包构建项目package，等同于 .NET Nuget。

### 三、安装maven
> 由于maven是Java开发的，所以依赖于Jdk, 需要先安装Java环境，设置JAVA_HOME.

- 在 [apache maven](http://maven.apache.org/download.cgi) 官网下载程序包后解压。  
- 设置maven环境变量 `M2_HOME = D:\Java\apache-maven-3.5.2`
- 设置 `PATH = %M2_HOME%\bin`
- 验证maven环境： `mvn -v`

### 四、maven项目目录结构

```bash
#maven创建项目的目录结构
src  
 |- main  #源代码目录
	|- java  
		|-package  
 |- test  #单元测试目录
 |- resource #配置文件目录
 |- pom.xml #pom配置文件
```

创建maven项目时，可手动进行目录创建或者使用archetype插件进行生成  

```bash
# 按提示创建
mvn archetype:generate -DarchetypeCatalog=internal  

# 指定创建参数
mvn archetype:generate --DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.joshua.maven02 
			-DartifactId=maven02 
			-Dversion=0.0.1-SNAPSHOT
			-Dpackage=com.joshua.maven02
```

### 五、maven常用构建命令
```bash
mvn -v   #查看maven版本
mvn compile   #编译项目生成target文件
mvn test   #运行单元测试
mvn package   #打包项目
mvn clean   #清空target文件夹
mvn install   #将项目打包发布到本地仓库
mvn jetty:run  #运行插件
```

### 六、maven中的坐标和仓库
maven项目坐标：

- **groupId(项目名称)** 
- **artifactId(模块名称)**
- **version(版本号)**   

maven项目仓库

- **本地仓库**
- **远程仓库**  

当需要下载jar包时，先到本地仓库中搜索，如果未找到则到中心仓库中检索。
> 设置本地仓库路径：在 /m2_home/conf/settings.xml 文件中更改第54行 **localRepository** 选项
  设置镜像仓库：在/m2_home/conf/settings.xml文件中更改第146行 **mirrors** 选项。  
  阿里云镜像仓库：  
```xml
<mirror>
  <id>alimaven</id> 
  <name>aliyun maven</name> 
  <url>http://maven.aliyun.com/nexus/content/groups/public/</url> 
  <mirrorOf>central</mirrorOf> 
  </mirror>
```

### 七、maven构建周期
1. clean 清理项目
> - pre-clean 执行清理前的工作  
> - clean 清理上一次构建生成的所有文件  
> - post-clean 执行清理后的工作
2. default 构建项目（核心）
> - compile 编译项目  
> - test 运行单元测试  
> - package 打包项目  
> - install 注册到本地仓库  
3. site 生成项目站点
> - pre-site 生成站点前工作
> - site 生成站点
> - post-site 生成站点后工作
> - site-deploy 发布站点

### 八、在 eclipse 中使用 maven 插件
> eclipse3.5以后的已经默认集成了maven插件  

- 下载 [eclipse-maven-plugin](http://www.eclipse.org/m2e/m2e-downloads.html) 拷贝到/eclipse/dropins目录下。重启eclipse查看 ```window->preferences->maven``` 插件是否已经加载。
- maven 插件需要jdk支持，修改 /eclipse.ini 文件，添加-vm配置

```bash
-vm
D:\Java\jdk1.8.0_152\bin\javaw.exe
```

- 添加 eclipse jdk设置 `window->preferences->java->install JREs->add`
- 修改 maven 默认设置，并设置配置文件(修改了本地仓库和镜像仓库)
```bash
window->preferences->maven->installations->add
settings->user settings
```

### 九、pom.xml 说明示例
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <!-- 指定了当前pom的版本 -->
  <modelVersion>4.0.0</modelVersion>

  <!-- 项目名称 -->
  <groupId>com.joshua</groupId>
  <!-- 模块名称 -->
  <artifactId>maven01</artifactId>
  <!-- 版本号 
  		第一个0表示大版本号
  		第二个0表示分子版本号
  		第三个0表示小版本号
  		0.0.1-snapshot 快照版
  		alpha 内部测试版
  		beta 公测版
  		release 稳定版
  		GA 正式发布版  -->
  <version>0.0.1-SNAPSHOT</version>
  <!-- 打包方式  war zip 
	   pom 聚合模式/父项目使用 -->
  <packaging>jar</packaging>

  <!-- 项目描述名 -->
  <name>maven01</name>
  <!-- 项目地址 -->
  <url>http://maven.apache.org</url>

  <!-- 项目属性 -->
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <!-- 依赖列表 -->
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.10</version>
      <!-- 依赖范围，只在test范围内 -->
      <scope>test</scope>
      <!-- 设置依赖是否可选，默认为false，表示可继承，否则需要显示引用 -->
      <optional>false</optional>
      <!-- 排除依赖传递列表 -->
      <exclusions>
      	<exclusion>...</exclusion>
      </exclusions>
    </dependency>
  </dependencies>
  
  <!-- 依赖管理，不在本项目引用，在子项目中继承 -->
  <dependencyManagement>
  	<dependencies></dependencies>
  </dependencyManagement>
  
  <!-- 构建支持 -->
  <build>
  	<plugins>
  			<plugin>
  				<!-- 打包源代码插件 -->
  				<groupId>org.apache.maven.plugins</groupId>
  				<artifactId>maven-source-plugin</artifactId>
  				<version>2.4</version>
  				<!-- 在package阶段运行插件，运行方式为 jar-no-fork -->
  				<executions>
  					<execution>
  						<phase>package</phase>
  						<goals>
  							<goal>jar-no-fork</goal>
  						</goals>
  					</execution>
  				</executions>
  			</plugin>
  	</plugins>
  </build>
  
  <!-- 继承的父模块 -->
  <parent>...</parent> 
  <!-- 聚合多模块同时编译 -->
  <modules>
	<module>../moven02</module>
  </modules>
</project>
```
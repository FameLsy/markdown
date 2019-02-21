---
title: maven(4) 生命周期
date: 2019-02-21 00:00:04
tags: 
- maven 仓库
categories:
- maven
---

# 构建生命周期
要理解Maven的生命周期，必须知道以下三个概念
1. 构建生命周期
2. 构建阶段
3. 插件目标

以上3个概念，基本上是has-a的关系,如构建生命周期由多个构建阶段组成;

除此之外，还需要了解下不会在命令行调用的阶段

## 构建生命周期

Maven 有3个内置的生命周期
1. default: 处理项目部署
2. clean：处理项目的清理
3. site：处理项目站点文档的创建  

## 构建阶段 
构建生命周期中的每一个都由不同的构建阶段列表定义，其中构建阶段表示生命周期中的阶段  

## 插件目标
构建阶段由零个、一个或多个插件目标组成，表示特定任务  
如, *clean* 和 *package*为构建阶段， *dependency:copy-dependencies*是一个插件目标
```
mvn clean dependency:copy-dependencies package
```
执行此操作，相当于先执行 *clean*阶段，然后在执行 *dependency:copy-dependencies*目标，最后再执行 *package*阶段。

## 不会再命令行调用的某个阶段

此阶段是不会在命令行调用:以连字符（pre-*，post- *或process- *）命名的阶段通常不会从命令行直接调用

# 设置项目使用构建生命周期
## 打包package* 
在 *< packaging >*元素可以设置如*jar*、*war*、*ear*、*pom*等值，没有指定，默认为 *jar*。
每一个包都包含一个绑定到特定阶段的目标列表，如 *jar*包将绑定以下目标以构建生命周期的阶段

|(阶段)Phase|	（插件目标）plugin:goal|
|--|--|
|process-resources|	resources:resources|
|compile|	compiler:compile|
|process-test-resources|	resources:testResources|
|test-compile|	compiler:testCompile|
|test|	surefire:test|
|package|	jar:jar|
|install| install:install|
|deploy|	deploy:deploy|

## 插件
除了像 *package*这样的默认绑定目标，在项目中配置插件是第二种向阶段添加目标的方式。  
插件就是一些 *artifacts*,用于提供目标,且插件可以具有一个或多个目标.  
如 *Compiler*插件，有两个目标 
1. *compile* : 编译主代码
2. *testCompile* ：编译测试源码

如下，设置一个 *modello*插件  
该插件会将 *modello:java* 目标默认绑定到 *generate-sources*阶段
```xml
...
 <plugin>
   <groupId>org.codehaus.modello</groupId>
   <artifactId>modello-maven-plugin</artifactId>
   <version>1.8.1</version>
   <executions>
     <execution>
       <configuration>
         <models>
           <model>src/main/mdo/maven.mdo</model>
         </models>
         <version>4.0.0</version>
       </configuration>
       <goals>
         <goal>java</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```
如果需要自己指定阶段  
*display* 插件会在 *process-test-resources*阶段执行目标 *display:time*
```
...
 <plugin>
   <groupId>com.mycompany.example</groupId>
   <artifactId>display-maven-plugin</artifactId>
   <version>1.0</version>
   <executions>
     <execution>
       <phase>process-test-resources</phase>
       <goals>
         <goal>time</goal>
       </goals>
     </execution>
   </executions>
 </plugin>
...
```
## 执行顺序
多个目标绑定到特定阶段，则使用的顺序是首先执行打包(package)中的目标，然后执行POM中配置的目标

# 具体生命周期

## clean生命周期

|构建阶段|	描述|
|--|--|
|pre-clean|	在实际项目清理之前执行所需的过程|
|clean|	删除上一个版本生成的所有文件|
|post-clean|	执行完成项目清理所需的过程|

## default生命周期

|构建阶段|	描述|
|--|--|
|validate|	验证项目是否正确，并提供所有必要信息。|
|initialize|	初始化构建状态，例如设置属性或创建目录。|
|generate-sources|	生成任何包含在编译中的源代码。|
|process-sources|	处理源代码，例如过滤任何值。|
|generate-resources|	生成包含在包中的资源。|
|process-resources|	将资源复制并处理到目标目录，准备打包。|
|compile|	编译项目的源代码。|
|process-classes|	从编译中对生成的文件进行后处理，例如对Java类进行字节码增强。|
|generate-test-sources|	生成任何包含在编译中的测试源代码。|
|process-test-sources|	处理测试源代码，例如过滤任何值。|
|generate-test-resourcess|	创建用于测试的资源。|
|process-test-resources|	将资源复制并处理到测试目标目录中。|
|test-compile|	将测试源代码编译到测试目标目录中|
|process-test-classes|	对来自测试编译的生成文件进行后处理，例如对Java类进行字节码增强。适用于Maven 2.0.5及以上版本。|
|test|	使用合适的单元测试框架运行测试。这些测试不应要求打包或部署代码。|
|prepare-package|	在实际包装之前执行准备包装所需的任何操作。这通常会导致包的解包，处理版本。（Maven 2.1及以上）|
|package|	获取已编译的代码并将其打包为可分发的格式，例如JAR。|
|pre-integration-test|	在执行集成测试之前执行所需的操作。这可能涉及诸如设置所需环境之类的事情。|
|integration-test|	如有必要，将程序包处理并部署到可以运行集成测试的环境中。|
|post-integration-test|	执行集成测试后执行所需的操作。这可能包括清理环境。|
|verify|	运行任何检查以验证包是否有效并符合质量标准。|
|install|	将软件包安装到本地存储库中，以便在本地用作其他项目的依赖项。|
|deploy|	在集成或发布环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。|

## site生命周期


|构建阶段|	|
|--|--|
|pre-site|	在实际项目站点生成之前执行所需的过程|
|site|	生成项目的站点文档|
|post-site|	执行完成站点生成所需的进程，并准备站点部署|
|site-deploy|	将生成的站点文档部署到指定的Web服务器|

# 内置生命周期绑定
构建阶段默认绑定的目标

## clean生命周期绑定

|Phase|	plugin:goal|
|--|--|
|clean|	clean:clean|

## default生命周期绑定  

packaging *ejb / ejb3 / jar / par / rar / war*

|Phase|	plugin:goal|
|--|--|
|process-resources|	resources:resources|
|compil|e	compiler:compile|
|process-test-resources|	resources:testResources|
|test-compile|	compiler:testCompile|
|test|	surefire:test|
|package|	ejb:ejb or ejb3:ejb3 or jar:jar or par:par or rar:rar or war:war|
|install|	install:install|
|deploy|	deploy:deploy|

packaging *ear*

|Phase|	plugin:goal|
|--|--|
|generate-resources|	ear:generate-application-xml|
|process-resources|	resources:resources|
|package|	ear:ear|
|install|	install:install|
|deploy|	deploy:deploy|

Packaging *maven-plugin*


|Phase|	plugin:goal|
|--|--|
|generate-resources|	plugin:descriptor|
|process-resources|	resources:resources|
|compile|	compiler:compile|
|process-test-resources|	resources:testResources|
|test-compile|	compiler:testCompile|
|test|	surefire:test|
|package|	jar:jar and plugin:addPluginArtifactMetadata|
|install|	install:install|
|deploy|	deploy:deploy|

Packaging *pom*

|Phase|	plugin:goal|
|--|--|
|package||	
|install|	install:install|
|deploy|	deploy:deploy|

## site生命周期绑定

|Phase|	plugin:goal|
|--|--|
|site|	site:site|
|site-deploy|	site:deploy|
---
title: maven
tags: 
- maven
categories:
- maven
---

# 安装
[MAVEN下载地址](http://maven.apache.org/download.cgi)

maven下载有src(源码版本)和bin(二进制版本)，选择bin版本
>注意：  
>Maven依赖JDK

**解压**
```
sudo tar xzvf apache-maven-3.6.0-bin.tar.gz -C /opt
```

**配置**
```
 vim ~/.bashrc
 ```
添加
```
export M2_HOME=/opt/apache-maven-3.6.0
export PATH=${M2_HOME}/bin:$PATH
```

**测试**
```
mvn --version
```
# MAVEN 目录

|目录名|说明|
|--|--|
|bin|mvn相关脚本|
|boot|只包含plexus-classworlds文件,类加载器框架|
|conf|配置文件|
|lib|JAVA类库文件|
|LICENSE|*|
|NOTICE|*|
|README.txt|*|


# ~/.m2 目录
所有Maven构件都被存储到该仓库中。

# 配置
**全局范围**
```
M2_HOME/conf/settings.xml
```
**用户范围**
```
~/.m2/settings.xml
```
# 项目目录结构

|目录|说明|
|--|--|
|pom.xml|包含此项目的项目对象模型|
|$ {basedir} / src / main / java|应用程序源|
|$ {basedir} / src / test / java|测试源|
|$ {basedir} / target / classes|编译后的文件|
# 编译应用程序源
```
mvn compile
```
>注意：  
>编译时可能失败，错误信息如下  
>[ERROR] Source option 5 is no longer supported. Use 6 or later.  
>[ERROR] Target option 1.5 is no longer supported. Use 1.6 or later.  
>原因是maven默认的maven-compiler-plugin编译器版本过低

**修改编译器版本**
```xml
<project
 ……
<build>
  <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <!-- since 2.0 -->
        <version>3.7.0</version>
        <configuration>
            <!-- use the Java 8 language features -->
            <source>1.8</source>
            <!-- want the compiled classes to be compatible with JVM 1.8 -->
            <target>1.8</target>
            <!-- The -encoding argument for the Java compiler. -->
            <encoding>UTF8</encoding>
        </configuration>
    </plugin>
  </plugins>
</build>

</project>
```
或者
```xml
<project
……
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>
</project>
```



# pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>my-app</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

|元素|说明|
|--|--|
|project| pom.xml的顶级元素|
|modelVersion|POM使用的对象模型的版本,很少变化|
|groupId|创建项目的组织或组|
|artifactId |项目生成的artifact(通常是JAR文件)的唯一名称|
|packaging|指示aritfact要使用的包类型,默认jar|
|version|aritfact的版本，SNAPSHOT表示开发状态|
|name|项目显示名称，通常用于Maven生成的文档中|
|url|指示可以找到项目站点的位置。这通常用于Maven生成的文档中|
|description|项目的基本描述。这通常用于Maven生成的文档中|

**编译并执行测试源**
```
mvn test
```
**编译但不执行测试源**
```
mvn test-compile
```

# 制作和安装JAR
**制作JAR**
```
mvn package
```
生产的JAR文件位于 *$ {basedir} / target* 目录下

**maven安装**
```
mvn install
```
默认会安装到本地存储位置*$ {user.home} /.m2 / repository*
# 插件使用方式
**例**
```xml
<project
...
<build>
  <plugins>
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.5</source>
        <target>1.5</target>
      </configuration>
    </plugin>
  </plugins>
</build>
...
</project>
```
在 < configuration > 中给定参数
# 向JAR中添加资源
**添加项目资源**  
```
$ {basedir} / src / main / resources
```
**添加测试资源**
```
$ {basedir} / src / test / resources
```

# 过滤资源文件
目的：让资源文件包含只能在构建时提供的值  
方法：使用 *$ { < property name > }*获取定义的属性  
定义的属性可以是：
1. pom.xml中定义的值
2. setting.xml中定义的值
3. 外部属性文件定义的值
4. 系统属性

**首先，在复制时让maven过滤资源**  
该目录下的资源会被过滤，通过 *$ { < property name > }*获取到值。
```
<project>
...
<build>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
...
</project>
```
**创建application.properties资源文件**
```
src/main/resources/META-INF/application.properties
```
**获取POM某些默认的值(无需定义)**  
$ { project.name }指的是项目的名称  
$ { project.version }指的是项目的版本  
```
# application.properties
application.name=${project.name}
application.version=${project.version}
```
**复制和过滤资源的构建生命周期阶段**
```
mvn process-resources
```
**查看target/classes下的application.properties**
```
#pplication.properties
application.name=my-app
application.version=1.0-SNAPSHOT
```
**引用外部文件中定义的属性**  
创建 *src / main / filters / filter.properties*  
```
# filter.properties
my.filter.value=hello!
```
引用外部文件
```
...
 <build>
    <filters>
      <filter>src/main/filters/filter.properties</filter>
    </filters>
...
```
在application.properties引用属性
```
# application.properties
message=${my.filter.value}
```

**在pom.xml种定义属性**
```
<project>
...
 <properties>
    <my.filter.value>hello</my.filter.value>
  </properties>
...
</project>
```

**获取系统属性或者-D参数定义的属性**
```
# application.properties
java.version=${java.version}
command.line.prop=${command.line.prop}
```
command.line.prop在命令行上定义
```
mvn process-resources "-Dcommand.line.prop=hello again"
```

# 外部依赖
```
<project>
...
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  ...
</project>
```
至少定义4个内容：*< groupId >*、*< artifactId >*、*< version >*、*< scope >*  
**Maven在哪里引用依赖关系？**  
1. Maven查找的本地存储库（$ {user.home} /.m2 / repository是默认位置）以查找所有依赖项
2. 远程仓库：http://repo.maven.apache.org/maven2/

# 远程仓库部署JAR
pom.xml文件(配置存储库URL)
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>jar</packaging>
 
  <name>Maven Quick Start Archetype</name>
  <url>http://maven.apache.org</url>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.apache.codehaus.plexus</groupId>
      <artifactId>plexus-utils</artifactId>
      <version>1.0.4</version>
    </dependency>
  </dependencies>
 
  <build>
    <filters>
      <filter>src/main/filters/filters.properties</filter>
    </filters>
    <resources>
      <resource>
        <directory>src/main/resources</directory>
        <filtering>true</filtering>
      </resource>
    </resources>
  </build>
  <!--
   |
   |
   |
   -->
  <distributionManagement>
    <repository>
      <id>mycompany-repository</id>
      <name>MyCompany Repository</name>
      <url>scp://repository.mycompany.com/repository/maven2</url>
    </repository>
  </distributionManagement>
</project>
```
 setting.xml文件(配置连接到存储库的身份验证信息)
 ```xml
 <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  ...
  <servers>
    <server>
      <id>mycompany-repository</id>
      <username>jvanzyl</username>
      <!-- Default value is ~/.ssh/id_dsa -->
      <privateKey>/path/to/identity</privateKey> (default is ~/.ssh/id_dsa)
      <passphrase>my_key_passphrase</passphrase>
    </server>
  </servers>
  ...
</settings>
```
# 创建文档
```
mvn archetype:generate \
  -DarchetypeGroupId=org.apache.maven.archetypes \
  -DarchetypeArtifactId=maven-archetype-site \
  -DgroupId=com.mycompany.app \
  -DartifactId=my-app-site
```
# 构建其他项目类型
```
mvn archetype:generate \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-webapp \
    -DgroupId=com.mycompany.app \
    -DartifactId=my-webapp
```
这将创建一个名为my-webapp的目录，其中包含以下项目描述符：
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>my-webapp</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
 
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
 
  <build>
    <finalName>my-webapp</finalName>
  </build>
</project>
```
注意< packaging >元素,告诉Maven构建为WAR
```
mvn package
```
将会生成 *target / my-webapp.war*

# 一次性构建多个项目
**在两个项目的上级目录添加pom.xml**
```
+- pom.xml
+- my-app
| +- pom.xml
| +- src
|   +- main
|     +- java
+- my-webapp
| +- pom.xml
| +- src
|   +- main
|     +- webapp
```
输入以下内容
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.mycompany.app</groupId>
  <artifactId>app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>my-app</module>
    <module>my-webapp</module>
  </modules>
</project>
```
webapp依赖JAR，所以将它添加到my-webapp / pom.xml
```xml
  ...
  <dependencies>
    <dependency>
      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>
    </dependency>
    ...
  </dependencies>
  ```
my-app/pom.xml和my-webapp/pom.xml均添加 < parent >元素
```xml
<project 
...
  <parent>
    <groupId>com.mycompany.app</groupId>
    <artifactId>app</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
...
</project>
  ```
执行
```
mvn verify
```
# 构建生命周期
**构建生命周期**  
Maven 有3个内置的生命周期
1. default: 处理项目部署
2. clean：处理项目的清理
3. site：处理项目站点文档的创建  

**构建阶段**  
构建生命周期中的每一个都由不同的构建阶段列表定义，其中构建阶段表示生命周期中的阶段  
如，default包含以下阶段

|阶段(phases)|说明|
|--|--|
|validate| - 验证项目是否正确并且所有必要信息都可用|
|compile| - 编译项目的源代码|
|test| - 使用合适的单元测试框架测试编译的源代码。这些测试不应要求打包或部署代码
|package| - 获取已编译的代码并将其打包为可分发的格式，例如JAR。
|verify| - 对集成测试结果进行任何检查，以确保满足质量标准
|install| - 将软件包安装到本地存储库中，以便在本地用作其他项目的依赖项
|deploy| - 在构建环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。

这些构建阶段会按顺序执行
```
mvn install
```
该命令会在执行 *install* 前，执行其前面的构建阶段。

**插件目标**  
构建阶段由零个、一个或多个插件目标组成，表示特定任务  
如, *clean* 和 *package*为构建阶段， *dependency:copy-dependencies*是一个插件目标
```
mvn clean dependency:copy-dependencies package
```
执行此操作，相当于先执行 *clean*阶段，然后在执行 *dependency:copy-dependencies*目标，最后再执行 *package*阶段。

**不会再命令行调用的某个阶段**
1. 以连字符（pre-*，post- *或process- *）命名的阶段通常不会从命令行直接调用

# 设置项目使用构建生命周期
**打包package**  
在 *< packaging >*元素可以设置如*jar*、*war*、*ear*、*pom*等值，没有指定，默认为 *jar*。
每一个包都包含一个绑定到特定阶段的目标列表，如 *jar*包将绑定以下目标以构建生命周期的阶段

|(阶段)Phase|	（插件目标）plugin:goal
|--|--|
process-resources|	resources:resources
compile|	compiler:compile
process-test-resources|	resources:testResources
test-compile|	compiler:testCompile
test|	surefire:test
package|	jar:jar
install| install:install
deploy|	deploy:deploy

**插件**  
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
**执行顺序**  
多个目标绑定到特定阶段，则使用的顺序是首先执行打包(package)中的目标，然后执行POM中配置的目标

# 具体生命周期

**clean生命周期**  

构建阶段|	描述
|--|--|
pre-clean|	在实际项目清理之前执行所需的过程
clean|	删除上一个版本生成的所有文件
post-clean|	执行完成项目清理所需的过程

**default生命周期**

构建阶段|	描述
|--|--|
validate|	验证项目是否正确，并提供所有必要信息。
initialize|	初始化构建状态，例如设置属性或创建目录。
generate-sources|	生成任何包含在编译中的源代码。
process-sources|	处理源代码，例如过滤任何值。
generate-resources|	生成包含在包中的资源。
process-resources|	将资源复制并处理到目标目录，准备打包。
compile|	编译项目的源代码。
process-classes|	从编译中对生成的文件进行后处理，例如对Java类进行字节码增强。
generate-test-sources|	生成任何包含在编译中的测试源代码。
process-test-sources|	处理测试源代码，例如过滤任何值。
generate-test-resourcess|	创建用于测试的资源。
process-test-resources|	将资源复制并处理到测试目标目录中。
test-compile|	将测试源代码编译到测试目标目录中
process-test-classes|	对来自测试编译的生成文件进行后处理，例如对Java类进行字节码增强。适用于Maven 2.0.5及以上版本。
test|	使用合适的单元测试框架运行测试。这些测试不应要求打包或部署代码。
prepare-package|	在实际包装之前执行准备包装所需的任何操作。这通常会导致包的解包，处理版本。（Maven 2.1及以上）
package|	获取已编译的代码并将其打包为可分发的格式，例如JAR。
pre-integration-test|	在执行集成测试之前执行所需的操作。这可能涉及诸如设置所需环境之类的事情。
integration-test|	如有必要，将程序包处理并部署到可以运行集成测试的环境中。
post-integration-test|	执行集成测试后执行所需的操作。这可能包括清理环境。
verify|	运行任何检查以验证包是否有效并符合质量标准。
install|	将软件包安装到本地存储库中，以便在本地用作其他项目的依赖项。
deploy|	在集成或发布环境中完成，将最终包复制到远程存储库以与其他开发人员和项目共享。

**site生命周期**


构建阶段|	描述
|--|--|
pre-site|	在实际项目站点生成之前执行所需的过程
site|	生成项目的站点文档
post-site|	执行完成站点生成所需的进程，并准备站点部署
site-deploy|	将生成的站点文档部署到指定的Web服务器

# 内置生命周期绑定
构建阶段默认绑定的目标

**clean生命周期绑定**

Phase|	plugin:goal
|--|--|
clean|	clean:clean

**default生命周期绑定**  

packaging *ejb / ejb3 / jar / par / rar / war*

Phase|	plugin:goal
|--|--|
process-resources|	resources:resources
compil|e	compiler:compile
process-test-resources|	resources:testResources
test-compile|	compiler:testCompile
test|	surefire:test
package|	ejb:ejb or ejb3:ejb3 or jar:jar or par:par or rar:rar or war:war
install|	install:install
deploy|	deploy:deploy

packaging *ear*

Phase|	plugin:goal
|--|--|
generate-resources|	ear:generate-application-xml
process-resources|	resources:resources
package|	ear:ear
install|	install:install
deploy|	deploy:deploy

Packaging *maven-plugin*


Phase|	plugin:goal
|--|--|
generate-resources|	plugin:descriptor
process-resources|	resources:resources
compile|	compiler:compile
process-test-resources|	resources:testResources
test-compile|	compiler:testCompile
test|	surefire:test
package|	jar:jar and plugin:addPluginArtifactMetadata
install|	install:install
deploy|	deploy:deploy

Packaging *pom*

Phase|	plugin:goal
|--|--|
package||	
install|	install:install
deploy|	deploy:deploy

**site生命周期绑定**

Phase|	plugin:goal
|--|--|
site|	site:site
site-deploy|	site:deploy
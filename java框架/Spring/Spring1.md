---
title: Spring(1)：装配bean
tgas: 
- java基础
categories: 
- Spring 教程
---
# 自动装配
- 具体步骤:
 1）普通的接口
2）实现接口的类，使用@Component注解，告诉Spring需要为其创建bean（应该叫做创建组件类）
3）开启组件扫描，为组件类创建bean
4）通过@Autowried注解，自动注入到成员变量或方法参数中

示例代码:
接口1：
```java
/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1实现，使用@Component注解，表示组件类，会在spring 中创建bean
```java
/**
 * CompactDisc实现类
 */
@Component
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("sgtPeppers的sing方法");
    }
}
```
接口2
```java
/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口2实现

在构造器方法上使用了@Autowired标注，参数CompactDisc将由spring 中的bean来自动注入
```java
@Component
public class CDPlayer implements  MediaPlayer{
    private CompactDisc compactDisc;

    @Autowired
    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }
    public void play() {
        System.out.println("播放器开始播放");
        compactDisc.sing();
    }
}
```
配置类

@Configuration 注解：表明该类为注解类

@ComponentScan注解：启动组件扫描功能，没有参数的@ComponentScan,默认扫描配置类同包下的组件
```java
@Configuration
@ComponentScan
public class JavaConfig {

}
```
ok,以上几步，一个Spring的简单自动装配完成，如何测试？

CompactDiscTest为一个测试类

@RunWith(StringJunit4ClassRunner.class): 使用SpringJunit4ClassRunner.class，可以在测试开始的时候自动创建Spring的应用上下文

@ContextConfiguration: 表示加载配置，有几种加载的方式，这里，选用的是加载配置类。

@Autowired : 当变量上修饰了这个注解时，Spring会自动将创建号的bean赋值过去。
```java
package com.spring.action;

import com.spring.action.autoconfig.CDPlayer;
import com.spring.action.autoconfig.CompactDisc;
import com.spring.action.autoconfig.JavaConfig;
import com.spring.action.autoconfig.MediaPlayer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

//使用SpringJUnit4ClassRunner，可以在测试开始的时候自动创建Spring的应用上下文
@RunWith(SpringJUnit4ClassRunner.class)
//导入配置
@ContextConfiguration(classes = JavaConfig.class)
public class AutoconfigTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;
    @Test
    public void play() {
        mediaPlayer.play();
    }
}
```
测试结果：可以看见，成功运行了sgtPeppers的实现方法
```
WARNING: Please consider reporting this to the maintainers of org.springframework.cglib.core.ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
播放器开始播放
sgtPeppers的sing方法
```
# 通过Java代码装配bean
- 具体步骤：
1）普通的接口
2）实现接口的类，该类为需要装配到spring bean中
3）配置类，由该类定义bean
示例代码：
接口1：（普通的接口）
```java
package com.spring.action.javaconfig;

/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1实现：（普通的类）
```java
package com.spring.action.javaconfig;

/**
 * CompactDisc实现类
 */
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("sgtPeppers的sing方法");
    }
}
```
接口2：（普通的接口）
```java
package com.spring.action.javaconfig;

/**
 * 媒体播放器
 */
public interface MediaPlayer {
    void play(); //一个播放方法
}
接口2实现：（在构造方法使用了@Autowired来自动注入CompactDisc参数）

package com.spring.action.javaconfig;

import org.springframework.beans.factory.annotation.Autowired;

public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }
    public void play() {
        System.out.println("播放器开始播放");
        compactDisc.sing();
    }
}
```
配置类

@Configuration注解表明其为配置类

通过@Bean，将方法返回的实例作为Spring bean保存

在第二个方法中，@Bean 注解的方法会自动将bean注入到参数中。
```java

package com.spring.action.javaconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
public class JavaConfig {
    //为sgtPeppers创建bean
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPeppers();
    }
    //为DCPlayer创建bean
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```
测试类：
```java
package com.spring.action;

import com.spring.action.javaconfig.CompactDisc;
import com.spring.action.javaconfig.JavaConfig;
import com.spring.action.javaconfig.MediaPlayer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

//使用SpringJUnit4ClassRunner，可以在测试开始的时候自动创建Spring的应用上下文
@RunWith(SpringJUnit4ClassRunner.class)
//导入配置
@ContextConfiguration(classes = JavaConfig.class)
public class JavaconfigTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;
    @Test
    public void play() {
        mediaPlayer.play();
    }
}
```
测试结果
```
WARNING: Please consider reporting this to the maintainers of org.springframework.cglib.core.ReflectUtils$1
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
播放器开始播放
sgtPeppers的sing方法
```
# XML装配bean
- 具体步骤:
1）普通的接口
2）实现接口的类，该类需要装配到spring bean中
3）xml配置文件
示例代码:
接口1：

package com.spring.action.xmlconfig;
```java
/**
 * 一个光盘接口
 */
public interface CompactDisc {
    void sing();//光盘的sing功能
}
```
接口1实现
```java
package com.spring.action.xmlconfig;

/**
 * CompactDisc实现类
 */
public class SgtPeppers implements CompactDisc {
    public void sing() {
        System.out.println("sgtPeppers的sing方法");
    }
}
```
接口2
```java
package com.spring.action.xmlconfig;

/**
 * 媒体播放器
 */
public interface MediaPlayer {
    void play(); //一个播放方法
}
```
接口2实现
```java
package com.spring.action.xmlconfig;

import org.springframework.beans.factory.annotation.Autowired;

public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    public CDPlayer(CompactDisc compactDisc){
        this.compactDisc = compactDisc;
    }
    public void play() {
        System.out.println("播放器开始播放");
        compactDisc.sing();
    }
}
```
XML配置文件

```
<constructor-arg>:借助构造器来注入bean
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="sgtPeppers" class="com.spring.action.xmlconfig.SgtPeppers"/>

    <bean id="cdPlayer" class="com.spring.action.xmlconfig.CDPlayer">
        <constructor-arg name="compactDisc" ref="sgtPeppers"/>
    </bean>

</beans>
```
测试类
```java
package com.spring.action;

import com.spring.action.xmlconfig.CompactDisc;
import com.spring.action.xmlconfig.MediaPlayer;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

//使用SpringJUnit4ClassRunner，可以在测试开始的时候自动创建Spring的应用上下文
@RunWith(SpringJUnit4ClassRunner.class)
//导入配置
@ContextConfiguration(locations = "classpath:site.xml")
public class XMLconfigTest {
    @Autowired
    private CompactDisc compactDisc;

    @Autowired
    private MediaPlayer mediaPlayer;
    @Test
    public void play() {
        mediaPlayer.play();
    }
}
```
运行结果
```
9月 11, 2018 5:15:28 下午 org.springframework.context.support.GenericApplicationContext prepareRefresh
信息: Refreshing org.springframework.context.support.GenericApplicationContext@1623b78d: startup date [Tue Sep 11 17:15:28 CST 2018]; root of context hierarchy
播放器开始播放
sgtPeppers的sing方法
```
# bean的命名
1）没有明确命名bean的id，则id为类名首字母小写。如CDPlayer的bean id就是cDplayer
2）自定义ID

- 方式一：
```java
@Component("cDplayerName")
```
- 方式二：
@Name注解来自Java依赖注入规范（Java Dependency Injection），与@Component只有细微差别，大多数情况下可以互换【个人觉得知道就好，没必要用】
```java
@Name("cDplayerName")
```
# 组件扫描包基础包设置
- 方式一：类所在的包就是基础包
```java
@ComponentScan
```

- 方式二：指定基础包（String 形式）
```java
@ComponentScan(“packageName”)
```

- 方式三：指定基础包更清晰版（String形式）
```java
@ComponentScan(basePackages = “packageName”)
```
- 方式四：指定多个基础包（String 形式）
```java
@ComponentScan(backPackages = {“packageName1”, ”packageName2”})
```
- 方式五：通过类来设置类的所在的包为基础包
```java
@ComponentScan(basePackageClasses={DemoClass.class, DemoClass2.class})
```
>为什么要特地表明2、3、4是string 形式：因为String是类型不安全的（not type-safe）,而5更加安全。5设置可以添加一个空标记接口，只用来设置基础包，这是有利于代码重构的
# 配置文件的加载
- 方式一：自动加载当前类名+context.xml后缀的配置文件。如CDPlayerContext.xml
```java
@ContextConfiguration
```
- 方式二：加载配置类
```java
@ContextConfiguration(classes = JavaConfig.class)
```
- 方式三:加载多个配置类
```java
@ContextConfiguration(classes = {JavaConfig.class, JavaConfig2.class})
```
- 方式四：加载指定的xml文件
```java
@ContextConfiguration(locations=”classpath:xxx.xml”)
```
- 方式五：加载多个指定的xml文件
```java
@ContextConfiguration(locations={“classpath:xxx.xml” , “classpath:xxx2.xml”})
```
>注：用idea开发需要把xml文件放到resources中，idea不会读src下的静态资源文件~~~

# 利用构造器XML声明bean的各种方式
```有<constructor-arg>标签就表示：它借助的是类的构造器。```

- ```方式一：通过<constructor-arg>借助构造器注入初始化bean，name为参数名```
```xml
 <bean id="cdPlayer" class="com.spring.action.xmlconfig.CDPlayer">
        <constructor-arg name="compactDisc" ref="sgtPeppers"/>
    </bean>
```
- 方式二：通过c命名空间借助构造器注入初始化bean
```xml
 <bean id="cdPlayer" class="com.spring.action.xmlconfig.CDPlayer" c:compactDisc-ref="sgtPeppers"/>
```
![在这里插入图片描述](https://img-blog.csdn.net/20180911175623166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
- 方式三：c命名空间索引写法
0代表构造器的第一个参数，为什么要"_"呢，因为XML是不允许属性第一位是数字的
```xml
<bean id="cdPlayer" class="com.spring.action.xmlconfig.CDPlayer" c:_0-ref="sgtPeppers"/>
```
# XML注入字面量（String、基本数据类型等）
- 方式一：
```xml
 <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer">
        <constructor-arg name="compactDisc" ref="sgtPeppers"/>
        <constructor-arg name="name" value="字符串数据"/>
        <constructor-arg name="happy" value="String Data"/>
    </bean>
 ```
   
- 方式二:C命名空间注册
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer"
          c:name="字符串数据"
          c:happy="String Data"
          c:compactDisc-ref="sgtPeppers">
    </bean>
 ```
- 方式三：C命名空间索引
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig2.CDPlayer"
          c:_0="字符串数据"
          c:_1="String Data"
          c:_2-ref="sgtPeppers">
    </bean>
```
- 方式四：装配集合

没有使用name，则按构造器参数顺序注入
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="sgtPeppers" class="com.spring.action.xmlconfig3.SgtPeppers"/>

    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig3.OtherCDPlay">
        <!--String title-->
        <constructor-arg value="title"/>
        <!--CompactDisc compactDisc;-->
        <constructor-arg ref="sgtPeppers"/>
        <!--List<String> stringList;-->
        <constructor-arg>
            <list>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </list>
        </constructor-arg>
        <!--List<CompactDisc> compactDiscList;-->
        <constructor-arg>
            <list>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
            </list>
        </constructor-arg>
        <!--Set<String> compactDiscs;-->
        <constructor-arg>
            <set>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </set>
        </constructor-arg>
        <!--Set<CompactDisc> compactDiscSet;-->
        <constructor-arg>
            <set>
                <ref bean="sgtPeppers"/>
            </set>
        </constructor-arg>
    </bean>

</beans>
```
至此的注入，都是通过构造器进行注入。
# 属性注入
通过属性注入的CDPlayer类只能有一个无参构造器。需要提供属性的setter方法。name就是参数名

- 方式一：<property> 注入
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig4.CDPlayer">
        <property name="name" value="name"/>
        <property name="happy" value="happy"/>
        <property name="compactDisc" ref="sgtPeppers"/>
    </bean>
```
- 方式二：p命名空间法注入
```xml
    <bean id="cdPlayer" class="com.spring.action.xmlconfig4.CDPlayer"
          p:name="name"
          p:happy="happy"
          p:compactDisc-ref="sgtPeppers"
    />
```
![在这里插入图片描述](https://img-blog.csdn.net/20180911203903801?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
- 方式三：属性注入集合
```xml
    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig4.OtherCDPlay">
        <property name="title" value="title"/>
        <property name="compactDisc" ref="sgtPeppers"/>
        <property name="stringList">
            <list>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </list>
        </property>
        <property name="compactDiscList" >
            <list>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
                <ref bean="sgtPeppers"/>
            </list>
        </property>
        <property name="compactDiscs">
            <set>
                <value>a</value>
                <value>b</value>
                <value>c</value>
                <value>d</value>
            </set>
        </property>
        <property name="compactDiscSet">
            <set>
                <ref bean="sgtPeppers"/>
            </set>
        </property>
    </bean>
```
- 方式四:utli空间装配集合
p命名空间无法装配集合，只能装配值或者bean引用
```xml
    <util:list id="stringList">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:list>

    <util:list id="compactDiscList">
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
        <ref bean="sgtPeppers"/>
    </util:list>

    <util:set id="compactDiscs">
        <value>a</value>
        <value>b</value>
        <value>c</value>
        <value>d</value>
    </util:set>

    <util:set id="compactDiscSet">
        <ref bean="sgtPeppers"/>
    </util:set>

    <bean id="otherCDPlayer" class="com.spring.action.xmlconfig4.OtherCDPlay"
          p:title="title"
          p:compactDisc-ref="sgtPeppers"
          p:stringList-ref="stringList"
          p:compactDiscList-ref="compactDiscList"
          p:compactDiscs-ref="compactDiscs"
          p:compactDiscSet-ref="compactDiscSet"
    />
```
Spring util-命名空间中的元素
元素|描 述
--|--
util:constant	|引用某个类型的public static域，并将其暴露为bean|
util:list	|创建一个java.util.List类型的bean，其中包含值或引用|
util:map|	创建一个java.util.Map类型的bean，其中包含值或引用|
util:properties|	创建一个java.util.Properties类型的bean|
util:property-path|	引用一个bean的属性（或内嵌属性），并将其暴露为bean|
util:set|	创建一个java.util.Set类型的bean，其中包含值或引用|
# 导入和混合配置
- 方式一：avaConfig中导入其他的JavaConfig

示例代码：
CDConfig 中配置了一个CompactDisc  bean
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDConfig {
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPepper();
    }
}
```
CDPlayer通过@Import 注解，引入了CDConfig的配置，从而使用了CompactDisc bean
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```
或者更好的办法，使用一个专门的上级配置类，来讲CDConfig和CDPlayerConfig组合在一起（CDPlayerConfig无需导入CDConfig，且两个配置类不用再使用@Configuration注解）

上级配置类，导入了两个class
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({CDConfig.class, CDPlayerConfig.class})
public class JavaConfig {
}
```
两个下级配置类，不需要@Configuration，也不需要@Improt
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

//@Configuration
public class CDConfig {
    @Bean
    public CompactDisc compactDisc(){
        return new SgtPepper();
    }
}
```
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

//@Configuration
//@Import(CDConfig.class)
public class CDPlayerConfig {
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```
- 方式二：JavaConfig中导入XML

示例代码：

JavaConfig:通过@ImportResource导入xml
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;

//@Configuration
//@Import(CDConfig.class)
@ImportResource("classpath:cd-config.xml")
public class CDPlayerConfig {
    @Bean
    public MediaPlayer mediaPlayer(CompactDisc compactDisc){
        return new CDPlayer(compactDisc);
    }
}
```
cd-config.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="sgtPepper" class="com.spring.action.mixedconfig.SgtPepper"/>
    <!--<bean id="cdPlayer" class="com.spring.action.mixedconfig.CDPlayer" c:_0-ref="sgtPepper"/>-->
</beans>
````
- 方式三：在XML中引入JavaConfig
CDConfig：同样的，不需要@Configuration
```java
package com.spring.action.mixedconfig;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

//@Configuration
public class CDConfig {
    @Bean("cdx")
    public CompactDisc compactDisc(){
        return new SgtPepper();
    }
}
```
XML: 通过<bean>标签引入，c:_o-ref="cdx",cdx对应的是CDCing的bean id
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--<bean id="sgtPepper" class="com.spring.action.mixedconfig.SgtPepper"/>-->
    <bean class="com.spring.action.mixedconfig.CDConfig"/>
    <bean id="cdPlayer" class="com.spring.action.mixedconfig.CDPlayer" c:_0-ref="cdx"/>
</beans>
```
- 方式四：在XML中导入XML
```使用<import>标签导入即可```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--<bean class="com.spring.action.mixedconfig.CDConfig"/>-->
    <!--<bean id="cdPlayer" class="com.spring.action.mixedconfig.CDPlayer" c:_0-ref="cdx"/>-->
    <import resource="cd-config.xml"/>
    <bean id="cdPlayer" class="com.spring.action.mixedconfig.CDPlayer" c:_0-ref="sgtPepper"/>
</beans>
```
# bean中的自动装配bean
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    public CDPlayer(CompactDisc compactDisc) {
        this.compactDisc = compactDisc;
    }

    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
CDPlayer中自动装配CompactDisc再这里是存在歧义。怎么解决，第一个想到的就是@Qualifier方法来指定bean，但是！显示@Qualifier不能用在构造器方法上。
 ![在这里插入图片描述](https://img-blog.csdn.net/20180912140541608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01BU09STA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



所以，这个方法不行，那么，通过setter方法注入呢

先添加了一个setter方法，并使用@Qualifier标注注入bean id为anotherSgtPeppers的bean，同时注释了构造方法上的@Autowired
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    @Qualifier("anotherSgtPeppers")
    public void setCompactDisc(CompactDisc compactDisc) {
        System.out.println("进入了setter方法");
        this.compactDisc = compactDisc;
    }

//    @Autowired
    public CDPlayer(CompactDisc compactDisc) {
        this.compactDisc = compactDisc;
    }
    

    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```

测试下，结果报错，错误的原因是错误的创建CDPlayer bean，NoUniqueBeanDefinitionException: No qualifying bean of 
type 'com.spring.ambiguity.quaifier.CompactDisc'：没有限定CompactDisc。
```
Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating
 bean with name 'CDPlayer' defined in file [D:\更改的路径\Desktop\临时
\14\SpringAction\target\classes\com\spring\ambiguity\quaifier\CDPlayer.class]: Unsatisfied 
dependency expressed through constructor parameter 0; nested exception is 
org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of 
type 'com.spring.ambiguity.quaifier.CompactDisc' available: expected single matching bean 
but found 3: anotherSgtPeppers,otherSgtPeppers,sgtPeppers
```
接下来试着把构造器给注释掉，然后添加一个无参的构造器
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

    @Autowired
    @Qualifier("anotherSgtPeppers")
    public void setCompactDisc(CompactDisc compactDisc) {
        System.out.println("进入了setter方法");
        this.compactDisc = compactDisc;
    }

    public CDPlayer() {
        System.out.println("进入了构造器");
    }

    //    @Autowired
//    public CDPlayer(CompactDisc compactDisc) {
//        this.compactDisc = compactDisc;
//    }


    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
测试结果
```
进入了构造器
进入了setter方法
CDPlayer
AnotherSgtPeppers的sing方法
```
结果发现，就算没有@Autowire，也会进入构造器【创建bean实例当然需要构造器。。。】
这下就解释了，一开始的有参构造器，因为CompactDIsc有3个bean可以注入，所以测试结果会出错

所以，第一种办法就是，不要提供有参构造器，然后再setter里面通过@Autowrie和@Qualifier来限定bean

那么，就想用构造器怎么办，试试看自定义注释
```java
package com.spring.ambiguity.quaifier;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
    private CompactDisc compactDisc;

//    @Autowired
//    @Qualifier("anotherSgtPeppers")
//    public void setCompactDisc(CompactDisc compactDisc) {
//        System.out.println("进入了setter方法");
//        this.compactDisc = compactDisc;
//    }

//    public CDPlayer() {
//        System.out.println("进入了构造器");
//    }

    @Okss
    public CDPlayer(CompactDisc compactDisc) {
        System.out.println("进入了有参构造器");
        this.compactDisc = compactDisc;
    }


    public void play() {
        System.out.println("CDPlayer");
        compactDisc.sing();
    }
}
```
测试：结果可行！
```
进入了有参构造器
CDPlayer
AnotherSgtPeppers的sing方法
```
得出的结论就是

（1）任何bean的产生，都需要一个构造参数
（2）一个bean作为另一个bean的参数，即使不用@Autowire，也会自动注入
（3）如果想要再方法里注入bean，那么就必须要@Autowire了
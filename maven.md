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
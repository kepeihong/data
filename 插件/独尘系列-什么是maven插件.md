# 什么是 Maven 的插件？

Maven 是一个执行插件的框架，每一个任务实际上是由插件完成的。Maven 插件通常用于：


	
		
* 创建 jar 文件
* 创建 war 文件 
* 编译代码文件
* 进行代码单元测试
* 创建项目文档
* 创建项目报告
		
	
一个插件通常提供了一组目标，可使用以下语法来执行：

mvn [plugin-name]:[goal-name]

例如，一个 Java 项目可以使用 Maven 编译器插件来编译目标，通过运行以下命令编译

mvn compiler:compile


## 插件类型

Maven 提供以下两种类型插件：


	
			
|  类型   | 描述  |
|  ----  | ----  |
| 构建插件  | 在生成过程中执行，并在 pom.xml 中的<build/> 元素进行配置 |
| 报告插件 | 在网站生成期间执行，在 pom.xml 中的 <reporting/> 元素进行配置|
| env.prod.properties  | 生产配置时，prod信息被使用 |
	

	以下是一些常见的插件列表：

|  类型   | 描述  |
    |  ----  | ----  |
    | clean  | 编译后的清理目标，删除目标目录 |
    | compiler | 编译 Java 源文件|
    | surefile  | 运行JUnit单元测试，创建测试报告 |
    | jar  | 从当前项目构建 JAR 文件 |
    | war | 从当前项目构建 WAR 文件|
    |javadoc  | 产生用于该项目的 Javadoc|    	
    |antrun  | 	从构建所述的任何阶段运行一组 Ant 任务|  

例子


我们使用 maven-antrun-plugin 插件在例子中来在控制台打印数据。现在在 C:\MVN\project 文件夹 创建一个 pom.xml 文件，内容如下： 
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<groupId>com.companyname.projectgroup</groupId>
<artifactId>project</artifactId>
<version>1.0</version>
<build>
<plugins>
   <plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-antrun-plugin</artifactId>
   <version>1.1</version>
   <executions>
      <execution>
         <id>id.clean</id>
         <phase>clean</phase>
         <goals>
            <goal>run</goal>
         </goals>
         <configuration>
            <tasks>
               <echo>clean phase</echo>
            </tasks>
         </configuration>
      </execution>     
   </executions>
   </plugin>
</plugins>
</build>
</project>
```

接下来，打开命令控制台，并转到包含 pom.xml 的文件夹并执行以下命令 mvn 命令。

C:\MVN\project>mvn clean


Maven 将开始处理并显示清洁周期/阶段，如下图中输出：
```
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [post-clean]
[INFO] ------------------------------------------------------------------
[INFO] [clean:clean {execution: default-clean}]
[INFO] [antrun:run {execution: id.clean}]
[INFO] Executing tasks
     [echo] clean phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: < 1 second
[INFO] Finished at: Sat Jul 07 13:38:59 IST 2012
[INFO] Final Memory: 4M/44M
[INFO] ------------------------------------------------------------------

```
上面的例子说明了以下关键概念：


	
		
* 插件可在 pom.xml 使用的 plugin 元素来指定； 
* 每个插件可以有多个目标；
* 从插件应使用它的相位元素开始处理定义阶段。这里已经使用 clean 阶段；
* 可以通过将它们绑定到插件的目标来执行配置任务。这里已经绑定 echo 任务到 maven-antrun-plugin 的运行目标；
		
	就这样，Maven将处理其余部分。如果没有可用的本地存储库，它会下载这个插件；



# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 
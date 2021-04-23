# 什么是构建配置文件?

生成配置文件是一组可以用来设置或覆盖 Maven 构建配置值的默认值。使用生成配置文件，你可以针对不同的环境，如：生产V/S开发环境自定义构建。


配置文件中指定 pom.xml 文件使用其配置文件/配置文件元素和多种方式来触发。配置文件修改 POM 后，在编译的时候是用来给不同的目标环境参数（例如，开发，测试和生产环境的数据库服务器的路径）。


* 生成配置文件的类型


创建配置文件的文件主要有三种类型：

|  类型   | 定义位置  |
|  ----  | ----  |
| Per Project  | 在项目中定义的POM文件, pom.xml |
| Per User  | 定义在 Maven 中的设置 XML 文件(%USER_HOME%/.m2/settings.xml) |
| Global  | 定义在 Maven 中的全局设置 xml 文件 (%M2_HOME%/conf/settings.xml) |
	

### 配置文件激活


Maven 构建配置文件的文件，可以使用以下几种方式来激活。
* 明确使用命令从控制台输入。
* 通过 Maven 设置。
* 基于环境变量（用户/系统变量）。
* OS设置（例如，Windows系列）。
* 呈现/丢失的文件。
		
	


配置文件的文件激活的例子


我们假设你的项目如下的目录结构：

 

现在下src/main/resources 有三个特定的文件：


	
|  文件名称   | 描述  |
|  ----  | ----  |
| env.properties  | 如果没有配置文件关联则使用默认配置 |
| env.test.properties  | 当测试配置文件用于测试配置|
| env.prod.properties  | 生产配置时，prod信息被使用 |
	

显式配置文件激活


在下面的例子中，我们会附加上maven-antrun-plugin:run 插件：运行测试阶段目标。这将使我们能够为不同的配置文件调用文本消息。将在 pom.xml 中定义不同的配置文件，并在命令控制台使用 maven 命令将配置文件启动。


假设，我们建立如下的 pom.xml 在 C:\MVN\project 文件夹。 
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>com.companyname.projectgroup</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>
   <profiles>
      <profile>
      <id>test</id>
      <build>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-antrun-plugin</artifactId>
            <version>1.1</version>
            <executions>
               <execution>
                  <phase>test</phase>
                  <goals>
                     <goal>run</goal>
                  </goals>
                  <configuration>
                  <tasks>
                     <echo>Using env.test.properties</echo>
            <copy file="src/main/resources/env.test.properties" tofile
		    ="${project.build.outputDirectory}/env.properties"/>
                  </tasks>
                  </configuration>
               </execution>
            </executions>
         </plugin>
      </plugins>
      </build>
      </profile>
   </profiles>
   <dependencies>
      <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>3.8.1</version>
         <scope>test</scope>
      </dependency>
   </dependencies>
</project>

	env.properties 

environment=debug

	env.test.properties 

environment=test

	env.prod.properties 

environment=prod
```
现在，打开命令控制台，进入到包含 pom.xml 的文件夹并执行以下 mvn 命令。通过配置文件名作为参数可使用 -P 选项。 
```
C:\MVN\project>mvn test -P test
Maven会开始处理并显示构建配置文件的测试结果。
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [test]
[INFO] ------------------------------------------------------------------
[INFO] [resources:resources {execution: default-resources}]
[WARNING] Using platform encoding (Cp1252 actually) to copy filtered resources,
i.e. build is platform dependent!
[INFO] Copying 3 resources
[INFO] [compiler:compile {execution: default-compile}]
[INFO] Nothing to compile - all classes are up to date
[INFO] [resources:testResources {execution: default-testResources}]
[WARNING] Using platform encoding (Cp1252 actually) to copy filtered resources,
i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory C:MVNprojectsrc	est
esources
[INFO] [compiler:testCompile {execution: default-testCompile}]
[INFO] Nothing to compile - all classes are up to date
[INFO] [surefire:test {execution: default-test}]
[INFO] Surefire report directory: C:MVNproject	argetsurefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
There are no tests to run.

Results :

Tests run: 0, Failures: 0, Errors: 0, Skipped: 0

[INFO] [antrun:run {execution: default}]
[INFO] Executing tasks
     [echo] Using env.test.properties
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: 1 second
[INFO] Finished at: Sun Jul 08 14:55:41 IST 2012
[INFO] Final Memory: 8M/64M
[INFO] ------------------------------------------------------------------
```

现在，作为一个练习，可以做以下步骤： 


	
		
* 在另一个配置文件pom.xml 添加元素（复制现有的配置文件元素 profiles，并将其粘贴轮廓元素结束）。
* 更新在此档案中元素的 ID 并测试正常。
* 更新任务部分使用 env.properties 并将 env.properties 复制到目标目录		
* 再次重复上述三个步骤，更新 ID 和 env.prod.properties
		
	
	
		
现在准备建立配置文件文件（正常/测试/产品）。
		
	


现在，打开命令控制台，进入到该文件夹包含的 pom.xml 并执行以下 mvn 命令。通过配置文件名作为参数使用 -P 选项。
```
C:\MVN\project>mvn test -Pnormal

C:\MVN\project>mvn test -Pprod

```
建立检查输出看到它们的差别了 


配置文件通过Maven设置激活


打开 Maven 的 settings.xml 文件，可在 %USER_HOME%/.m2 目录，％USER_HOME％ 表示用户的主目录。 settings.xml 文件如果不存在，那么就创建一个新的。


添加测试配置文件作为使用activeProfiles节点主动配置文件，示例如下所示：
```
<settings xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
   <mirrors>
      <mirror>
         <id>maven.dev.snaponglobal.com</id>
         <name>Internal Artifactory Maven repository</name>
         <url>http://repo1.maven.org/maven2/</url>
         <mirrorOf>*</mirrorOf>
      </mirror>
   </mirrors>
   <activeProfiles>
      <activeProfile>test</activeProfile>
   </activeProfiles>
</settings>
```
现在，打开命令控制台，进入到该文件夹包含 pom.xml 并执行以下 mvn 命令。不要通过使用 -P 选项。Maven会显示结果，测试配置文件是一个活跃的配置文件。
```
C:\MVN\project>mvn test
```

通过系统环境变量配置文件


现在从 Maven 的 settings.xml 配置文件删除和更新 pom.xml 中提到的测试配置文件。 profile 元素添加 activation 元素，如下所示。 


当系统属性 “env” 的值设置为 “test” 时指定的测试配置文件将被触发。
创建一个环境变量 “env”，并设置其值为 “test” 。
```
<profile>
   <id>test</id>
   <activation>
      <property>
         <name>env</name>
         <value>test</value>
      </property>
   </activation>
</profile>
```
打开命令控制台，进入到包含 pom.xml 文件的文件夹下并执行以下 mvn 命令。
```
C:\MVN\project>mvn test
```

通过操作系统激活配置文件


activation 元素包括 os 细节，如下所示。测试配置文件文件时，将触发的系统是 windows XP。
```
<profile>
   <id>test</id>
   <activation>
      <os>
         <name>Windows XP</name>
         <family>Windows</family>
         <arch>x86</arch>
         <version>5.1.2600</version>
      </os>
   </activation>
</profile>
```
现在，打开命令控制台，进入到文件夹包含 pom.xml 并执行以下 mvn 命令。不要通过使用选项 -P 配置文件名称。 Maven 会显示结果：
```
C:\MVN\project>mvn test
```

配置文件激活通过现状/丢失文件


载入 activation 元件包括 os 的细节，如下所示。test配置文件将因 target/generated-sources/axistools/wsdl2java/com/companyname/group 缺少触发。
```
<profile>
   <id>test</id>
   <activation>
      <file>
         <missing>target/generated-sources/axistools/wsdl2java/
		 com/companyname/group</missing>
      </file>
   </activation>
</profile>
```
现在，打开命令控制台，进入到包含 pom.xml 的文件夹并执行以下 mvn 命令。不要使用选项 -P传递配置文件名称 。 Maven 会显示结果，test 配置文件是一个 活动配置文件。

C:\MVN\project>mvn test



# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 
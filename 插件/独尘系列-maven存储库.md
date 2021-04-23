# maven存储库
在 Maven 术语里存储库是一个目录，即目录中保存所有项目的 jar 库，插件或任何其他项目特定文件，并可以容易由 Maven 使用。
	Maven库中有三种类型
* local - 本地库
* central - 中央库
* remote - 远程库
## 本地库


Maven 本地存储库是一个在本地计算机上的一个文件夹位置。当你第一次运行 maven 命令的时候它就被创建了。

Maven 的本地资源库让您的项目可依赖这些项目（插件库 jar 文件，jar文件等）。当运行 Maven 构建，那么 Maven 会自动下载所有依赖的jar到本地存储库中。它有助于避免依赖存储在远程机器上的项目建立参考。

Maven 本地存储库，默认情况下创建在 ％USER_HOME％ 目录。要覆盖默认位置，可在 Maven 的 settings.xml 文件中修改 ％M2_HOME％conf 目录指向另一个路径。

```
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
      <localRepository>C:/MyLocalRepository</localRepository>
</settings>
```
当运行 Maven 命令，Maven 会下载依赖到您的自定义路径。


## 中央储存库


Maven中央存储库是由Maven社区提供的资源库。它包含了大量的常用程序库。 

当Maven没有在本地存储库找到任何依赖，就会开始搜索在中央存储库，它会使用下列网址: http://repo1.maven.org/maven2/ 

中央仓库的关键概念：

* 此系统信息库由Maven社区管理
* 它不要求配置
* 搜索时需要互联网接入
		
	


要浏览中央 Maven 仓库的内容，Maven 社区提供了一个网址：http://search.maven.org/#browse 。使用这个库，开发人员可以在中央存储库中搜索所有可用的库。


## 远程仓库

有时，Maven不能从依赖中央存储库找到上述库，那么它停下构建过程并输出错误消息到控制台。为了防止这种情况，Maven提供远程仓库概念，这是开发商的自定义库包含所需的库文件或其他项目 jar 文件。

例如，使用以下提到的 pom.xml，Maven 会从远程仓库下载依赖项（不在中央存储库中提供）。
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>com.companyname.projectgroup</groupId>
   <artifactId>project</artifactId>
   <version>1.0</version>
   <dependencies>
      <dependency>
         <groupId>com.companyname.common-lib</groupId>
         <artifactId>common-lib</artifactId>
         <version>1.0.0</version>
      </dependency>
   <dependencies>
   <repositories>
      <repository>
         <id>companyname.lib1</id>
         <url>http://download.companyname.org/maven2/lib1</url>
      </repository>
      <repository>
         <id>companyname.lib2</id>
         <url>http://download.companyname.org/maven2/lib2</url>
      </repository>
   </repositories>
</project>
```
Maven 依赖搜索序列


当我们执行 Maven 构建命令，Maven 依赖库按以下顺序进行搜索：
* 第1步 - 搜索依赖本地资源库，如果没有找到，跳到第2步，否则，如果找到那么会做进一步处理。
* 第2步 - 搜索依赖中央存储库，如果没有找到，则从远程资源库/存储库中，然后移动到步骤4，否则如果找到，那么它下载到本地存储库中，以备将来参考使用。
* 第3步 - 如果没有提到远程仓库，Maven 则会停止处理并抛出错误（找不到依赖库）。
* 第4步 - 远程仓库或储存库中的搜索依赖，如果找到它会下载到本地资源库以供将来参考使用，否则 Maven 停止处理并抛出错误（找不到依赖库）。




# 其他
喜欢的同学点个星星，打赏奖励一下博主！！！

 <img src="https://img-blog.csdnimg.cn/20210414173956371.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 <img src="https://img-blog.csdnimg.cn/20210414174007800.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlMzY5MDkzNDU3,size_16,color_FFFFFF,t_70" width = "200" height = "250" alt="图片名称" align=center />
 
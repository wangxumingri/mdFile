#Maven

<https://blog.csdn.net/zengdongwen/article/details/81241198> 

####常用技巧：

​	==conf/settings.xml==

##### 配置本地仓库：

```properties
 <localRepository>本地仓库地址</localRepository>
 <localRepository>F:/repository</localRepository>
```

##### 配置远程仓库：

```properties
默认是：http://my.repository.com/repo/path</url>
但在国外，下载太慢，配置成国内的阿里云

<mirrors>
		<mirror>
			<id>alimaven</id>
			<name>aliyun maven</name>
			<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
			<mirrorOf>central</mirrorOf>
		</mirror>
</mirrors>
```



##### 配置私有仓库：

```properties
<profiles>
 	<profile>
		<id>aiaf</id>
		
 		<repositories>
			<repository>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>false</enabled>
                    </snapshots>
                    <id>central</id>
                    <name>Central Repository</name>
                    <url>http://repo.maven.apache.org/maven2</url>
  			</repository>
		<repositories>     
        
        <pluginRepositories>
                <pluginRepository>
                    <id>aiaf-group</id>
                    <name>Public Repositories</name>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                    <url>http://nexus.nfs.org/nexus/content/groups/public/</url>
                    <layout>default</layout>
                </pluginRepository>
         </pluginRepositories>
 	<profile>
 <profiles>		
```

#### 配置profile

```xml
<profiles>
      <!--配置全局JDK-->
	  <profile>
            <id>jdk-1.8</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
            </activation>
            <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
            </properties>
          
          
        </profile>
 </profiles>
```



`tips：`  **也可以在pom.xml中配置仓库**

#####安装本地jar包：

```
mvn install:install-file 
	-Dfile=jar包所在目录 
	-DgroupId=包的组织名 
	-DartifactId=包的项目名 
	-Dversion=包的版本号 
	-Dpackaging=打包方式（一般都是jar）
```

`示例：`

**安装lombok的jar包：目录D：\lombok.jar**

```xml
命令行：直接输入（前提，配置了path）
	mvn install:install-file 
         -Dfile= D:\lombok.jar
		-DgroupId=org.projectlombok 
		-DartifactId=lombok 
		-Dversion=1.18.2 
		-Dpackaging=jar
<!--maven中lombok的坐标-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.2</version>
    <scope>provided</scope>
</dependency>
```

**上述命令成功后，即可在pom.xml文件中，引入lombok的jar**

`tips：`

​	上述参数可以去： https://mvnrepository.com 去查询





## 上传到远程：

输入url地址

有权限要求的，还要在setting.xml中配置好用户名和密码以及仓库id 

```
mvn deploy:deploy-file -Dmaven.test.skip=true 
-DgroupId=cn.conac.rc.api.client 
-DartifactId=rc-gateway-api-client 
-Dversion=1.0.0 -Dpackaging=jar 
-Dfile=D:\temp\rc-gateway-api-client.jar 
-Durl=http://你的地址/nexus/content/repositories/releases/ 
-DrepositoryId=releases
```

`tips:`

​	-Durl：这就是你要安装到的仓库的地址 

​	命令不能换行，否则在cmd窗口复制不完全

***

#####依赖问题：

​	maven项目中的依赖关系主要有：父子，兄弟

​	父子：子模块会继承父模块pom.xml中的  **dependencies**和  **plug**  

​	`tips:`

​		1. 如果父模块使用了   `***Management`，子模块就不会继承父模块的上述内容

​     		    此时，子模块需要什么jar包，需要在自己的pom.xml中导入：

​		        	* 父模块已定义的，可以不加版本号

​			        * 父模块中没有的，需要加版本号

​		2.相反，子模块会自动继承父模块的依赖和插件，无需额外导入

​	兄弟：

​		兄弟依赖需要手动建立，比如一个项目中的两兄弟模块，A与B，如果A 想用 B模块中的类，

​		就需要在自己的pom.xml中，手动导入B模块的坐标，主动建立依赖关系。

####常用标签：

#####properties标签：

properties中除了可以锁定jar包版本，还可以配置源码和编译的字符编码，还可以指定web.xml的位置

```xml
<properties>
    	<!--指定build时的字符编码-->
 		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<!--指定compile的字符编码-->
    	<project.compile.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<dm.api.version>1.0.0-SNAPSHOT</dm.api.version>
    	<!-- Skip artifact deployment -->
		<maven.deploy.skip>true</maven.deploy.skip>
    	<!--定义一个变量，后面用${webXmlPath}，来引用-->
		<webXmlPath>src/main/webapp/WEB-INF/web.xml</webXmlPath> 
</properties>
```

##### dependency标签：

```xml
<dependency>
	<groupId>org.springframework.batch</groupId>
	<artifactId>spring-batch-admin-manager</artifactId>
	<version>1.3.0.RELEASE</version>
	<exclusions>
		<exclusion>
			<groupId>org.springframework.integration</groupId>
			<artifactId>spring-integration-file</artifactId>
		</exclusion>
	</exclusions>
</dependency>
```

**`tips：` 可配合  `exclusions`标签，排除该坐标下的某个jar**

#####resource标签：

```xml
<build>
	<finalName>devicemonitor</finalName>
	<pluginManagement>
		<plugins>
			<plugin>
				<artifactId>maven-war-plugin</artifactId>
				<configuration>
					<!-- <packagingExcludes>WEB-INF/lib/*.jar</packagingExcludes> -->
					<webXml>${webXmlPath}</webXml>
				</configuration>
			</plugin>
		</plugins>
	</pluginManagement>
	<resources>
		<resource>
			<directory>src/main/java</directory>
			<includes>
				<include>**/*.xml</include>
				<include>**/*.properties</include>
			</includes>
		</resource>
	</resources>
<build>    
```

#####profiles标签：

设置环境：如开发，测试，生产；启动项目时，可根据参数，启动对应的环境

```xml
<profiles>
	<profile>
		<id>devicemonitor</id>
		<properties>
             <!--指定该环境使用的web.xml-->
			<webXmlPath>resource2/web.xml</webXmlPath>
		</properties>
	</profile>
</profiles>
```


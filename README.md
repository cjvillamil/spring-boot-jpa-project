# Spring-boot-jpa-project
It's a spring-boot-jpa-project step by step. We will construct a Java RESTful services for a jpa project.

It's important for me realize the documentation for don't forget a older implementations.

## Configure spring-boot project application.properties file

In this case i'm using a SqlServer database, but in theory

```properties
# DATASOURCE configuration
spring.datasource.driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver
spring.datasource.jndi-name=jdbc/your-jdbc

# The SQL dialect makes Hibernate generate better SQL for the chosen database
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.SQLServerDialect

#Hibernate property for enable work with database columns with camelCase style naming.
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl

# Hibernate ddl auto update listens for modifications, it
# does not delete existing tables though
spring.jpa.hibernate.ddl-auto = none

# Logs sql statements
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hiberna
```
In the pom file the configuration is something like this
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>your.group.id</groupId>
	<artifactId>artefactId</artifactId>
	<version>0.0.1</version>
	<packaging>war</packaging>
	<name>appName</name>
	<description>app description</description>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>javax</groupId>
			<artifactId>javaee-api</artifactId>
			<scope>provided</scope>
			<version>7.0</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
			<exclusions>
            	<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-tomcat</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
		    <groupId>com.microsoft.sqlserver</groupId>
		    <artifactId>mssql-jdbc</artifactId>
		    <scope>runtime</scope>
		</dependency>
<!--In my personal case i'm ussing a Liberty Server (it's the lite version of WAS or Webshpre Application Server i'm using was9 and liberty profile for a was9)-->
<!-- 		<dependency> -->
<!-- 			<groupId>org.springframework.boot</groupId> -->
<!-- 			<artifactId>spring-boot-starter-tomcat</artifactId> -->
<!-- 			<scope>provided</scope> -->
<!-- 		</dependency> -->
<!-- 		<dependency> -->
<!-- 			<groupId>org.springframework.boot</groupId> -->
<!-- 			<artifactId>spring-boot-starter-test</artifactId> -->
<!--  			<scope>test</scope> -->
<!-- 		</dependency> -->
		<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
		<dependency>
		    <groupId>org.projectlombok</groupId>
		    <artifactId>lombok</artifactId>
		    <scope>provided</scope>
		</dependency>
		<dependency>
		    <groupId>org.modelmapper</groupId>
		    <artifactId>modelmapper</artifactId>
		    <version>2.3.3</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<executions>
               		<execution>
               			<goals>
               				<goal>repackage</goal>
           				</goals>
           				<configuration>
           					<skip>true</skip>
       					</configuration>
   					</execution>
				</executions>
			</plugin>
			<plugin>
	            <artifactId>maven-resources-plugin</artifactId>
            </plugin>
		</plugins>		
	</build>

</project>
```
## Jpa tips for json deserialization

For a OneToOne circular references, you should use Jackson anotations like this:

```java
//bi-directional one-to-one association to User
	@OneToOne(fetch=FetchType.EAGER)
	@JsonIdentityInfo(generator = ObjectIdGenerators.IntSequenceGenerator.class, property = "user_id")
	@JoinColumn(name="user_id", nullable=false, insertable=false, updatable=false)
	private User user1;

	//bi-directional one-to-one association to User
	@JsonBackReference
	@OneToOne(mappedBy="user1", fetch=FetchType.LAZY)
	private User user2;
```

For OneToMany and ManyToOne you should use Jackson anotations like this:

```java
	//On socio entity bi-directional many-to-one association to User
	@OneToMany(mappedBy="socio")
	@JsonIgnore
	private List<User> Usuarios;
	
	//On user entity bi-directional many-to-one association to Socio
	@ManyToOne(fetch=FetchType.EAGER)
	@JoinColumn(name="socio_id")
	private Socio socio;
```
# TIPS AND TRICKS
## Create a .war for a Angular project
I'm beliving that in this world its so important that you learn all time, this is a pearl that i was find, becouse i need deploy a angular project into WAS server, its fine i'm not crazy; and yes this is possible.

into root folder of your angular project execute
```ng
ng build
```
for multiple folders 
```ng
ng build -- NameForRootFolder
```
this creates a folder called dist, then move into the dist folder and execute
```cmd
jar -cvf WarNameForYourProject.war
```

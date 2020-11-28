

# SpringBoot

## 1. SpringBoot基础回顾

## 2. SpringBoot原理深入及源码剖析

​	传统的Spring框架实现一个web服务，需要导入各种依赖jar包，然后编写对应的xml配置文件等，相较而言，Spring Boot显得更加方便、快捷和高效。

接下来分别对Spring Boot框架的依赖管理、自动配置、执行流程进行深入分析。

### 2.1 依赖管理

问题：（1）为什么导入dependency时不需要指定版本？

在SpringBoot入门程序中，项目pom.xml文件有两个核心依赖，分别是spring-boot-starter-parent和spring-boot-starter-web，关于这两个依赖的相关介绍具体如下：

#### 1. spring-boot-starter-parent依赖

在charpter01项目中的pom.xml文件中找到spring-boot-starter-parent依赖，示例代码如下1：

```
<!-- Spring Boot父项目依赖管理 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent<11./artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>`
```

上述代码中，将spring-boot-starter-parent依赖作为Spring Boot项目的统一父项目依赖管理，并将项目版本号统一为2.2.2.RELEASE，该版本号根据实际开发需求是可以修改的

继续查看spring-boot-starter-parent底层源文件，发现spring-boot-starter-parent的底层有一个父依赖spring-boot-dependencies，核心代码具体如下

```
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.2.2.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

继续查看spring-boot-dependencies底层源文件，核心代码具体如下：

```
<properties>
    <activemq.version>5.15.11</activemq.version>
    ...
    <solr.version>8.2.0</solr.version>
    <mysql.version>8.0.18</mysql.version>
    <kafka.version>2.3.1</kafka.version>
    <spring-amqp.version>2.2.2.RELEASE</spring-amqp.version>
    <spring-restdocs.version>2.0.4.RELEASE</spring-restdocs.version>
    <spring-retry.version>1.2.4.RELEASE</spring-retry.version>
    <spring-security.version>5.2.1.RELEASE</spring-security.version>
    <spring-session-bom.version>Corn-RELEASE</spring-session-bom.version>
    <spring-ws.version>3.0.8.RELEASE</spring-ws.version>
    <sqlite-jdbc.version>3.28.0</sqlite-jdbc.version>
    <sun-mail.version>${jakarta-mail.version}</sun-mail.version>
    <tomcat.version>9.0.29</tomcat.version>
    <thymeleaf.version>3.0.11.RELEASE</thymeleaf.version>
    <thymeleaf-extras-data-attribute.version>2.0.1</thymeleaf-extras-data-
    attribute.version>
    ...
</properties>
```

从该文件可以看出，该文件通过标签对一些常用技术框架的依赖文件进行了统一版本号管理，例如acticemq、spring、tomcat等，都有与spring boot 2.2.2版本相匹配的版本，这也是pom.xml引入依赖文件不需要标注依赖文件版本号的原因。

需要说明的是，如果pom.xml引入的依赖文件不是spring-boot-starter-parent管理的，那么在pom.xml引入依赖文件时，需要使用标签指定依赖文件的版本号。



问题 ：（2） spring-boot-starter-parent父依赖启动器的主要作用是进行版本统一管理，那么项目运行依赖的jar包是从何而来的？

#### 2. spring-boot-starter-web依赖

查看spring-boot-starter-web依赖文件源码，核心代码具体如下

```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-json</artifactId>
        <version>2.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <version>2.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
        <version>2.2.2.RELEASE</version>
        <scope>compile</scope>
        <exclusions>
        <exclusion>
        <artifactId>tomcat-embed-el</artifactId>
        <groupId>org.apache.tomcat.embed</groupId>
        </exclusion>
        </exclusions>
        </dependency>
        <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.2.RELEASE</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

从上述代码可以发现，spring-boot-starter-web依赖启动器的主要作用是提供web开发场景所需要的底层所有依赖

正式如此，在pom.xml中引入spring-boot-starter-web依赖启动器时，就可以实现web场景开发，而不需要额外导入Tomcat服务器以及其它web依赖文件等。当然，这些引入的依赖文件的版本号还是由spring-boot-starter-parent父依赖进行的统一管理。

spring boot除了提供上述介绍的web依赖启动器外，还提供了其它许多开发场景的相关依赖，我们可以打开springboot官方文档，搜索“Starters”关键字查询场景依赖启动器

![1605447180331](C:\Users\Qingyu\AppData\Roaming\Typora\typora-user-images\1605447180331.png)

列出了spring boot官方提供的部分场景依赖启动器，这些依赖启动器适用于不同的场景开发，使用时只需要在pom.xml文件中导入对应的依赖启动器即可。

需要说明的是，springboot官方并不是针对所有的场景开发的技术框架都提供了场景启动器，例如数据库操作框架mybatis、阿里巴巴的druid数据源等，springboot官方就没有提供对应的依赖启动器。为了充分利用springboot框架的优势，在springboot官方没有整合这些技术框架的情况下，mybatis、druid等技术框架所在的开发团队主动与springboot框架进行了整合，实现了各自的依赖启动器，例如mybatis-spring-boot-starter?druid-spring-boot-starter等。我们在pom.xml文件中引入这些第三方的依赖启动器时，切记要配置对应的版本号。

### 2.2 自动配置（启动流程）

概念：能够在我们添加jar包依赖的时候，自动为我们配置一些组件的相关配置，我们无需配置或者只需要少量的配置就能运行编写的项目。

问题：spring boot到底是如何进行自动配置的，都把哪些组件进行了自动配置？

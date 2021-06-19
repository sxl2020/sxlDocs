# 一、Spring Boot 入门

## 1、Spring Boot 简介

> 简化Spring应用开发的一个框架；
>
> 整个Spring技术栈的一个大整合；
>
> J2EE开发的一站式解决方案；



## 2、微服务

2014，martin fowler

- 微服务：架构风格（服务微化）

一个应用应该是一组小型服务；可以通过HTTP的方式进行互通；

微服务：每一个功能元素最终都是一个可**独立替换**和**独立升级**的软件单元；

- 单体应用：ALL IN ONE

  - [详细参照微服务文档](https://martinfowler.com/articles/microservices.html#MicroservicesAndSoa)



## 3、环境准备

- http://www.gulixueyuan.com/ 谷粒学院

环境约束

- jdk1.8：Spring Boot 推荐jdk1.7及以上；java version "1.8.0_112"

- maven3.x：maven 3.3以上版本；Apache Maven 3.3.9

- IntelliJIDEA2017：IntelliJ IDEA 2017.2.2 x64、STS

- SpringBoot 1.5.9.RELEASE：1.5.9；

统一环境

### 1、MAVEN设置；

给maven 的settings.xml配置文件的profiles标签添加

```xml
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
```

### 2、IDEA设置

整合maven进来；

![idea设置](E:/sxlKG/docs/SpringBoot/images/搜狗截图20180129151045.png)



![images/](E:/sxlKG/docs/SpringBoot/images/搜狗截图20180129151112.png)

## 4、Spring Boot HelloWorld

功能需求：浏览器发送hello请求，服务器接受请求并处理，响应Hello World字符串；

### 1、创建一个maven工程；（jar）

### 2、导入spring boot相关的依赖

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```

### 3、编写一个主程序；启动Spring Boot应用

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}
```

### 4、编写相关的Controller、Service

```java
@Controller
public class HelloController {

    @ResponseBody
    @RequestMapping("/hello")
    public String hello(){
        return "Hello World!";
    }
}
```

### 5、运行主程序测试

### 6、简化部署

```xml
 <!-- 这个插件，可以将应用打包成一个可执行的jar包；-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

将这个应用打成jar包，直接使用java -jar的命令进行执行；

> 在使用Intellij Idea来开发时，只需点击运行按钮便会自动安装部署。若没有安装Idea，也可以使用命令java -jar 来运行程序。



## 5、SpringBoot自动配置

### 5.1 POM文件研究

关于Maven及POM的相关文献，可以参考博客

- [Maven笔记](https://blog.csdn.net/qq_30757197/article/details/102599145)
- [基于maven的Java项目目录结构总结](https://blog.csdn.net/qq_30757197/article/details/103630048)

#### 5.1.1 父项目

```xml
<--- --所有Spring-Boot-starter项目的父项目->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>
```

**在Idea中通过快捷键ctrl+点击spring-boot-starter-parent**，进入其父项目的配置文件 spring-boot-dependencies。

```
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>1.5.9.RELEASE</version>
  <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

**在Idea中通过快捷键ctrl+点击spring-boot-dependencies**，进入其配置文件，发现<properties>属性配置了SpringBoot默认的配置

```
<properties>
    <activemq.version>5.15.12</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.79</appengine-sdk.version>
    <artemis.version>2.10.1</artemis.version>
    ...
    <aspectj.version>1.9.5</aspectj.version>
</properties>    
它来真正管理Spring Boot应用里面的所有依赖版本；
```

从上面的分析汇总，可以知道:

**当我们导入SpringBoot项目时，是不需要写版本的，默认的版本在Springboot中均已配置好。**



#### 5.1.2 spring-boot-starter-web 启动器

在pom配置文件中，包含了该依赖spring-boot-starter-web。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

- spring-boot-starter-web，主要作用是帮我们导入了**web模块**正常运行所依赖的组件。

Spring Boot将所有的功能场景都抽取出来，称为spring-boot-starter：又叫做spring-boot场景启动器，主要是帮我们导入各种开发场景下所依赖的组件；做成一个个的starters（启动器），只需要在项目里面引入这些starter相关场景的所有依赖都会导入进来。要用什么功能就导入什么场景的启动器。常见的有：

- spring-boot-starter-activemq
- spring-boot-starter-aop等等



### 5.2 主程序类SpringApplication

```java
/**
 *  @SpringBootApplication 来标注一个主程序类，说明这是一个Spring Boot应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {

    public static void main(String[] args) {

        // Spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
    }
}
```



#### 5.2.1 @**SpringBootApplication**:

@**SpringBootApplication**:    

Spring Boot应用标注在某个类上说明这个类是SpringBoot的**主配置类**，SpringBoot就应该运行这个类的main方法来启动SpringBoot应用；

@**SpringBootApplication**:   

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
      @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```



##### 5.2.1.1 @**SpringBootConfiguration**:

@**SpringBootConfiguration**:

该注解标注在某个类上，表示这是一个Spring Boot的配置类；这个注解也是一个组合注解，其核心注解代码为：

```
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
```

其内有一个@Configuration注解

@**Configuration**:这是一个Spring注解，标注在配置类上来标注这个注解；

>配置类与配置文件：
>配置类 与  配置文件的作用是一样的，对项目中的相关参数进行配置；使用**配置类可以将配置操作代码化**。配置类本质上也是容器中的一个组件，底层用@Component来标注。



##### 5.2.1.2 @**EnableAutoConfiguration**

作用： **开启自动配置功能；**

以前我们需要配置的东西，Spring Boot帮我们自动配置；@**EnableAutoConfiguration**告诉SpringBoot开启自动配置功能；这样自动配置才能生效； EnableAutoConfiguration也是一个组合注解，是由 AutoConfigurationPackage和 Import两个注解组合而成。

```java
@AutoConfigurationPackage
@Import(EnableAutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

###### 5.2.1.2.1@**AutoConfigurationPackage**：

作用：自动配置包

其AutoConfigurationPackage的组合内，含有如下注解：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import({Registrar.class})
public @interface AutoConfigurationPackage {
}
```

其本质是一个Spring的底层注解@Import，给容器中导入一个组件；

​		@**Import**({Registrar.class})：

导入的组件由AutoConfigurationPackages.Registrar.class来指定。

进入Registrar类的核心代码观察到：

```java
 static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {
        Registrar() {
        }
// 注册一些Bean及相关信息
        public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
            AutoConfigurationPackages.register(registry, (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName());
        }
```

在上述代码中

```java
AutoConfigurationPackages.PackageImport(metadata)).getPackageName()
```

计算的结构为主配置类所在的包名，在本项目中即com.atguigu。

**因此，Registrar**类的作用是：

> 将主配置类（@SpringBootApplication标注的类）的所在包及下面所有子包里面的所有组件扫描到Spring容器；



###### 5.2.2.2.2 AutoConfiguration...Selector.class

**作用**：@Import({AutoConfigurationImportSelector.class})，导入哪些组件的选择器；

**探究：**进入AutoConfigurationImportSelector类，观察到有一个SelectImports方法,该方法告知如何自动导入组件

```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
...
	public String[] selectImports(AnnotationMetadata annotationMetadata) {
        if (!this.isEnabled(annotationMetadata)) {
            return NO_IMPORTS;
        } else {
            AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
            AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
            return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
        }
    }
}
```

**结论：**将所有需要导入的组件以全类名的方式返回；这些组件就会被添加到容器中；会给容器中导入非常多的自动配置类（xxxAutoConfiguration）；![自动配置类](E:/sxlKG/docs/SpringBoot/images/搜狗截图20180129224104.png)

有了自动配置类，免去了我们手动编写配置注入功能组件等的工作；

**自动配置的原理：**

Spring Boot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值，将这些值作为**自动配置类导入到容器中**，自动配置类就生效，帮我们进行自动配置工作；==以前我们需要自己配置的东西，自动配置类都帮我们；

**J2EE的整体整合解决方案和自动配置都在spring-boot-autoconfigure-1.5.9.RELEASE.jar中；**



## 6、使用Spring Initializer快速创建Spring Boot项目

### 6.1 IDEA：使用 Spring Initializer快速创建项目

IDE都支持使用Spring的项目创建向导快速创建一个Spring Boot项目；

选择我们需要的模块；向导会联网创建Spring Boot项目；

#### 6.1.1 默认项目结构

默认生成的Spring Boot项目；

- 主程序已经生成好了，我们只需要我们自己的逻辑
- resources文件夹中目录结构
  - static：保存所有的静态资源； js css  images；
  - templates：保存所有的模板页面；（Spring Boot默认jar包使用嵌入式的Tomcat，默认不支持JSP页面）；可以使用模板引擎（freemarker、thymeleaf）；
  - application.properties：Spring Boot应用的配置文件；可以修改一些默认设置；


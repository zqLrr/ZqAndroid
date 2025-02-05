# Spring-Boot 框架

目标：

1.搭建起spring-boot框架

2.连接数据库

3.Code First

4.实现接口的实现

5.Spring-boot部署（Docker部署）

## 框架搭建

1.使用[Spring-boot官网](https://start.spring.io/)提供的模版，搭建

![image-20240514174506322](Spring-Boot 框架.assets/image-20240514174506322.png)

1.报错：

 Doesn't say anything about its target Java version (required compatibility with Java 11)

原因：Gradle 插件org.springframework.boot 3.2.5 需要gradle JVM 1

解决方案：

配置Gradle JVM :17

![image-20240514175035544](Spring-Boot 框架.assets/image-20240514175035544.png)

## 连接数据库

以MySQL 为例：

首先引入Mysql 驱动

```
implementation 'com.mysql:mysql-connector-j'
```

在`application.properties`中添加dataBase相关信息

```bash
spring.jpa.hibernate.ddl-auto=update
#mysql?????????????
spring.datasource.url=jdbc:mysql://mysql_test:3306/sg_case_scheme?characterEncoding=utf-8
#mysql_test：是数据库ip或者容器地址，使用容器地址是为了实现ip改变时不影响

spring.datasource.username=root
spring.datasource.password=root
#mysql???????????
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
```

## 使用IDEA可视化数据库 

采用Database工具

## Spring 三层架构

**Controller（控制层）：** 负责接收客户端请求，调用相应的业务逻辑，并返回视图或数据。

**Service（业务逻辑层）：** 处理具体的业务逻辑，调用 DAO 层进行数据操作。

**DAO（数据访问层）：** 直接与数据库交互，执行数据的增删改查操作。

## ORM 框架

是为了实现Code 和DataBase 的相互转换，体现了Code First的思想，Spring 提供了JPA 作为ORM框架。

```java
implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
```

主要有以下注解：@Entity @Table @Column 等

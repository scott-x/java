# mybatis

[官网中文入口](https://mybatis.org/mybatis-3/zh/index.html)


### mybatis环境搭建
1. idea创建maven项目, 创建实体类和接口
2. 添加依赖,pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>mybatis</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
     <dependencies>
         <dependency>
             <groupId>org.mybatis</groupId>
             <artifactId>mybatis</artifactId>
             <version>3.4.5</version>
         </dependency>
         <dependency>
             <groupId>mysql</groupId>
             <artifactId>mysql-connector-java</artifactId>
             <version>8.0.18</version>
         </dependency>
         <dependency>
             <groupId>log4j</groupId>
             <artifactId>log4j</artifactId>
             <version>1.2.12</version>
         </dependency>
         <dependency>
             <groupId>junit</groupId>
             <artifactId>junit</artifactId>
             <version>4.10</version>
         </dependency>
         <dependency>
             <groupId>org.apache.maven.plugins</groupId>
             <artifactId>maven-compiler-plugin</artifactId>
             <version>3.8.1</version>
         </dependency>
     </dependencies>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>13</maven.compiler.source>
        <maven.compiler.target>13</maven.compiler.target>
    </properties>
</project>
```

3. 主配置文件，放在resources目录下，可以随便命名

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--myBatis主配置文件-->
<configuration>
    <!--配置环境-->
    <environments default="mysql">
        <!--配置mysql的环境-->
        <environment id="mysql">
            <!--配置事务的类型-->
            <transactionManager type="JDBC"/>
            <!--配置数据源，也叫连接池-->
            <dataSource type="POOLED">
                <!--配置连接数据库的四个基本信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/day16"/>
                <property name="username" value=""/>
                <property name="password" value=""/>
            </dataSource>
        </environment>
    </environments>
    <!--指定映射配置文件的位置，映射配置文件值的是每个dao独立的配置文件-->
    <mappers>
        <mapper resource="com/scott/dao/UserDao.xml"/>
    </mappers>
</configuration>
```
4. mapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.scott.dao.UserDao">
    <!--配置查询所有，id为接口的方法名称-->
    <select id="findAll" resultType="com.scott.domain.User">
       select * from user
    </select>
</mapper>
```

### test

```java
package com.scott.test;

import com.scott.dao.UserDao;
import com.scott.domain.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @ClassName: MybatisTest
 * @description:
 * @date: 2020/4/13 5:21 AM
 * @author: Scott
 * @version: 1.0
 */
public class MybatisTest {
    /**
     * 入门案例
     * @param args
     */
    public static void main(String[] args) throws IOException {
        //1. 读取配置文件
        InputStream in = Resources.getResourceAsStream("sqlMapConfig.xml");
        //2. 创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        //3. 使用工厂生成一个SqlSession对象
        SqlSession sqlSession = factory.openSession();
        //4. 使用SqlSession创建Dao的代理对象
        UserDao userdao = sqlSession.getMapper(UserDao.class);
        //5. 使用代理对象执行方法
        List<User> users = userdao.findALl();

        for (User user : users) {
            System.out.println(user);
        }
        //6. 释放资源
        sqlSession.close();
        in.close();
    }
}
```
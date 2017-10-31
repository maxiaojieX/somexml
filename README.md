# 目录
* [pom.xml](#pom)
* [web.xml](#web)
* [mybatis-config.xml](#mybatis-config)
* [Mapper.xml](#Mapper)
* [generatorConfig.xml](#generator)
* [Spring-applicationContext.xml](#Spring)

<h2 id="pom">pom.xml</h2>

***
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.ma</groupId>
    <artifactId>Blog</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>war</packaging>

    <dependencies>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

    </dependencies>

    <build>
        <finalName>Blog</finalName>
        <plugins>

            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>

            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
                <configuration>
                    <port>8080</port>
                    <path>/</path>
                </configuration>
            </plugin>

        </plugins>
    </build>
</project>
```
***
<h2 id="web">web.xml</h2>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

</web-app>
```
***
<h2 id="mybatis-config">mybatis-config.xml</h2>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <typeAliases>
        <typeAlias alias="Student" type="com.kaishengit.entity.Student" />
    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
                <property name="username" value="root" />
                <property name="password" value="123456" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="mappers/StudentMapper.xml" />
    </mappers>
</configuration>
```
<h2 id="Mapper">Mapper.xml</h2>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kaishengit.entity.StudentMapper">

	<resultMap id="projectMap" type="Project">
            <id property="id" column="id"/>
            <result property="projectName" column="project_name"></result>
            <association property="admin" javaType="com.kaishengit.entity.Admin">
                <id property="id" column="id"/>
                <result property="adminName" column="admin_name"/>
            </association>
        </resultMap>
	
	<resultMap id="ArticleMap" type="com.kaishengit.entity.Article">
            <id property="id" column="id"/>
            <result property="title" column="title"/>
            <collection property="lableList" ofType="com.kaishengit.entity.Lable" >
                <id property="lid" column="lid"/>
                <result property="lablename" column="lablename"/>
            </collection>
        </resultMap>
	
	<select id="findGo" resultMap="ArticleMap" parameterType="map">
            SELECT
                t_article.id,t_article.title,t_lable.lid,t_lable.lablename
            FROM
                t_article
            INNER JOIN t_article_lable ON t_article.id = t_article_lable.aid
            INNER JOIN t_lable ON t_article_lable.lid = t_lable.lid
            <where>
                <if test="id != null and id != ''">
                    id = #{id}
                </if>
                <if test="title != null and title != ''">
                    AND title =#{title}
                </if>
            </where>
    </select>
	
    <insert id="insertMany" parameterType="list">
        INSERT INTO students (name,email) VALUES
        <foreach collection="list" item="student" separator="," >
            (#{student.name},#{student.email})
        </foreach>
    </insert>
	
</mapper>
```
<h2 id="generator">generatorConfig.xml</h2>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--使用本地connector.jar连接数据库-->
    <classPathEntry location="F:\maven_local_lib\mysql\mysql-connector-java\5.1.26\mysql-connector-java-5.1.26.jar"/>
    <!--targetRuntime:MyBatis3:生成带有Example
                      MyBatis3Simple：生成简单的类和sql语句-->
    <context id="myConfig" targetRuntime="MyBatis3">
        <!--是否禁用默认注释-->
        <commentGenerator>
            <property name="suppressAllComments" value="true"/>
            <property name="suppressDate" value="true"/>
        </commentGenerator>
        <!--连接数据库-->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/mybatis"
                        userId="root"
                        password="123456"/>
        <!--targetPackage：生成的实体类放进哪个包？  targetProject：包的位置？-->
        <javaModelGenerator targetPackage="com.kaishengit.entity" targetProject="src/main/java"/>
        <!--targetPackage：生成的XML放在哪个文件夹？  targetProject：文件夹的位置 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>
        <!--targetPackage：生成的Mapper接口放在哪个包？ targetProject：包的位置？-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.kaishengit.mapper" targetProject="src/main/java"/>
        <!--tableName:为数据库中哪些表使用自动生成  domainObjectName:在JAVA中的名字-->
        <table tableName="t_project" domainObjectName="Project" enableSelectByExample="true"/>
    </context>
</generatorConfiguration>
```
<h2 id="Spring">Spring-applicationContext.xml</h2>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--不使用注解时，bean，注入以及AOP的配置-->

    <!--<bean id="userDao" class="com.kaishengit.entity.UserDao"/>-->
    <!--<bean id="userServiceImpl" class="com.kaishengit.service.impl.UserServiceImpl">-->
        <!--<property name="userDao" ref="userDao"/>-->
    <!--</bean>-->
    <!--<bean class="com.kaishengit.service.impl.User2Impl" id="user2"/>-->
    <!--<bean class="com.kaishengit.util.AopAdvance" id="aopAdvance"/>-->

    <!--<aop:config>-->
        <!--&lt;!&ndash;引用通知类&ndash;&gt;-->
        <!--<aop:aspect ref="aopAdvance" >-->
            <!--&lt;!&ndash;给哪些类中的哪些方法使用动态代理 ,-->
            <!--并不是在加载xml时创建了代理目标对象，而是在通-->
            <!--过getBean获取这个类的时候才创建代理对象&ndash;&gt;-->
            <!--<aop:pointcut id="pointcut" expression="execution(* com.kaishengit.service..*.*(..))"/>-->
            <!--<aop:around method="aroundAop" pointcut-ref="pointcut"/>-->
        <!--</aop:aspect>-->
    <!--</aop:config>-->


    <!--使用注解时，bean使用自动扫描，AOP使用自动代理-->
    <context:component-scan base-package="com.kaishengit"/>
    <context:property-placeholder location="classpath:config.properties"/>
    <aop:aspectj-autoproxy/>

    <!--Spring jdbc数据源的配置-->
    <bean class="org.apache.commons.dbcp2.BasicDataSource" id="dataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--jdbcTemplate 类似于dbUtil中的QueryRunner-->
    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--事务-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>
        

</beans>
```
# 目录

# 杂货铺
* [pom.xml](#pom) 
* [web.xml](#web)
* [mybatis-config.xml](#mybatis-config)
* [Mapper.xml](#Mapper)
* [generatorConfig.xml](#generator)
* [Spring-applicationContext.xml](#Spring)
* [Spring-Quartz.xml](#Spring-Quartz)
* [Spring-ActiveMQ.xml](#Spring-ActiveMQ)
* [Hibernate.cfg.xml](#Hibernate.cfg)
* [XXX.hbm.xml](#hbm)
* [spring-dubbo-xx.xml](#dubbo)

</br>

SSM框架打包带走
* [Maven依赖](#maven)
* [web.xml配置SpringMVC中央控制器](#ssmweb)
* [mvc-servlet.xml配置扫描中央控制器、视图解析器](#mvc)
* [Spring.xml配置扫描Bean,数据源和事务](#ssmspring)

<hr>
<br><br>

# 实体店
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
<h2 id="maven">Maven依赖</h2>

```xml
<dependencies>
    <!--spring-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.3.9.RELEASE</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.4</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>1.3.1</version>
    </dependency>
    <!--jackson-->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.8.9</version>
    </dependency>

</dependencies>

```
<h2 id="ssmweb">web.xml配置SpringMVC中央控制器</h2>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">

    <!--字符集过滤器-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--SpringMVC中央控制器-->
    <servlet>
        <servlet-name>mvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>mvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--在web容器启动时加载Spring配置，启动Spring容器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring*.xml</param-value>
    </context-param>
</web-app>
```
<h2 id="mvc">mvc-servlet.xml配置扫描中央控制器、视图解析器</h2>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--开启自动扫描-->
    <context:component-scan base-package="com.ma.controller">
        <!--防止其他扫描冲突，配置其他扫描在扫描时，放弃扫描该包注解下的类-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--新特性中无名氏配置-->
    <mvc:annotation-driven/>

    <!--放行静态资源-->
    <!--<mvc:default-servlet-handler/>-->

    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.UrlBasedViewResolver">
        <!--支持JSTL的配置项-->
        <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
        <!--视图前缀-->
        <property name="prefix" value="/WEB-INF/view/"/>
        <!--视图后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--文件上传解析器-->
    <!--<bean class="org.springframework.web.multipart.commons.CommonsMultipartResolver" id="multipartResolver">-->
        <!--<property name="maxUploadSize" value="10485760"/>-->
    <!--</bean>-->

</beans>
```
<h2 id="ssmspring">Spring.xml配置扫描Bean,数据源和事务</h2>

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


    <!--使用注解时，bean使用自动扫描，AOP使用自动代理-->
    <context:component-scan base-package="com.ma">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--数据库配置信息位置-->
    <context:property-placeholder location="classpath:config.properties"/>


    <!--Spring jdbc数据源的配置-->
    <bean class="org.apache.commons.dbcp2.BasicDataSource" id="dataSource">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>


    <!--事务-->
    <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager" id="transactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!--开启事务-->
    <tx:annotation-driven transaction-manager="transactionManager"/>

    <!--MyBatis sqlSessionFactory-->
    <bean class="org.mybatis.spring.SqlSessionFactoryBean" id="sessionFactory">
        <!--引用数据源-->
        <property name="dataSource" ref="dataSource"/>
        <!--mapper.xml文件的位置-->
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
        <!--别名-->
        <property name="typeAliasesPackage" value="com.ma.entity"/>
        <!--分页插件配置-->
        <property name="plugins">
            <array>
                <bean class="com.github.pagehelper.PageInterceptor">
                    <property name="properties">
                        <value>helperDialect=mysql</value>
                    </property>
                </bean>
            </array>
        </property>
        <!--数据库字段名下划线映射配置-->
        <property name="configuration">
            <bean class="org.apache.ibatis.session.Configuration">
                <property name="mapUnderscoreToCamelCase" value="true"/>
            </bean>
        </property>
    </bean>

    <!--自动扫描mapper接口，并产生实体类交给Spring管理-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.ma.mapper"/>
    </bean>

</beans>
```
<h2 id="Spring-Quartz">Spring-Quartz.xml</h2>

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


    <!--动态任务-->
    <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean" id="schedulerFactoryBean">
        
        <!--数据源-->
        <property name="dataSource" ref="dataSource"/>
        <!--事务管理器-->
        <property name="transactionManager" ref="transactionManager"/>
        <!--quartz-properties.xml文件的位置-->
        <property name="configLocation" value="classpath:/quartz.properties"/>
        <!--如果任务相同是否覆盖数据库中的任务，false会报错-->
        <property name="overwriteExistingJobs" value="true"/>

    </bean>


</beans>
```
<h2 id="Spring-ActiveMQ">Spring-ActiveMQ.xml</h2>

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

<!--消息创建端-->
    <!--配置MQ链接工厂-->
    <bean id="activeMQConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="tcp://127.0.0.1:61616"/>
    </bean>

    <!--配置SpringleMsFactory-->
    <bean id="singleConnectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="activeMQConnectionFactory"/>
    </bean>

    <!--配置JMSTemplete-->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <property name="connectionFactory" ref="singleConnectionFactory"/>
    </bean>


<!--消息消费端-->

    <!--配置MQ链接工厂-->
    <bean id="activeMQConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="tcp://127.0.0.1:61616"/>
    </bean>

    <!--配置SpringleMsFactory-->
    <bean id="singleConnectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory">
        <property name="targetConnectionFactory" ref="activeMQConnectionFactory"/>
    </bean>

    <!--配置JMSTemplete-->
    <bean id="jmsListenerContainerFactory" class="org.springframework.jms.config.DefaultJmsListenerContainerFactory">
        <property name="connectionFactory" ref="singleConnectionFactory"/>
    </bean>
    <!--开启基于注解的消费端-->
    <jms:annotation-driven container-factory="jmsListenerContainerFactory"/>





</beans>
```
<h2 id="Hibernate.cfg">Hibernate.cfg.xml</h2>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>

    <session-factory>

        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql:///mybatis?useSSL=false</property>
        <property name="connection.username">root</property>
        <property name="connection.password">123456</property>

        <!--方言-->
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
        <!--池配置-->
        <property name="c3p0.max_size">2</property>
        <property name="c3p0.min_size">2</property>
        <property name="c3p0.timeout">5000</property>
        <property name="c3p0.max_statements">100</property>
        <property name="c3p0.idle_test_period">3000</property>
        <property name="c3p0.acquire_increment">2</property>
        <property name="c3p0.validate">false</property>
        <!--打印语句-->
        <property name="show_sql">true</property>

        <property name="current_session_context_class">thread</property>

        <!--映射文件注册-->
        <mapping resource="hbm/Students.hbm.xml"/>
        <mapping resource="hbm/Article.hbm.xml"/>
        <mapping resource="hbm/Node.hbm.xml"/>

    </session-factory>

</hibernate-configuration>
```
<h2 id="hbm">XXX.hbm.xml</h2>

```xml
<!--例子,t_article表为多表,t_node为一表-->

<!--Article.hbm.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.ma.pojo">
    <class name="Article" table="t_article">
        <id name="id">
            <generator class="native"/>
        </id>
        <property name="title" column="title"/>
        <!--
        nodeid为外键,在Article类中使用Node作为属性
        name:属性名
        class:对象属性
        column:数据库字段名
        fetch:避免N+1
        -->
        <many-to-one name="node" class="Node" column="nodeid" fetch="join"/>
    </class>

</hibernate-mapping>

<!--Node.hbm.xml-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.ma.pojo">
    <class name="Node" table="t_node">
        <id name="nid" column="nid">
            <generator class="native"/>
        </id>
        <property name="nodeName" column="nodename"/>

        <set name="articleSet">
            <key column="nodeid"></key>
            <one-to-many class="Article"/>
        </set>

    </class>

</hibernate-mapping>

```
```java
<!--Article.java-->
public class Article {
    private Integer id;
    private String title;
    private Node node;
}    

<!--Node.java-->
public class Node {
    private Integer nid;
    private String nodeName;
    private Set<Article> articleSet;
}
```
<h2 id="dubbo">Dubbo.xml</h2>

```xml
<!--微服务提供端-->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <!--服务名称-->
    <dubbo:application name="UserService"/>

    <!--注册中心-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>

    <!--dubbo协议和端口-->
    <dubbo:protocol host="127.0.0.1" name="dubbo" port="20880"/>

    <!--暴露服务-->
    <bean id="userService" class="com.ma.service.impl.UserServiceImpl"/>
    <dubbo:service interface="com.ma.service.UserService" ref="userService"/>
</beans>

```
```xml
<!--微服务消费端-->

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <dubbo:application name="UserConsumer"/>
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>
    <!--需要哪个服务-->
    <dubbo:reference interface="com.ma.service.UserService" id="myUserService"/>

</beans>

```
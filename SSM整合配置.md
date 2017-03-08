#从业务看SSM整合配置

##web.xml

```xml 
  <?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.0" 
    xmlns="http://java.sun.com/xml/ns/javaee" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
    http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
  <display-name></display-name>    
  
  <!-- 404错误拦截 -->
  <error-page>
    <error-code>404</error-code>
    <location>/error404.jsp</location>
  </error-page>
  <!-- 500错误拦截 -->
  <error-page>
    <error-code>500</error-code>
    <location>/error500.jsp</location>
  </error-page>
  
  <!-- 加载spring容器 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>WEB-INF/classes/spring/applicationContext-*.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    
  <!-- 配置前端控制器 -->
  <servlet>
      <servlet-name>spring</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <init-param>
          <!-- ContextconfigLocation配置springmvc加载的配置文件
          适配器、处理映射器等
           -->
          <param-name>contextConfigLocation</param-name>
          <param-value>WEB-INF/classes/spring/springmvc.xml</param-value>
  </init-param>
  </servlet>
  <servlet-mapping>
      <servlet-name>spring</servlet-name>
      <!-- 1、.action访问以.action结尾的  由DispatcherServlet进行解析
           2、/,所有访问都由DispatcherServlet进行解析
       -->
      <url-pattern>/</url-pattern>
  </servlet-mapping>
    
  <!-- 解决post乱码问题的过滤器 -->
  <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>utf-8</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  <welcome-file-list>
    <welcome-file>welcome.jsp</welcome-file>
  </welcome-file-list>
  
</web-app>
  <!--
  这里需要注意，springmvc.xml是spring配置文件，将在后面讨论。在<servlet-mapping>中url如果是.action，
  前端控制器就只会拦截以.action结尾的请求，并不会理会静态的文件。对静态页面的控制就要通过其他的手段。以/作为url的话就会拦截所有的请求，
  包括静态页面的请求。这样的话就可以拦截任何想要处理的请求，但是有一个问题。如果拦截了所有的请求，
  如果不在拦截器中做出相应的处理那么所有静态的js、css以及页面中用到的图片就会访问不到造成页面无法正常显示。
  但这可以通过静态资源的配置来解决这个问题。后面会提到
  -->
  
  
```
##springmvc.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xmlns:jdbc="http://www.springframework.org/schema/jdbc"
xmlns:jee="http://www.springframework.org/schema/jee"
xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd">
    
<!--   需要注意XML的命名空间  -->
     <!-- 配置视图解析器 -->
     <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
         <!-- 使用前缀和后缀 -->
         <property name="prefix" value="/"></property>
         <property name="suffix" value=".jsp"></property>
     </bean>
     
     <!-- 使用组件扫描的方式可以一次扫描多个Controller -->
     <context:component-scan base-package="com.wxisme.ssm.controller">
     </context:component-scan>
     <!-- 配置注解的处理器映射器和处理器适配器 -->
    <mvc:annotation-driven conversion-service="conversionService"></mvc:annotation-driven>
    
    <!-- 自定义参数类型绑定 -->
    <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
     <property name="converters">
         <list>
             <!-- 日期类型绑定 -->
             <bean class="com.wxisme.ssm.controller.converter.DateConverter"/>
         </list>
     </property>
    </bean>
    
    
    <!-- 访问静态资源文件 -->
    <!-- <mvc:default-servlet-handler/> 需要在web.xml中配置-->
<!--   完全可以不拦截所有路径，可避免静态资源访问问题  -->
    <mvc:resources mapping="/images/**" location="/images/" />
    <mvc:resources mapping="/css/**" location="/css/" />  
    <mvc:resources mapping="/js/**" location="/js/" />
    <mvc:resources mapping="/imgdata/**" location="/imgdata/" />
    
    <!-- 定义拦截器 -->
    <mvc:interceptors>
    <!-- 直接定义拦截所有请求 -->
    <bean class="com.wxisme.ssm.interceptor.IdentityInterceptor"></bean>
        <!-- <mvc:interceptor>
            拦截所有路径的请求   包括子路径
            <mvc:mapping path="/**"/>
            <bean class="com.wxisme.ssm.interceptor.IdentityInterceptor"></bean>
        </mvc:interceptor> -->
    </mvc:interceptors>
    
    <!--配置上传文件数据解析器  -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="maxUploadSize">
            <value>9242880</value>
        </property>
    </bean>
    
    <!-- 定义全局异常处理器 -->
    <!-- 只有一个全局异常处理器起作用 -->
    <bean id="exceptionResolver" class="com.wxisme.ssm.exception.OverallExceptionResolver"></bean>
    
 </beans>
```

##applicationContext-*.xml
```xml
<!-- 包含3个配置文件，分别对应数据层控制、业务逻辑service和事务控制 -->
```

###数据访问层，applicationContext-dao.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xmlns:jdbc="http://www.springframework.org/schema/jdbc"
xmlns:jee="http://www.springframework.org/schema/jee"
xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd">

<!-- 加载数据库连接的资源文件 -->
<context:property-placeholder location="/WEB-INF/classes/jdbc.properties"/>

<!-- 配置数据源   dbcp数据库连接池 jar包-->
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
</bean>
	<!-- spring ,mybatis整合配置 SqlMapConfig.xml在后面-->
<!-- 配置sqlSessionFactory -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <!-- 数据库连接池 -->
    <property name="dataSource" ref="dataSource"/>
    <!-- 加载Mybatis全局配置文件 -->
    <property name="configLocation" value="/WEB-INF/classes/mybatis/SqlMapConfig.xml"/>
</bean>

<!-- 配置mapper扫描器 扫描mapper包下的所有mapper文件和类，要求mapper配置文件和类名需要一致 -->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <!-- 扫描包路径，如果需要扫描多个包中间用半角逗号隔开 -->
    <property name="basePackage" value="com.wxisme.ssm.mapper"></property>
    <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
</bean>

</beans>
```

>jdbc.properties
```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/database
jdbc.username=root
jdbc.password=1234
```
###applicationContext-service.xml
```xml
<!-- 这个文件里暂时只需要定义service的实现类即可 -->
<!-- 定义service -->
<bean id="userService" class="com.wxisme.ssm.service.impl.UserServiceImpl"/>
```
###applicationContext-transaction.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:context="http://www.springframework.org/schema/context"
xmlns:mvc="http://www.springframework.org/schema/mvc"
xmlns:jdbc="http://www.springframework.org/schema/jdbc"
xmlns:jee="http://www.springframework.org/schema/jee"
xmlns:aop="http://www.springframework.org/schema/aop"
xmlns:tx="http://www.springframework.org/schema/tx"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context.xsd
http://www.springframework.org/schema/mvc
http://www.springframework.org/schema/mvc/spring-mvc.xsd
http://www.springframework.org/schema/tx
http://www.springframework.org/schema/tx/spring-tx.xsd
http://www.springframework.org/schema/aop
http://www.springframework.org/schema/aop/spring-aop.xsd">
    
<!-- 事务控制  对MyBatis操作数据库  spring使用JDBC事务控制类 -->

<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!-- 配置数据源 -->
    <property name="dataSource" ref="dataSource"/>
</bean>
    
    <!-- 通知 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 传播行为 -->
            <tx:method name="save*" propagation="REQUIRED"/>
            <tx:method name="insert*" propagation="REQUIRED"/>
            <tx:method name="update*" propagation="REQUIRED"/>
            <tx:method name="delete*" propagation="REQUIRED"/>
            <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
            <tx:method name="select*" propagation="SUPPORTS" read-only="true"/>
            
            
        </tx:attributes>
    </tx:advice>
    
    <!-- 配置aop  -->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut="execution(* com.wxisme.ssm.service.impl.*.*(..))"/>
    </aop:config>
    
    
</beans>
```
##mybatis.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

<!-- 将数据库连接数据抽取到属性文件中方便测试 -->
<!-- <properties resource="/WEB-INF/classes/jdbc.properties"></properties> -->
<!-- 别名的定义 -->
<typeAliases>
    <!-- 批量定义别名 ，指定包名，自动扫描包中的类，别名即为类名，首字母大小写无所谓-->
    <package name="com.wxisme.ssm.po"/>
</typeAliases>

<!-- 数据库连接用数据库连接池 -->

<mappers>
    <!-- 通过扫描包的方式来进行批量加载映射文件 -->
    <package name="com.wxisme.ssm.mapper"/>
</mappers>
</configuration>
```

##pom.xml
```xml
<properties>
<!-- spring版本号 -->
		<spring.version>4.0.2.RELEASE</spring.version>
		<!-- mybatis版本号 -->
		<mybatis.version>3.2.6</mybatis.version>
		<!-- log4j日志文件管理包版本 -->
		<slf4j.version>1.7.7</slf4j.version>
		<log4j.version>1.2.17</log4j.version>
</properties>
```

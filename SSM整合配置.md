#从业务看SSM整合配置

##web.xml
>这个文件配置的是哪方面的业务
```xml
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

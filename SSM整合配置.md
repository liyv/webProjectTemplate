#从业务看SSM整合配置

##web.xml
>这个文件配置的是哪方面的业务
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.0" 
    xmlns="http://java.sun.com/xml/ns/javaee" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
    http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
  <display-name></display-name>    
  

 
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
  这里需要注意，springmvc.xml是spring配置文件，将在后面讨论。在<servlet-mapping>中url如果是.action，前端控制器就只会拦截以.action结尾的请求，并不会理会静态的文件。对静态页面的控制就要通过其他的手段。以/作为url的话就会拦截所有的请求，包括静态页面的请求。这样的话就可以拦截任何想要处理的请求，但是有一个问题。如果拦截了所有的请求，如果不在拦截器中做出相应的处理那么所有静态的js、css以及页面中用到的图片就会访问不到造成页面无法正常显示。但这可以通过静态资源的配置来解决这个问题。后面会提到
  -->
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

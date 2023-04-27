# :seedling: Spring #12

### SpringBoot

**#1 Spring Starter Project**

![image](https://user-images.githubusercontent.com/68459918/234767893-c93a4d90-59cd-4abe-a8ad-b1fd4d957c56.png)

![image](https://user-images.githubusercontent.com/68459918/234767459-178047c4-8c34-49d4-aeb0-773dd45af64d.png)



**#2 File Import**

![image](https://user-images.githubusercontent.com/68459918/234768257-91af2e55-39fc-42ea-8828-388b9049989e.png)

![image-20230427143446916](C:\Users\SSAFY\Desktop\GIT\TIL\05_spring\assets\image-20230427143446916.png)





**#3 setting**

:page_facing_up: **pom.xml**

```xml
<!-- https://mvnrepository.com/artifact/org.apache.tomcat.embed/tomcat-embed-jasper -->
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
</dependency>

<!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl -->
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>
```

:page_facing_up: **servlet-context.xml**

```xml
<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<beans:property name="prefix" value="/WEB-INF/views/" />
	<beans:property name="suffix" value=".jsp" />
</beans:bean>
```

**:leaves: application.properties**

```properties
# JSP path
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```



:page_facing_up: **root-context.xml**

```xml
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"</property>
	<property name="url" 
              value="jdbc:mysql://localhost:3306/ssafydb?serverTimezone=UTC">	
    </property>
    
	<property name="username" value="ssafy"></property>
	<property name="password" value="ssafy"></property>
</bean>


<!-- MyBatis를 사용하기 위한 sqlSessionFactory를 등록한다. -->
<bean id="sqlSessionFactory"
		class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource" />
	<!-- mapper xml 파일의 경로를 ant 표현식의 형태로 사용한다. -->
	<property name="mapperLocations"
		value="classpath*:mappers/**/*.xml" />
	<!-- mapper에서 사용할 DTO들의 기본 패키지를 등록한다. -->
	<property name="typeAliasesPackage" value="com.ssafy.hw.model.dto">
    </property>
</bean>
```

**:leaves: application.properties**

```properties
# dataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ssafy_board?serverTimezone=UTC
spring.datasource.username=ssafy
spring.datasource.password=ssafy
  		
# mybatis
mybatis.mapper-locations=classpath*:mappers/*.xml
mybatis.type-aliases-package=com.ssafy.board.model.dto
```


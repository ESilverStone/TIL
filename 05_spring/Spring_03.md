# :seedling: Spring #3

### Spring Web MVC

* **DispatcherServlet** : 클라이언트 요청처리(요청 및 처리 결과 전달)
* **HandlerMapping** : 요청을 어떤 Controller가 처리할 지 결정
* **Controller** : 요청에 따라 수행할 메서드를 선언하고, 요청처리를 위한 로직 수행(비즈니스 로직 호출)
* **ModelAndView** : 요청처리를 하기 위해서 필요한 혹은 그 결과를 저장하기 위한 객체
* **ViewResolver** : Controller에 선언된 view이름을 기반으로 결과를 반환할 View를 결정
* **View** : 응답화면 생성



![image](https://user-images.githubusercontent.com/68459918/231642449-b6878c84-11ef-4615-9763-1b0e7eca6e63.png)

**Spring MVC 요청처리 흐름**

1. 클라이언트 요청이 들어오면 DispatcherServlet이 받는다.
2. HandlerMapping이 어떤 Controller가 요청을 처리할지 결정한다. 
3. DispatcherServlet은 Controller에 요청을 전달
4. Controller는 요청을 처리한다. 
5. 결과(요청처리를 위한 data, 결과를 보여줄 view의 이름)를 ModelAndView에 담아 반환
6. ViewResolver에 의해서 실제 결과를 처리할 View를 결정하고 반환
7. 결과를 처리할 View에 ModelAndView를 전달
8. DispatcherServlet은 View가 만들어낸 결과를 응답



**DispatcherServlet 생성**

:open_file_folder: **webapp**

└ :open_file_folder: **WEB-INF**

​	└ :page_facing_up: **web.xml**

```html
<servlet>
	<servlet-name>appServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet
	</servlet-class>
	<init-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/appServlet/servlet-context.xml
		</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
```

![image](https://user-images.githubusercontent.com/68459918/233013302-9d192f87-0045-4b14-bab9-2ab85b77bb53.png)

```html
<!-- The definition of the Root Spring Container shared by all Servlets 
		and Filters -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>/WEB-INF/spring/root-context.xml</param-value>
</context-param>

<!-- Creates the Spring Container shared by all Servlets and Filters -->
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener
	</listener-class>
</listener>
```

![image](https://user-images.githubusercontent.com/68459918/233014262-bd53cc4b-04eb-485c-b44c-5cb4b32dbcea.png)

└ :open_file_folder: **WEB-INF.spring.appServlet**

​	└ :page_facing_up: **servlet-context.xml**

```html
<!-- Resolves views selected for rendering by @Controllers to .jsp resources 
		in the /WEB-INF/views directory -->
<beans:bean
	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<beans:property name="prefix" value="/WEB-INF/views/" />
	<beans:property name="suffix" value=".jsp" />
</beans:bean>

<context:component-scan
	base-package="com.ssafy.ws.controller, com.ssafy.ws.interceptor" />
```

![image](https://user-images.githubusercontent.com/68459918/233018200-b66703f3-02ba-4c20-9bc3-774efc3cda11.png)



:package: **com.ssafy.pjt**

└ :page_facing_up: **HomeController.java**

```
```


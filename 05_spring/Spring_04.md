# :seedling: Spring #4

### Interceptor

* **HandlerInterceptor**(인터페이스)를 구현한 것. 따라서, implements HandlerInterceptor으로 구현해준다.
* 요청(requests)을 처리하는 과정에서 요청을 가로채서 처리
* 접근제어, 로그 등 비즈니스 로직과 구분되는 반복적이고 부수적인 로직 처리
* HandlerIntercepter의 주요 메서드
  * **preHandle()**
  * **postHandle()**
  * **afterCompletion()**




### preHandle

* Controller(핸들러) 실행 이전에 호출
* false를 반환하면 요청을 종료한다.

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		
	System.out.println("A : preHandle");
	return true;
}
```



### postHandle

* Controller(핸들러) 실행 후 호출
* 정상 실행 후 추가 기능 구현 시 사용
* Controller에서 예외 발생 시 해당 메서드는 실행되지 않음

```java
@Override
public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
	System.out.println("A : postHandle");
}
```



### afterCompletion

* 뷰가 클라이언트에게 응답을 전송한 뒤 실행.
* Controller에서 예외 발생시, 네번째 파라미터로 전달이 된다. (기본은 null)
* 따라서, Controller에서 발생한 예외 혹은 실행 시간 같은 것들은 기록하는 등 후처리 시 주로 사용.

```java
@Override
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
		
	System.out.println("A : afterComplection");
}
```



### Interceptor의 흐름

![image](https://user-images.githubusercontent.com/68459918/232398893-47cd3de8-22f0-4c3c-84f4-d95d8378294d.png)

```html
<interceptors>
	<interceptor>
		<mapping path="/*"/>
		<beans:bean class="com.ssafy.mvc.interceptor.AInterceptor"/>
	</interceptor>
	<interceptor>
		<mapping path="/*"/>
		<beans:bean class="com.ssafy.mvc.interceptor.BInterceptor"/>
	</interceptor>
	<interceptor>
		<mapping path="/*"/>
		<beans:bean class="com.ssafy.mvc.interceptor.CInterceptor"/>
	</interceptor>
</interceptors>
```

* servlet-context.xml에  A-B-C순으로 beans객체를 생성하였다면 아래와 같은 순으로 interceptor처리가 이루어진다. 

![image-20230417151713263](C:\Users\SSAFY\Desktop\GIT\TIL\05_spring\assets\image-20230417151713263.png)



### login Interceptor

:package: **com.ssafy.mvc.controller**

└ :page_facing_up: **HomeController.java**

```java
@Controller
public class HomeController {
	
	private static final Logger logger = LoggerFactory.getLogger(HomeController.class);
	
	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String home(Locale locale, Model model) {
		logger.info("Welcome home! The client locale is {}.", locale);
		
		Date date = new Date();
		DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
		
		String formattedDate = dateFormat.format(date);
		
		model.addAttribute("serverTime", formattedDate );
		
		return "home";
	}
	
	//regist라고 하는 요청이 들어오면 regist.jsp 로 보내면 좋겠어
	//그런데 요청이 a태그를 이용해서 날아왔어... 
	@GetMapping("regist")
	public String regist() {
		//로그인 유무를 파악해서... 
		//로그인 했으면 계속 고
		//로그인 안했으면 다시 메인으로 돌아가...
		return "regist";
	}
	
	
	@GetMapping("login")
	public String loginForm() {
		return "login";
	}
	
	@PostMapping("login")
	public String login(HttpSession session, String id, String pw) {
		
		//id와 pw를 이용해서 실제 로그인을 해야겠지요.
		//Service를 만들어서 Dao DB
		if(id.equals("ssafy") && pw.equals("1234")) {
			session.setAttribute("id", "ssafy");
			return "redirect:/";
		}
		//아니라면 로그인페이지로 다시가라. 실패했으니까
		return "redirect:/login";
	}
	
	@GetMapping("logout")
	public String logout(HttpSession session) {
		session.removeAttribute("id");
		return "redirect:/";
	}
}
```

:package: **com.ssafy.mvc.interceptor**

└ :page_facing_up: **LoginInterceptor.java**

```java
public class LoginInterceptor implements HandlerInterceptor {
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
		//요기서 한번 검사를 해야겠다.
		HttpSession session = request.getSession();
		//로그인할때 이렇게 id를 직접 세션에 담아 두었다라고 가정
		if(session.getAttribute("id") == null) {
			response.sendRedirect("login");
			return false;
		}
		return true; //ok 통과	
	}
}
```

:open_file_folder: **appServlet**

└ :page_facing_up: **servlet-context.xml**

```html
<context:component-scan base-package="com.ssafy.mvc.controller" />
	
	<beans:bean class="com.ssafy.mvc.interceptor.LoginInterceptor" id="confirm"></beans:bean>
	
<interceptors>
	<interceptor>
		<mapping path="/*"/>
		<exclude-mapping path="/login"/>
		<exclude-mapping path="/"/>
		<beans:ref bean="confirm"/>
	</interceptor>
</interceptors>
```

:open_file_folder: **views**

└ :page_facing_up: **home.jsp**

```html
<body>
	<c:choose>
		<c:when test="${empty id }">
			<a href="login">로그인 페이지</a>
		</c:when>
		<c:otherwise>
			${id}님 반갑습니다. <a href="logout">로그아웃</a>
		</c:otherwise>
	</c:choose>

	<h1>Hello world!</h1>
	<P>The time on the server is ${serverTime}.</P>
	
	<!-- regist.jsp로 보내는 버튼을 하나 만들고 싶다. -->
	<a href="regist">regist.jsp 로 가즈아</a>
</body>
```

└ :page_facing_up: **login.jsp**

```html
<body>
	<h2>로그인페이지입니다.</h2>
	<form action="login" method="POST">
		<input type="text" name="id"><br>
		<input type="password" name="pw"><br>
		<input type="submit" ><br>
	</form>
</body>
```

└ :page_facing_up: **regist.jsp**

```html
<body>
	<h2>로그인해야 들어올 수 있는 페이지</h2>
</body>
```


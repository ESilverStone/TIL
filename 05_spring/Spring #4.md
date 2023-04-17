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


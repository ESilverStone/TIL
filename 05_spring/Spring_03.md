# :seedling: Spring #3

### Spring Web MVC

* **DispatcherServlet** : 클라이언트 요청처리(요청 및 처리 결과 전달)
* **HandlerMapping** : 요청을 어떤 Controller가 처리할 지 결정
* **Controller** : 요청에 따라 수행할 메서드를 선언하고, 요청처리를 위한 로직 수행(비즈니스 로직 호출)
* **ModelAndView** : 요청처리를 하기 위해서 필요한 혹은 그 결과를 저장하기 위한 객체
* **ViewResolver** : Controller에 선언된 view이름을 기반으로 결과를 반환할 View를 결정
* **View** : 응답화면 생성



![image](https://user-images.githubusercontent.com/68459918/231642449-b6878c84-11ef-4615-9763-1b0e7eca6e63.png)

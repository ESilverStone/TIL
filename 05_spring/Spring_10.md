# :seedling: Spring #10

### REST API

**@ResponseBody** : JSP같은 뷰로 전달되는 것이 아니라 데이터 자체를 전달

```java
@Controller
@RequestMapping("/rest1")
public class TestController {

	// http://localhost:8080/mvc/rest1/test1 > 404 error
	@GetMapping("/test1")
	public String test1() {
		return "hi rest";
	}

	// http://localhost:8080/mvc/rest1/test2 > @responsebody : jsp 화면 대신 데이터를 넘긴다.
	@GetMapping("/test2")
	@ResponseBody
	public String test2() {
		return "hi rest";
	}

	// http://localhost:8080/mvc/rest1/test3 > @responsebody : jsp 화면 대신 데이터를 넘긴다.
	@GetMapping("/test3")
	@ResponseBody
	public Map<String, String> test3() {
		// Map은 키와 밸류를 가지고 있음
		Map<String, String> data = new HashMap<>();

		data.put("id", "ssafy");
		data.put("password", "1234");
		data.put("name", "갓은석");

		return data; // jackson을 추가했더니 요거 맵인데도 json으로 바꿔서 돌려주더라 오
	}

	// http://localhost:8080/mvc/rest1/test4 > DTO 변환. 짹슨이 객체도 알아서 Json형태로 바꾸어준다.
	@GetMapping("/test4")
	@ResponseBody
	public User test4() {

		User user = new User();
		user.setId("ssafy");
		user.setPassword("1234");
		user.setName("디아스");

		return user;
	}

	// http://localhost:8080/mvc/rest1/test5 > List 반환해보자.
	@GetMapping("/test5")
	@ResponseBody
	public List<User> test5() {

		List<User> list = new ArrayList<User>();
		
		for(int i=1; i<=5; i++) {
			User user = new User();
			user.setId("ssafy"+i);
			user.setPassword("1234");
			user.setName("후뱅디아스"+"bot"+i);
			list.add(user);
		}
		
		return list;
	}
}
```

**@RestController** : Controller가 REST 방식을 처리하기 위한 것임을 명시

```java
@RestController
@RequestMapping("/rest2")
public class TestController2 {
	@GetMapping("/test1")
	public String test1() {
		return "hi rest";
	}
}
```

**@Get,Post,Put,DeleteMapping** : 요청 방식

```java
@RestController
@RequestMapping("/rest3")
public class TestController3 {

	@GetMapping("/test1")
	public String test1() {
		return "GET";
	}
	@PostMapping("/test2")
	public String test2() {
		return "POST";
	}
	@PutMapping("/test3")
	public String test3() {
		return "PUT";
	}
	@DeleteMapping("/test4")
	public String test5() {
		return "DELETE";
	}
}
```

**@PathVariable** : URL 경로에 있는 값을 파라미터로 추출

**@RequestBody** : JSON 데이터를 원하는 타입으로 바인딩

**ResponseEntity** : 데이터 응답시 [상태코드, 헤더, 데이터] 설정이 가능

```java
@RestController
@RequestMapping("/rest4")
public class TestController4 {

//	@PathVariable : url 경로에 있는 값을 파라미터로 추출하겠다. 
//	http://localhost:8080/컨텍스트루트/rest4/board/1
//	http://localhost:8080/컨텍스트루트/rest4/board/2
//	http://localhost:8080/컨텍스트루트/rest4/board/99 해당 글의 번호가 들어왔다. 

//	@GetMapping("/board/{id}")
//	public String test1(@PathVariable int id) {
//		return "#"+ id + " board";
//	}

	@GetMapping("/board/{id}")
	public String test1(@PathVariable("id") int no) {
		return "#" + no + " board";
	}

	// 게시글 등록을 해보자
	// application/x-www-form-urlencoded : 폼으로 넘기면 알아서 잘 들어간다.
//	@PostMapping("/board")
//	public String test2(User user) {
//
//		return user.toString();
//	}

	// JSON 형태로 값을 보내면 저기 user라는 바구니 넣어버리고 싶다. 
//	{
//		"name" : "one paper push",
//	    "id" : "ssafy",
//	    "password" : "1234"
//	    
//	}
	//@RequestBody : 원하는 형태로 변환해준다. 
	@PostMapping("/board")
	public String test3(@RequestBody User user) {

		return user.toString();
	}

	// responseEntity : 응답하려고 하는 데이터 / 응답 상태코드 / 응답 헤더같은 것을 결정할 수 있음
	@GetMapping("/test4")
	public ResponseEntity<String> test4() {
		
		HttpHeaders headers = new HttpHeaders();
		headers.add("auth", "admin");
		
		return new ResponseEntity<String>("OK", headers, HttpStatus.CREATED);
	}
}
```



Talend API Tester에서 테스트 가능





### 실습

:page_facing_up: **UserRestController**

```java
@RestController
@RequestMapping("/userapi")
@CrossOrigin(origins = "*", methods = { RequestMethod.GET, RequestMethod.POST })
public class UserRestController {

	@Autowired
	UserService us;

	@GetMapping("/user")
	@ApiOperation(value = "등록된 모든 사용자 정보를 반환한다.", response = User.class)
	public ResponseEntity<?> selectAll() {
		try {
			List<User> users = us.selectAll();
			if (users.isEmpty()) {
				return ResponseEntity.noContent().build();
			} else {
				return ResponseEntity.ok(users);
			}
		} catch (Exception e) {
			return exceptionHandling(e);
		}
	}

	@GetMapping("/user/{id}")
	@ApiOperation(value = "{id}에 해당하는 사용자 정보를 반환한다.", response = User.class)
	public ResponseEntity<?> select(@PathVariable String id) {
		try {
			User user = us.searchById(id);
			if (user.getId() == null) {
				return ResponseEntity.noContent().build();
			} else {
				return ResponseEntity.ok(user);
			}
		} catch (Exception e) {
			return exceptionHandling(e);
		}
	}

	@GetMapping("/user/search")
	@ApiOperation(value = "SearchCondition 에 부합하는 조건을 가진 사용자 목록을 반환한다.", response = User.class)
	public ResponseEntity<?> search(SearchCondition con) {
		try {
			List<User> users = us.searchByCondition(con);
			if (users.isEmpty()) {
				return ResponseEntity.noContent().build();
			} else {
				return ResponseEntity.ok(users);
			}
		} catch (Exception e) {
			return exceptionHandling(e);
		}
	}

	@PostMapping("/user")
	@ApiOperation(value = "사용자 정보를 삽입한다.", response = Integer.class)
	public ResponseEntity<?> insert(User user) {
		try {
			int result = us.insert(user);
			return ResponseEntity.ok(result);

		} catch (Exception e) {
			return exceptionHandling(e);
		}
	}

	private ResponseEntity<String> exceptionHandling(Exception e) {
		e.printStackTrace();
		return new ResponseEntity<String>("Sorry: " + e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
	}
}
```


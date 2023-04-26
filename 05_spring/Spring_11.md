# :seedling: Spring #11

### REST API_Board

**BoardRestController.java**

```java
@RestController
@RequestMapping("/api")
public class BoardRestController {
	
	@Autowired
	private BoardService boardService;
	
	// 1. 목록을 가져와 보자
	@GetMapping("/board")
	public ResponseEntity<List<Board>> list(SearchCondition condition) {
		
//		List<Board> list = boardService.getBoardList();		// 단순히 전체 조회
		List<Board> list = boardService.search(condition);	// 검색 조건이 있으면 그걸로 조회
		
		if(list == null || list.size() == 0)
			return new ResponseEntity<List<Board>>(HttpStatus.NO_CONTENT);
		
		
		return new ResponseEntity<List<Board>>(list, HttpStatus.OK);
	}
	
	// 2. 상세보기
	@GetMapping("/board/{id}")
	public ResponseEntity<Board> detail(@PathVariable int id){
		
		Board board = boardService.readBoard(id);
		
		return new ResponseEntity<Board>(board, HttpStatus.OK);
	}
	
	// 3. 등록
	@PostMapping("/board")
	public ResponseEntity<Board> write(Board board){
		boardService.writeBoard(board);
		// 지금 우리의 게시글은 키가 절대로 중복이 되지 않는다. 그래서 무조건 등록은 된다. 
		// 가끔가다가 혹여나 여기말고 다른 곳에서 문제가 발생해서 글이 등록되지 않았다. 
		// DB에 댕겨올 때 테이블을 변경하는 작업이라면 무엇인가를 하나 돌려줌.. >>> 테이블을 건드린 행의 개수가 반환이 된다.
		// 만약에 0이라면 이거 등록 안된거니까 프론트에게 돌려주어야겠다. 
		// 그게 아니라면 잘 등록이 된거니까 ok ㅆㄱㄴ
				
		return new ResponseEntity<Board>(board, HttpStatus.CREATED);
	}
	
	// 4. 삭제
	@DeleteMapping("/board/{id}")
	public ResponseEntity<Void> delete(@PathVariable int id) {
		boardService.removeBoard(id);
		// 이것도 마찬가지로 반환값이 1이상이어야 실제로 무엇인가 지워진거지 그게 아니면 지워진건 아니다.
		// 이상한 값을 넣어도 그냥 동작을 해버린다. 그러니 쿼리문을 나리지 못하게 사전에 커팅해야함
		// 그리고 권한이 있어야만 지우는 거지 지금과 같은 방식은 남의글도 마구잡이로 지울 수 있다. 
		
		return new ResponseEntity<Void>(HttpStatus.OK);
	}
	
	// 5. 수정
	@PutMapping("/board")	// JSON 형태의 데이터로 요청을 보내보자
	public ResponseEntity<Void> update(@RequestBody Board board) {
		boardService.modifyBoard(board);
		// 얘도 마찬가지 인비다
		
		return new ResponseEntity<Void>(HttpStatus.OK);
	}
}
```



### Swagger

* 기본 문서로 사용하던 문제를 해결하기 위해 Swagger를 사용
* 간단한 설정으로 프로젝트의 API목록을 웹에서 확인 및 테스트 할 수 있게 해주는 Library
* Swagger를 사용하면 Controller에 정의되어 있는 모든 URL을 바로 확인할 수 있다. 
* API 목록 뿐 아니라 API의 명세 및 설명도 볼 수 있으며, 또한 API를 직접 테스트해 볼 수도 있다. 



**setting**

:page_facing_up: **pom.xml**

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>3.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>3.0.0</version>
</dependency>
```

:page_facing_up: **servlet-context.xml**

```xml
<resources location="classpath:/META-INF/resources/webjars/springfox-swagger-ui/" mapping="/swagger-ui/**"></resources>
	
<beans:bean id="swagger2Config" class="com.ssafy.board.config.SwaggerConfig"/>
```

:package: **config**

└ :page_facing_up: **SwaggerConfig.java**

```java
@EnableSwagger2
public class SwaggerConfig {
	
	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2)
				.select()
				.apis(RequestHandlerSelectors.basePackage("com.ssafy.board.controller"))
				.paths(PathSelectors.ant("/*/api/**"))
				.build()
				.apiInfo(apiInfo());
	}
	
	private ApiInfo apiInfo() {
		return new ApiInfoBuilder()
				.title("SSAFY 9기 Board rest API")
				.description("엄청나게 대단한 게시판을 위한 레스트풀한 서버입니다.")
				.version("0.1")
				.build();
	}
}
```

**@API** : Controller가 REST 방식을 처리하기 위한 것임을 명시

**@ApiIgnore** : Class, method에 선언이 가능하며 클라이언트에 노출하고 싶지 않은 경우 사용

**@ApiOperation** : 제공되는 API에 대한 간단한 설명

**@ApiModel** : URL 경로에 있는 값을 파라미터로 추출

**@ApiModelProperty** : 결과로 응답되는 데이터 필드에 대한 설명

**@ApiImplicitParam** : API 요청시 설정하는 파라미터에 대한 설명

**@ApiImplicitParams** : API 요청시 설정하는 파라미터가 여러개일 경우 ApiImplicitParam과 함께 사용

:package: **controller**

└ :page_facing_up: **BoardRestController.java**

```java
@RestController
@RequestMapping("/api")
@Api(tags = "게시판 컨트롤러")
public class BoardRestController {
	
	@Autowired
	private BoardService boardService;
	
	// 1. 목록을 가져와 보자
	@ApiOperation(value="게시글 조회", notes="검색조건도 넣으면 같이 가져옴")
	@GetMapping("/board")
	public ResponseEntity<List<Board>> list(SearchCondition condition) {
		
//		List<Board> list = boardService.getBoardList();		// 단순히 전체 조회
		List<Board> list = boardService.search(condition);	// 검색 조건이 있으면 그걸로 조회
		
		if(list == null || list.size() == 0)
			return new ResponseEntity<List<Board>>(HttpStatus.NO_CONTENT);
		
		
		return new ResponseEntity<List<Board>>(list, HttpStatus.OK);
	}
	
	...위 코드와 동일
	
	// 5. 수정
	@ApiIgnore
	@PutMapping("/board")	// JSON 형태의 데이터로 요청을 보내보자
	public ResponseEntity<Void> update(@RequestBody Board board) {
		boardService.modifyBoard(board);
		// 얘도 마찬가지 인비다
		
		return new ResponseEntity<Void>(HttpStatus.OK);
	}
}
```

:package: **dto**

└ :page_facing_up: **Board.java**

```java
@ApiModel(value="게시판 바구니", description = "게시글 정보")
public class Board {...}
```



### CORS

:package: **config**

└ :page_facing_up: **SwaggerConfig.java**

```java
@CrossOrigin("*")
public class BoardRestController {...}
```


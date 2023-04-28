# :seedling: Spring #13

### SpringBoot_API

**settting**

:page_facing_up: **pom.xml**

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-boot-starter -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-boot-starter</artifactId>
	<version>3.0.0</version>
</dependency>
```

:leaves: **application.properties**

```properties
# dataSource
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/ssafy_board?serverTimezone=UTC
spring.datasource.username=ssafy
spring.datasource.password=ssafy

  		
# mybatis
mybatis.mapper-locations=classpath*:mappers/*.xml
mybatis.type-aliases-package=com.ssafy.board.model.dto

# server.port=9999 > 포트번호 변경시, 80 : url에 뜨지 않음

# context-path
# server.servlet.context-path=/board
```

:page_facing_up: **Spring13BootBoardApiApplication.java**

```java
@SpringBootApplication
public class Spring13BootBoardApiApplication {

	public static void main(String[] args) {
		SpringApplication.run(Spring13BootBoardApiApplication.class, args);
	}
}
```

:package: **com.ssafy.board.config**

└ :page_facing_up: **DBconfig.java**

```java
@Configuration
@MapperScan(basePackages = "com.ssafy.board.model.dao")
public class DBconfig {
}
```

└ :page_facing_up: **Webconfig.java**

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {

	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		registry.addResourceHandler("/**").addResourceLocations("classpath:/static/");

		registry.addResourceHandler("/swagger-ui/**")
				.addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/");
	}

	// 요기에다가
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		// 인터셉터 등로갛면 된다.
	}

	// Cors 에러를 해결하기 위해서 컨트롤러에서 세부화하여 처리할 수도 있지만
	// 전역설정처럼 여기서 한방에 처리할 수도 있음
	@Override
	public void addCorsMappings(CorsRegistry registry) {
		registry.addMapping("/**").allowedOrigins("*"); // .allowedMethods("GET","POST");
	}
}
```

└ :page_facing_up: **SwaggerConfig.java**

```java
@Configuration
public class SwaggerConfig {
	
	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2)
				.select()
				.apis(RequestHandlerSelectors.basePackage("com.ssafy.board.controller"))
				.paths(PathSelectors.ant("/api*/**"))
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



:package: **com.ssafy.board.controller**

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
	@ApiIgnore
	@PutMapping("/board")	// JSON 형태의 데이터로 요청을 보내보자
	public ResponseEntity<Void> update(@RequestBody Board board) {
		boardService.modifyBoard(board);
		// 얘도 마찬가지 인비다
		
		return new ResponseEntity<Void>(HttpStatus.OK);
	}
}
```


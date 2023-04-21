# :seedling: Spring #7

### MyBatis - Spring

**setting**

:open_file_folder: **target**

└ :page_facing_up: **pom.xml**

```html
<!-- MySQL을 쓰기위해서 -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>8.0.21</version>
</dependency>

<!-- 마이바티스 -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.5.9</version>
</dependency>

<!-- 마이바티스 스프링 -->
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis-spring</artifactId>
	<version>2.0.6</version>
</dependency>

<!-- 커넥션을 관리하기 위해서 -->
<dependency>
	<groupId>org.apache.commons</groupId>
	<artifactId>commons-dbcp2</artifactId>
	<version>2.8.0</version>
</dependency>
```

:open_file_folder: **webapp.WEB-INF**

└ :open_file_folder: **spring**

​	└ :page_facing_up: **root-context.xml**

```html
<!-- Root Context: defines shared resources visible to all other web components -->
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
	<property name="url" value="jdbc:mysql://localhost:3306/ssafy_board?serverTimezone=UTC"/>
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
	<property name="username" value="root"/>
	<property name="password" value="ssafy"/>
</bean>


<!-- 마이바티스 사용하기 위한 sqlSessionFactory를 등록한다. -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
  		<property name="mapperLocations" value="classpath*:mappers/*.xml"/>
  		<property name="typeAliasesPackage" value="com.ssafy.board.model.dto"/>
</bean>
```

* commons.dbcp2 : 커넥션을 관리해준다.



### CRUD 실습

:package: **com.ssafy.board.service**

└ :page_facing_up: **BoardService.java (interface)** 

```java
//사용자 친화적으로
public interface BoardService {
	//게시글 전체 조회
	public List<Board> getBoardList();
	
	//게시글 상세조회 (클릭시 읽는거)
	public Board readBoard(int id); 
	
	//게시글 작성
	public void writeBoard(Board board);
	
	//게시글 삭제 
	public void removeBoard(int id);
	
	//게시글 수정
	public void modifyBoard(Board board);
}
```

└ :page_facing_up: **BoardServiceImpl.java** 

```java
@Service // root-context.xml에서 scan해주어야한다. 
public class BoardServiceImpl implements BoardService {
	
	private BoardDao boardDao;
	//Dao 인스턴스를 주입 해준다. 
	@Autowired
	public void setBoardDao(BoardDao boardDao) {
		this.boardDao = boardDao;
	}

	@Override
	public List<Board> getBoardList() {
		System.out.println("모든 게시글을 가지고 왔습니다.");
		return boardDao.selectAll();
	}

	@Override
	public Board readBoard(int id) {
		System.out.println(id+"번의 글을 읽습니다.");
		boardDao.updateViewCnt(id);
		return boardDao.selectOne(id);
	}

	@Override
	public void writeBoard(Board board) {
		System.out.println("게시글을 작성합니다.");
		boardDao.insertBoard(board);
	}

	@Override
	public void removeBoard(int id) {
		System.out.println(id+"번의 글을 삭제합니다.");
		boardDao.deleteBoard(id);
	}

	@Override
	public void modifyBoard(Board board) {
		System.out.println("게시글을 수정합니다.");
		boardDao.updateBoard(board);
	}
}
```

└ :package: **mapper**

​	└ :page_facing_up: **board.xml (DaoImpl의 최신방식, #6내용인데 편하게 볼려고 가져옴)**

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ssafy.board.model.dao.BoardDao">
	<resultMap type="Board" id="boardMap">
		<result column="id" property="id"/>
		<result column="writer" property="writer"/>
		<result column="title" property="title"/>
		<result column="content" property="content"/>
		<result column="view_cnt" property="viewCnt"/>
		<result column="reg_date" property="regDate"/>
	</resultMap>

	<!-- 전체글 조회 -->
	<select id="selectAll" resultType="Board">
		SELECT id, content, writer, title, view_cnt as viewCnt, date_format(reg_date, '%y-%m-%d %H:%i:%s') as regDate
		FROM board;
	</select>
	
	<!-- 상세글 조회 -->
	<select id="selectOne" parameterType="int"  resultMap="boardMap">
		SELECT id, content, writer, title, view_cnt, date_format(reg_date, '%y-%m-%d') as reg_date
		FROM board
		WHERE id = #{id};
	</select>
	
	<!-- 게시글 등록 -->
	<insert id="insertBoard" parameterType="Board">
		INSERT INTO board (title, writer, content)
		VALUES (#{title}, #{writer}, #{content})
	</insert>
	
	<!-- 게시글 삭제 -->
	<delete id="deleteBoard" parameterType="int">
		DELETE FROM board
		WHERE id = #{id}
	</delete>
	
	<!-- 조회수 증가 -->
	<update id="updateViewCnt" parameterType="int">
		UPDATE board
		SET view_cnt = view_cnt+1
		WHERE id = #{id}
	</update>
	
	
	<!-- 게시글 수정 -->
	<!-- 지금 게시글 날짜는 등록 날짜만 존재한다. 나중에 수정날짜도 같이 관리하게 된다면의 상황을보자-->
	<update id="updateBoard" parameterType="Board">
		UPDATE board
		SET title = #{title}, content=#{content}, reg_date = now()
		WHERE id=#{id}
	</update>	
</mapper>
```

:open_file_folder: **webapp.WEB-INF**

└ :open_file_folder: **spring**

​	└ :page_facing_up: **root-context.xml**

```html
<!-- 마이바티스에서 제공하는 scan을 통해 dao 인터페이스의 위치를 지정한다. -->
<mybatis-spring:scan base-package="com.ssafy.board.model.dao"/>
	
<!-- 스캔하여 bean으로 등록 -->
<context:component-scan base-package="com.ssafy.board.model.service"/>
```

└ :open_file_folder: **spring.appServlet**

​	└ :page_facing_up: **servlet-context.xml**

```html
<!-- 스캔하여 bean으로 등록 -->
<context:component-scan base-package="com.ssafy.board.controller" />
```

:open_file_folder: **webapp.WEB-INF**

└ :open_file_folder: **views.common**

​	└ :page_facing_up: **bootstrap.jsp**

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<link
	href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha3/dist/css/bootstrap.min.css"
	rel="stylesheet"
	integrity="sha384-KK94CHFLLe+nY2dmCWGMq91rCGa5gtU4mk92HdvYe+M/SXH301p5ILy+dN9+nJOZ"
	crossorigin="anonymous">
<script
	src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha3/dist/js/bootstrap.bundle.min.js"
	integrity="sha384-ENjdO4Dr2bkBIFxQpeoTz1HIcje39Wm4jDKdf19U8gI4ddQ3GYNS7NTKfAdVQSZe"
	crossorigin="anonymous"></script>
```

:package: **com.ssafy.board**

└ :page_facing_up: **BoardController.java** 

```java
@Controller
public class BoardController {
	
	@Autowired
	private BoardService boardService;
	
	@GetMapping("/")
	public String showIndex() {
		//보여줄게 없어서 리스트로 바로 점프를뛰자
		return "redirect:list";
	}
	
	
	@GetMapping("list")
	public String list(Model model) {
		List<Board> list = boardService.getBoardList();
		model.addAttribute("list", list);
		return "/board/list";
	}
	
	@GetMapping("writeform")
	public String writeform() {
		return "/board/writeform";
	}
	
	@PostMapping("write")
	public String write(Board board) {
		boardService.writeBoard(board);
		return "redirect:list";
	}
	
	@GetMapping("detail")
	public String detail(Model model, int id) {
		Board board = boardService.readBoard(id);
		model.addAttribute("board", board);
		return "/board/detail";
	}
	
	@GetMapping("delete")
	public String delete(int id) {
		boardService.removeBoard(id);
		return "redirect:list";
	}
	
	@GetMapping("updateform")
	public String updateform(Model model, int id) {
		//지금 게시글 하나 가져오는건 readBoard 밖에 없다. 
		model.addAttribute("board", boardService.readBoard(id));
		return "/board/updateform";
	}
	
	@PostMapping("update")
	public String update(Board board) {
		boardService.modifyBoard(board);
		return "redirect:detail?id="+board.getId();
	}
}
```

└ :open_file_folder: **views.board**

​	└ :page_facing_up: **list.jsp**

```html
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
...
<title>게시판 목록</title>
<%@ include file="../common/bootstrap.jsp" %>
</head>
<body>
	<div class="container">
		<h2>게시글 목록</h2>
		<hr>
		<div>
			<table class="table text-center">
				<tr>
					<th>번호</th>
					<th>제목</th>
					<th>글쓰니</th>
					<th>조회수</th>
					<th>등록날짜</th>
				</tr>
				<c:forEach items="${list}" var="board">
					<tr>
						<td>${board.id}</td>
						<td><a href="detail?id=${board.id}">${board.title}</a></td>
						<td>${board.writer}</td>
						<td>${board.viewCnt}</td>
						<td>${board.regDate}</td>
					</tr>
				</c:forEach>
			</table>
			
			<div class="d-flex justify-content-end">
				<a class="btn btn-outline-primary" href="writeform">글등록</a>
			</div>
		</div>
	</div>
</body>
</html>
```

​	└ :page_facing_up: **writeform.jsp**

```html
<title>게시글 등록</title>
<%@ include file="../common/bootstrap.jsp" %>
</head>
<body>
	<div class="container">
		<h2>글작성</h2>
		<form action="write" method="POST">
			<div class="mb-3">
				<label for="title" class="form-label">글제목</label>
				<input type="text" class="form-control" id="title" name="title">
			</div>
			<div class="mb-3">
				<label for="writer" class="form-label">글쓰니</label>
				<input type="text" class="form-control" id="writer" name="writer">
			</div>
			<div class="mb-3">
				<label for="content" class="form-label">글내용</label>
				<textarea class="form-control"  rows="10" cols="10" id="content" name="content"></textarea>
			</div>
			
			<button class="btn btn-primary">등록</button>
		</form>
	</div>
</body>
</html>
```

​	└ :page_facing_up: **detail.jsp**

```html
<title>게시글 상세보기</title>
<%@ include file="../common/bootstrap.jsp" %>
</head>
<body>
	<div class="container">
		<h2>글 상세보기</h2>
		<hr>
		<div class="card">
			<div class="card-body">
				<h5 class="card-title">${board.title}<span class="badge bg-danger">${board.viewCnt }</span></h5>
				<div class="d-flex justify-content-between">
					<div class="card-subtitle">${board.writer}</div>
					<div class="card-subtitle">${board.regDate}</div>
				</div>
				<p class="card-text">${board.content}</p>
				<div>
					<a href="delete?id=${board.id}" class="btn btn-info">삭제</a> 
					<a href="updateform?id=${board.id}" class="btn btn-success">수정</a> 
					<a href="list" class="btn btn-warning">목록</a> 
				</div>
			</div>
		
		</div>
	</div>
</body>
```

​	└ :page_facing_up: **update.jsp**

```html
<title>게시글 수정</title>
<%@ include file="../common/bootstrap.jsp" %>
</head>
<body>
	<div class="container">
		<h2>글수정</h2>
		<form action="update" method="POST">
			<input type="hidden" name="id" value="${board.id}">
			<div class="mb-3">
				<label for="title" class="form-label">글제목</label>
				<input type="text" class="form-control" id="title" name="title" value="${board.title}">
			</div>
			<div class="mb-3">
				<label for="writer" class="form-label">글쓰니</label>
				<input type="text" class="form-control" id="writer" name="writer" readonly value="${board.writer}">
			</div>
			<div class="mb-3">
				<label for="content" class="form-label">글내용</label>
				<textarea class="form-control"  rows="10" cols="10" id="content" name="content">${board.content}</textarea>
			</div>
			<button class="btn btn-primary">수정</button>
		</form>
	</div>
</body>
```


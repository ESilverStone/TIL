# :seedling: Spring CRUD

### 1. MySql(DB) 연결하기

:page_facing_up: **root-context.xml**

```xml
<!-- Root Context: defines shared resources visible to all other web components -->
<!-- 마이바티스에 접속하기 위해 연결 정보 설정-->
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
	<property name="url" value="jdbc:mysql://localhost:3306/ssafy_board?serverTimezone=UTC"/>
	<property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
	<property name="username" value="root"/>
	<property name="password" value="ssafy"/>	
</bean>
		
<!-- 마이바티스 사용하기 위한 sqlSessionFactory를 등록한다. -->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="dataSource" ref="dataSource"/>
	<property name="mapperLocations" value="classpath*:mappers/*.xml"/>
	<property name="typeAliasesPackage" value="com.ssafy.board.model.dto"/>
</bean>

<mybatis-spring:scan base-package="com.ssafy.board.model.dao"/>
	
<context:component-scan base-package="com.ssafy.board.model.service"/>
```

* 추가 설정

:page_facing_up: **servlet-context.xml**

```xml
<context:component-scan base-package="com.ssafy.board.controller" />
```



### 2. dto,dao 작성

:page_facing_up: **Board.java**

```java
public class Board {
	private int id;
	private String title;
	private String writer;
	private String content;
	private String regDate;
	private int viewCnt;
	
	
	public Board() {}
	
	public Board(String title, String writer, String content) {
		this.title = title;
		this.writer = writer;
		this.content = content;
	}
	
	
	public Board(int id, String title, String writer, String content, String regDate, int viewCnt) {
		this.id = id;
		this.title = title;
		this.writer = writer;
		this.content = content;
		this.regDate = regDate;
		this.viewCnt = viewCnt;
	}
	
	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getWriter() {
		return writer;
	}

	public void setWriter(String writer) {
		this.writer = writer;
	}

	public String getContent() {
		return content;
	}

	public void setContent(String content) {
		this.content = content;
	}

	public String getRegDate() {
		return regDate;
	}

	public void setRegDate(String regDate) {
		this.regDate = regDate;
	}

	public int getViewCnt() {
		return viewCnt;
	}

	public void setViewCnt(int viewCnt) {
		this.viewCnt = viewCnt;
	}

	@Override
	public String toString() {
		return "Board [id=" + id + ", title=" + title + ", writer=" + writer + ", content=" + content + ", regDate="
				+ regDate + ", viewCnt=" + viewCnt + "]";
	}
	
}
```

:page_facing_up: **BoardDao.java**

```java
public interface BoardDao {
	
	// 전체 게시글 가져오기
	public List<Board> selectAll();
	
	// id에 해당하는 게시글 하나 가져오기
	public Board selectOne(int id);
	
	// 게시글 등록
	public void insertBoard(Board board);
	
	// 게시글 수정
	public void updateBoard(Board board);

	// 게시글 삭제
	public void deleteBoard(int id);
	
	// 조회수 증가
	public void updateViewCnt(int id);	
	
}
```





### 3. Mapper 작성

:page_facing_up: **boardMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ssafy.board.model.dao.BoardDao">
	<resultMap type="Board" id="boardMap">
		<result column="id" property="id" />
		<result column="writer" property="writer" />
		<result column="title" property="title" />
		<result column="content" property="content" />
		<result column="view_cnt" property="viewCnt" />
		<result column="reg_date" property="regDate" />
	</resultMap>

	<!-- 전체글 조회  -->
	<select id="selectAll" resultType="Board">
		SELECT id, content, writer, title, view_cnt as viewCnt, date_format(reg_date, '%y-%m-%d %H:%i:%s') as regDate
		FROM board;	
	</select>
	
	<!-- 상세글 조회 -->
	<select id="selectOne" parameterType="int" resultMap="boardMap">
		SELECT id, content, writer, title, view_cnt as viewCnt, date_format(reg_date, '%y-%m-%d %H:%i:%s') as regDate
		FROM board
		WHERE id = #{id};	 
	</select>

	<!-- 게시글 등록 -->
	<insert id="insertBoard" parameterType="Board">
		INSERT INTO board (title, writer, content)
		VALUES (#{title}, #{writer}, #{content})
	</insert>
	
	<!-- 게시글 수정 -->
	<update id="updateBoard" parameterType="Board">
		UPDATE board 
		SET title = #{title}, content = #{content}, reg_date = now()
		WHERE id = #{id}	
	</update>
	
	<!-- 게시글 삭제 -->
	<delete id="deleteBoard" parameterType="int">
		DELETE FROM board
		WHERE id = #{id}	
	</delete>

	<!-- 조회수 증가  -->
	<update id="updateViewCnt" parameterType="int">
		UPDATE board
		SET view_cnt = view_cnt+1
		WHERE id = #{id}
	</update>

</mapper>
```



### 4. Service 작성

:page_facing_up: **BoardService.java**

```java
public interface BoardService {

	// 전체
	public List<Board> getBoardList();
	
	// 하나
	public Board readBoard(int id);
	
	// 작성
	public void writerBoard(Board board);
	
	// 수정
	public void modifyBoard(Board board);
	
	// 삭제
	public void removeBoard(int id);	
}
```

:page_facing_up: **BoardServiceImpl.java**

```java
@Service
public class BoardServiceImpl implements BoardService{

	private BoardDao boardDao;
	
	@Autowired
	public void setBoardDao(BoardDao boardDao) {
		this.boardDao = boardDao;
	}
	
	@Override
	public List<Board> getBoardList() {
		return boardDao.selectAll();
	}

	@Override
	public Board readBoard(int id) {
		boardDao.updateViewCnt(id);
		return boardDao.selectOne(id);
	}

	@Override
	public void writerBoard(Board board) {
		boardDao.insertBoard(board);
	}

	@Override
	public void modifyBoard(Board board) {
		boardDao.updateBoard(board);
	}

	@Override
	public void removeBoard(int id) {
		boardDao.deleteBoard(id);
	}
}
```



### 5. Controller

:page_facing_up: **BoardController.java**

```java
package com.ssafy.board.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;

import com.ssafy.board.model.dto.Board;
import com.ssafy.board.model.service.BoardService;


@Controller
public class BoardController {
	
	@Autowired
	private BoardService boardService;
	
	//root 설정
	@GetMapping("/")
	public String showIndex() {
		return "redirect:list";
	}
	
	
	// detail.jsp > "목록"
	@GetMapping("list")
	public String list(Model model) {
		List<Board> list = boardService.getBoardList();
		model.addAttribute("list", list);
		return "list";
	}
	
	// list.jsp > "글 등록"
	@GetMapping("writeform")
	public String writeform() {
		return "writeform";
	}
	
	// writeform.jsp > "등록"
	@PostMapping("write")
	public String write(Board board) {
		boardService.writerBoard(board);
		return "redirect:list";
	}
	
	// list.jsp > 글 제목 클릭
	@GetMapping("detail")
	public String detail(Model model, int id) {
		Board board = boardService.readBoard(id);
		model.addAttribute("board", board);
		return "detail";
	}
	
	// detail.jsp > "삭제"
	@GetMapping("delete")
	public String delete(int id) {
		boardService.removeBoard(id);
		return "redirect:list";
	}
	
	// detail.jsp > "수정"
	@GetMapping("updateform")
	public String updateform(Model model, int id) {
		Board board = boardService.readBoard(id);
		model.addAttribute("board", board);
		return "updateform";
	}
	
	// updateform.jsp > "수정"
	@PostMapping("update")
	public String update(Board board) {
		boardService.modifyBoard(board);
		return "redirect:detail?id="+board.getId();		
	}	
}
```



### 6. views

:page_facing_up: **list.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
	<meta charset="UTF-8">
	<title>게시판 목록</title>
</head>
<body>
	<h2>게시판 목록</h2>
	<hr>
	<table>
		<tr>
			<th>번호</th>
			<th>제목</th>
			<th>글쓴이</th>
			<th>조회수</th>
			<th>등록날짜</th>
		</tr>
		<c:forEach items="${list}" var="board">
		<tr>
			<th>${board.id}</th>
			<th><a href="detail?id=${board.id}">${board.title}</a></th>
			<th>${board.writer}</th>
			<th>${board.viewCnt}</th>
			<th>${board.regDate}</th>
		</tr>
		</c:forEach>	
	</table>
	
	<a href="writeform">글등록</a>
</body>
</html>
```

:page_facing_up: **writeform.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
	<meta charset="UTF-8">
	<title>게시글 등록</title>
</head>
<body>
	<h2>글 작성</h2>
	<form action="write" method="POST">
		<label for="title">글 제목</label>
		<input type="text" id="title" name="title">
		
		<label for="writer">글쓴이</label>
		<input type="text" id="writer" name="writer">
		
		<label for="content">글내용</label>
		<textarea rows="10" cols="10" id="content" name="content"></textarea>
		
		<button>등록</button>
	</form>
</body>
</html>
```

:page_facing_up: **detail.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
<meta charset="UTF-8">
	<title>게시글 상세보기</title>
</head>
<body>
	<h2>글 상세보기</h2>
	<h5>${board.title}<span>${board.viewCnt}</span></h5>
	<div>${board.writer}</div>
	<div>${board.regDate}</div>
	
	<p>${board.content}</p>
	
	<a href="delete?id=${board.id }">삭제</a>
	<a href="updateform?id=${board.id }">수정</a>
	<a href="list">목록</a>
</body>
</html>
```

:page_facing_up: **updateform.jsp**

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<html>
<head>
	<meta charset="UTF-8">
	<title>게시글 수정</title>
</head>
<body>
	<h2>글 수정</h2>
	<form action="update" method="POST">
		<label for="title">글제목</label>
		<input type="text" id="title" name="title" value="${board.title }">
	
		<label for="writer">글쓰니</label>
		<input type="text" id="writer" name="writer" readonly value="${board.writer }">
		
		<label for="title">글내용</label>
		<textarea rows="10" cols="10" id="content" name="content">${board.content}</textarea>
	
		<button>수정</button>
	</form>
</body>
</html>
```


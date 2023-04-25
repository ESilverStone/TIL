# :seedling: Spring #8

### 동적SQL & Java Config

**동적 SQL**

* Runtime 시점에서 사용자의 입력 값에 따라 동적으로 SQL을 생성하여 실행하는 방식
* JDBC나 다른 Framework 사용 시 어려움을 느낄 수 있음
* MyBatis는 이를 편리하게 사용할 수 있게 도움을 줌
* JSTL이나 XML 기반의 텍스트 프로세서를 사용해본 사람에게는 친숙할 것이다. 



**MyBatis 동적 SQL 종류**

* if
* choose (when, otherwise)
* trim (where, set)
* foreach



**동적 SQL 기능 실습**

:open_file_folder: **webapp.WEB-INF**

└ :open_file_folder: **views.board**

​	└ :page_facing_up: **list.jsp**

```html
<form action="search" class="row">
	<div class="col-2">
		<label>검색 기준 :</label>
		<select name="key" class="form-select">
			<option value="none">없음</option>
			<option value="writer">글쓰니</option>
			<option value="title">제목</option>
			<option value="content">내용</option>
		</select>
	</div>
	<div class="col-5">
		<label>검색 내용 :</label>
		<input type="text" name="word" class="form-control">
	</div>
	<div class="col-2">
		<label>정렬 기준 :</label>
		<select name="orderBy" class="form-select">
			<option value="none">없음</option>
			<option value="writer">글쓰니</option>
			<option value="title">제목</option>
			<option value="view_cnt">조회수</option>
		</select>
	</div>
	<div class="col-2">
	<label>정렬 방향 :</label>
		<select name="orderByDir" class="form-select">
			<option value="asc">오름차순</option>
			<option value="desc">내림차순</option>
		</select>
	</div>
	<div class="col-1">
		<input type="submit" value="검색" class="btn btn-primary">
	</div>
</form>
```

:package: **com.ssafy.board.controller**

└ :page_facing_up: **BoardController.java**

```java
	//	검색기능 작성
	@GetMapping("search")
	// public String search(Model model, String key, String word, String ..)
	// SearchCondition > dto로 처리한다.
	public String search(Model model, SearchCondition condition ) {
		
		model.addAttribute("list", boardService.search(condition));
		return "/board/list";
	}
```

:package: **com.ssafy.board.model.dto**

└ :page_facing_up: **SearchCondition.java**

```java
package com.ssafy.board.model.dto;

public class SearchCondition {
	private String key;
	private String word;
	private String orderBy;
	private String orderByDir; //JSP 만든 name과 이름을 동일시 해야 알잘로 넣어주더라...
	
	public SearchCondition() { //기본생성자는 습관처럼 만들자.
	}
	
	public String getKey() {
		return key;
	}
	public void setKey(String key) {
		this.key = key;
	}
	public String getWord() {
		return word;
	}
	public void setWord(String word) {
		this.word = word;
	}
	public String getOrderBy() {
		return orderBy;
	}
	public void setOrderBy(String orderBy) {
		this.orderBy = orderBy;
	}
	public String getOrderByDir() {
		return orderByDir;
	}
	public void setOrderByDir(String orderByDir) {
		this.orderByDir = orderByDir;
	}
}
```

:package: **com.ssafy.board.model.service**

└ :page_facing_up: **SearchCondition.java**

```java
//검색 버튼을 눌렀을 때 처리하기 위한 메서드
public List<Board> search(SearchCondition condition);
	
```

:package: **com.ssafy.board.model.dao**

└ :page_facing_up: **BoardDao.java**

```java
//검색기능
public List<Board> search(SearchCondition condition);
```

:open_file_folder: **mappers**

└ :page_facing_up: **boardMapper.xml**

```xml
<!-- 검색기능 -->
<select id="search" resultType="Board" parameterType="SearchCondition" >
	SELECT id, content, writer, title, view_cnt as viewCnt, date_format(reg_date, '%y-%m-%d %H:%i:%s') as regDate
	FROM board
	<!-- 어떤 기준을 가지고 검색을 할거냐!!! -->
	<if test="key != 'none'">
		WHERE ${key} LIKE concat('%', #{word}, '%')
	</if>
	<!-- 어떤 기준으로 어떤 방향으로 정렬할래? -->
	<if test="orderBy != 'none'">
		ORDER BY ${orderBy} ${orderByDir}
	</if>
</select>
```

:package: **com.ssafy.board.controller**

└ :page_facing_up: **BoardController.java**

```java
@PostMapping("write")
public String write(Board board) {
	System.out.println("등록전 : "+board);
	boardService.writeBoard(board);
	System.out.println("등록후 : "+board);
	//	return "redirect:list"; // 이렇게 하지말고...
	//게시글을 보고 싶다 상세보기로 바로 가고싶어...
	return "redirect:detail?id="+board.getId();
}
```





### **Spring TX**

* 데이터 무결성을 위해서 사용
* 스프링에서 제공하는 트랜잭션 기능을 활용할 수 있음
* jar or pom.xml을 이용하여 등록



 **setting**

:page_facing_up: **pom.xml**

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-tx</artifactId>
	<version>${org.springframework-version}</version>
</dependency>
```

:open_file_folder: **webapp.WEB-INF**

└ :open_file_folder: **spring.appServlet**

​	└ :page_facing_up: **root-context.xml**

```xml
<!-- 트랜잭션 매니저 등록한다. -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<constructor-arg ref="dataSource"/>
</bean>
	
<tx:annotation-driven transaction-manager="transactionManager"/>
```

:package: **com.ssafy.board.model.service**

└ :page_facing_up: **BoardServiceImpl.java**

```java
@Transactional
@Override
public void writeBoard(Board board) {
	System.out.println("게시글을 작성합니다.");
	boardDao.insertBoard(board);
}

@Transactional
@Override
public void removeBoard(int id) {
	System.out.println(id+"번의 글을 삭제합니다.");
	boardDao.deleteBoard(id);
}

@Transactional
@Override
public void modifyBoard(Board board) {
	System.out.println("게시글을 수정합니다.");
	boardDao.updateBoard(board);
}
```







### Java Config

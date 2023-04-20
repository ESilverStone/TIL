# :seedling: Spring #6

### MyBatis

**setting**

:open_file_folder: **lib**

└ :penguin: **mybatis-3.5.9.jar**

└ :penguin: **mysql-connector-java-8.0.21.jar**

:books:**Referenced Libraries (build path > libraries > ADD JARs) ** 

└ :penguin: **mybatis-3.5.9.jar**

└ :penguin: **mysql-connector-java-8.0.21.jar**

:open_file_folder: **resources**

└ :package: **config**

​	└ :page_facing_up: **mybatis-config.xml**

```html
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "https://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 여기서 가져와라잉 -->
	<properties resource="config/db.properties"/>

	<typeAliases>
		<typeAlias type="com.ssafy.board.model.dto.Board" alias="Board"/>
	</typeAliases>	
    
    <!-- 아래 출처에서 불러온 것. mapper경로는 수정필요-->
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${driver}" />
				<property name="url" value="${url}" />
				<property name="username" value="${username}" />
				<property name="password" value="${password}" />
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="mapper/boardMapper.xml" />
	</mappers>
</configuration>
```

* 출처 : `https://mybatis.org/mybatis-3/ko/getting-started.html` > 시작하기 > XML에서 SqlSesstionFactory 빌드하기

​	└ :page_facing_up: **db.properties**

```properties
url=jdbc:mysql://localhost:3306/ssafy_board?serverTimezone=UTC
driver=com.mysql.cj.jdbc.Driver
username=ssafy
password=ssafy
```

└ :package: **mapper**

​	└ :page_facing_up: **board.xml (DaoImpl의 최신방식)**

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
	<!-- 지금 게시글 날짜는 등록 날짜만 존재한다. 나중에 수정날짜도 같이 관리하게 된다면의 상황을보자ㅏ... -->
	<update id="updateBoard" parameterType="Board">
		UPDATE board
		SET title = #{title}, content=#{content}, reg_date = now()
		WHERE id=#{id}
	</update>	
	
</mapper>
```

:package: **com.ssafy.board.model.dao**

└ :page_facing_up: **BoardDao.java** 

```java
package com.ssafy.board.model.dao;

import java.util.List;

import com.ssafy.board.model.dto.Board;

public interface BoardDao {
	// 전체 게시글을 몽땅 들고 오쎄용
	public List<Board> selectAll();

	// ID에 해당하는 게시글 하나 가져오기
	public Board selectOne(int id);

	// 게시글 등록
	public void insertBoard(Board board);

	// 게시글 삭제
	public void deleteBoard(int id);

	// 게시글 수정
	public void updateBoard(Board board);

	// 조회수 증가
	public void updateViewCnt(int id);

}
```

:package: **com.ssafy.board.config**

└ :page_facing_up: **MyAppSqlConfig.java** 

```java
package com.ssafy.board.config;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class MyAppSqlConfig {
	private static SqlSession session;
	
	static {
		//MyBatis 설정파일을 가져오겠다.
		try {
			String resource = "config/mybatis-config.xml";
			Reader reader = Resources.getResourceAsReader(resource);
			SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
			//공장도 다 세웠다.
			
			//세션 팩토리르 이용해서 세션을 얻어오겠다.
			session = sqlSessionFactory.openSession(true); //true라는 옵션을 주면 자동으로 커밋이된다.
			System.out.println("세션 생성 성공");
		} catch (IOException e) {
			System.out.println("세션 생성 실패");
		}
	}
	
	public static SqlSession getSession() {
		return session;
	}
}
```

* 참고 : 시작하기 > XML에서 SqlSesstionFactory 빌드하기

:package: **com.ssafy.board.test**

└ :page_facing_up: **Test.java** 

```java
public class Test {
	public static void main(String[] args) {
		BoardDao dao = MyAppSqlConfig.getSession().getMapper(BoardDao.class);
	
        Board bb = dao.selectOne(3);
		bb.setTitle("대답잘하는법 공유해드림");
		bb.setContent("채팅 뿐만아니라 열정도 있어야함.");
		
		dao.updateBoard(bb);
		
		System.out.println(dao.selectAll().size());
		for (Board b : dao.selectAll()) {
			System.out.println(b);
		}
	}
}
```


# :seedling: Spring

### 의존성 주입

**생성자 이용**

```java
public class Programmer {
	// 캡슐화
	private Computer computer;
	
	// 객체생성 의존성을 제거하기 위해서 얘가 만드는게 아니라
	// 만들어진 컴퓨터를 넣어주겠다.
	// desktop laptop이 아닌 컴퓨터로 바꾸면 느슨한 결합을 하겠다. 
	// 생성자 주입
	public Programmer(Computer computer) {
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 작업중");
	}
}
```



**설정자 이용**

```java
public class Programmer {
	// 캡슐화
	private Computer computer;
	
	// setter(설정자)를 이용한 주입
	public void setComputer(Computer computer) {
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 작업중");
	}
}
```



**Factory 이용**

```java
public class ComputerFactory {
	// 이 컴퓨터 주세요
	public static Computer getComputer(String type) {
		
		if(type.equals("D")) {
			return new Desktop();
		} else if (type.equals("L")) {
			return new Laptop();
		}
		return null;
	}
}


public class Test {
	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		
		Programmer p = new Programmer();
		Computer computer = ComputerFactory.getComputer(sc.next());
		p.setComputer(computer);
		
		p.coding();
		
		// 같은 p에 반복
		computer = ComputerFactory.getComputer(sc.next());
		p.setComputer(computer);
		
		p.coding();
	}
}
```





### Spring Container Build

**Container**

* 스프링에서 핵심적인 역할을 하는 객체를 Bean이라고 한다.
* Container는 Bean의 인스턴스화 조립, 관리의 역할, 사용 소멸에 대한 처리를 담당한다.



스프링 설정 저보

* 애플리케이션 작성을 위해 생성할 Bean과 설정 정보, 의존성 등의 방법을 나타내는 정보
* 설정정보를 작성하는 방법은 XML 방식, Annotation 방식, Java 방식이 있다. 



Spring Container 빌드

* project 생성 후 > Maven Project로 변경
* pom.xml > Spring Context 의존성 추가

```html
<dependencies>
  	<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
	<dependency>
    	<groupId>org.springframework</groupId>
    	<artifactId>spring-context</artifactId>
    	<version>5.3.18</version>
	</dependency>
</dependencies>
```

* Source Folder 생성(resources)

* 빈(bean) 등록 (풀패키지 명 작성)

```html
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	<!-- 풀패키지명 -->
    <!-- scope를 정의해서 객체의 범위를 제어할 수 있다. 기본은 singleton -->
	<bean class="com.ssafy.di3.Desktop" id="desktop"></bean>
	<bean class="com.ssafy.di3.Programmer" id="programmer"></bean>
</beans>
```

* 스프링 컨테이너를 이용하여 객체 가져오기

```java
public class Test {
	public static void main(String[] args) {
		
		// 스프링 컨테이너 객체를 빌드
		ApplicationContext context 
            = new GenericXmlApplicationContext("applicationContext.xml");
		
		// 컨테이너로 부터 객체를 가져오겠다. 
		Programmer p = (Programmer) context.getBean("programmer");	
		Desktop desktop = context.getBean("desktop", Desktop.class);
		
		p.setComputer(desktop);
		p.coding();
		
		Desktop d2 = context.getBean("desktop", Desktop.class);
		Desktop d3 = context.getBean("desktop", Desktop.class);
		
		System.out.println(d2==d3);	// true
	}
}
```





### Spring DI

**XML**

몰?루



**Annotation**

* bean 생성 : **@Component**
* 생성되는 bean의 이름은 클래스의 첫 글자를 소문자로 바꾼 것이다. ex) Desktop > desktop
  * @Component(value = "bean-name") 으로 이름을 지정할 수 있다.
* **Component Scan**

```html
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
	
	<context:component-scan base-package="com.ssafy.di4"></context:component-scan>
</beans>
```

Namespaces > context를 체크

* 의존성을 주입할 대상에 @Autowired annotation 작성

```java
@Component(value = "p")
public class Programmer {
	// 캡슐화
    @Autowired
    @Qualifier("laptop")
	private Computer computer;
	 
	public Programmer() {}

	// 생성자 주입
	@Autowired
	public Programmer(@Qualifier("laptop") Computer computer) {
		this.computer = computer;
	}
	

	// setter(설정자)를 이용한 주입
	@Autowired
	public void setComputer(@Qualifier("laptop") Computer computer) {
		this.computer = computer;
	}
	
	public void coding() {
		System.out.println(computer.getInfo()+"으로 작업중");
	}
}
```

* spring 컨테이너 내에서 타입 매칭 시행 (컨테이너에 해당하는 타입의 bean이 있다면 매칭)



### 갓지피티님의 훈수쇼

**bean의 의미는?**

![image](https://user-images.githubusercontent.com/68459918/231105373-ac0b33e6-2712-470b-8218-5d6d31c4fa42.png)

**.class의 역할?**

![image](https://user-images.githubusercontent.com/68459918/231105566-e5e468b0-ec8a-4bc6-b4b5-7f15a7f831bb.png)

**의존성에 대해**

![image](https://user-images.githubusercontent.com/68459918/231105739-63b0fab1-513f-4506-a934-4a17ae9e68ea.png)

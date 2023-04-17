# :seedling: Spring #2

### AOP(Aspect Oriented Programming)

* OOP에서 모듈화의 핵심 단위는 클래스인 반면, AOP에서 모듈화의 단위는 Aspect
* Aspect는 여러 타입과 객체에 거쳐서 사용되는 기능(cross cutting, 트랜잭션 관리 등)의 모듈화
* Spring framework의 필수요소는 아니지만, AOP 프레임워크는 Spring IoC를 보완한다.



**AOP용어**

* **Aspect** : 여러 클래스에 공통적으로 구현되는 관심사의 모듈화
* **Join Point** : 메서드 실행이나 예외처리와 같은 프로그램 실행중의 특정 지점. Spring에서는 메서드 실행을 의미한다. 
* **Pointcut** : Join Point에 Aspect를 적용하기 위한 조건 서술. Aspect는 지정한 pointcut에 일치하는 모든 join point에서 실행된다. 
* **Advice** : 특정 조인포인트(Join Point)에서 Aspect에 의해서 취해진 행동. Around, Before, After 등의 Advice 타입이 존재
* **Target 객체** : 하나이상의 advice가 적용될 객체. Spring AOP는 Runtime Proxy를 사용하여 구현되므로 객체는 항상 Proxy 객체가 된다. 
* **AOP Proxy** : AOP를 구현하기 위해 AOP 프레임워크에 의해 생성된 객체, Spring Framework에서 AOP 프록시는 JDK dynamic proxy 또는 CGLIB proxy이다. 
* **Weaving** : Aspect를 다른 객체와 연결하여 Advice 객체를 생성. 런타임 또는 로딩 시 수행할 수 있지만 Spring AOP는 런타임에 위빙을 수행



**Spring AOP Proxy**

* 실제 기능이 구현된 target객체를 호출하면 target이 호출되는 것이 아니라 advice가 적용된 proxy 객체가 호출된다.
* Spring AOP는 기본값으로 표준 JDK dynamic proxy를 사용
* 인터페이스를 구현한 클래스가 아닌 경우 CGLIB 프록시를 사용

```html
<aop:aspectj-autoproxy proxy-target-class="true"></aop:aspectj-autoproxy>
```

```java
// PersonProxy
public class PersonProxy implements Person{
	
	private Person person;
	
	// 생성자 주입
	// 설정자 주입
	public void setPerson(Person person) {
		this.person = person;
	}
	
	@Override
	public void coding() {
		System.out.println("컴퓨터를 부팅한다.");      // 핵심관심사항 이전에 해야할 것
		
		try {
			person.coding();						// 핵심관심사항
			if(new Random().nextBoolean()) {
				throw new OuchException();
			}
			System.out.println("git에 push한다.");	  // 아무이상 없이 잘 마쳤을 때
			
		} catch (OuchException e) {
			System.out.println("반차를 낸다.");		// 문제가 발생했을 때
		} finally {
			System.out.println("보람?찬 하루를 마무리한다.");	// 모든게 끝이 났을 때				
		}
	}
}
```

```java
// Test
public class Test {
	public static void main(String[] args) {
		Programmer p = new Programmer();
		Ssafy s = new Ssafy();
		
//		p.coding();
//		s.coding();
		
		PersonProxy px = new PersonProxy();
		px.setPerson(s);
		
		px.coding();
	}
}
```





**Spring AOP**

* @AspectJ : 일반 Java 클래스를 Aspect로 선언하는 스타일, AspectJ프로젝트에 의해 소개되었음
* Spring AOP에서는 pointcut 구문 분석, 매핑을 위해서 AspectJ 라이브러리를 사용함
* 하지만, AOP runtime은 순수 Spring AOP이며, AspectJ에 대한 종속성은 없음



**Advice Type**

* **before** : target 메서드 호출 이전
* **after** : target 메서드 호출 이후, java exception 문장의 finally와 같이 동작
* **after returning** : target 메서드 정상 동작 후
* **after throwing** : target 메서드 에러 발생 후
* **around** : target 메서드의 실행 시기, 방법, 실행 여부를 결정





### AOP XML

```html
// ApplicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
	<aop:aspectj-autoproxy proxy-target-class="true"></aop:aspectj-autoproxy>
	
	
	<bean class="com.ssafy.aop.Programmer" id="programmer"/>
	<bean class="com.ssafy.aop.Ssafy" id="ssafy"/>
	<bean class="com.ssafy.aop.MyAspect" id="myAspect"/>
	
	<aop:config>
		<aop:pointcut expression="execution(public * com.ssafy.aop.*.coding())" id="mypt"/>
		<aop:aspect ref="myAspect">
			<!-- <aop:before method="before" pointcut-ref="mypt"/>
			<aop:after-returning method="afterReturning" pointcut-ref="mypt" returning="num"/>
			<aop:after-throwing method="afterThrowing" pointcut-ref="mypt" throwing="th"/>
			<aop:after method="after" pointcut-ref="mypt"/> -->
			<aop:around method="around" pointcut-ref="mypt"/>
		</aop:aspect>
	</aop:config>
</beans>
```

```java
// MyAspect
public class MyAspect {
	public void before() {
		System.out.println("컴퓨터를 부팅한다.");
	}
	
	public void afterReturning(int num) {
		System.out.println("Git Push 한다. : " + num + "개의 코드를");
	}
	
	public void afterThrowing(Throwable th) {
		System.out.println("반차를 낸다.");
		if(th instanceof OuchException) {
			((OuchException)th).handleException();
		}
	}
	
	public void after() {
		System.out.println("보람찬 하루를 마무리 한다.");
	}
	//////////////////////////////////
	// around로 한 방에 처리하기
	
	public int around(ProceedingJoinPoint pjt) {
		int num = 0;
		
		// before
		this.before();
		
		try {
			num = (int) pjt.proceed();	// 핵심관심사항
		} catch (Throwable e) {
			this.afterThrowing(e);
		} finally {
			this.after();
		}
		
		return num;		
	}
}
```





### AOP Annotation

```java
// MyAspect

@Component
@Aspect
public class MyAspect {
	
	@Pointcut("execution(public * com.ssafy.aop.*.coding())")
	public void mypt() {
		
	}
	
//	@Before("mypt()")
	public void before() {
		System.out.println("컴퓨터를 부팅한다.");
	}
	
//	@AfterReturning(value="mypt()", returning = "num")
	public void afterReturning(int num) {
		System.out.println("Git Push 한다. : " + num + "개의 코드를");
	}
	
//	@AfterThrowing(value="mypt()", throwing = "th")
	public void afterThrowing(Throwable th) {
		System.out.println("반차를 낸다.");
		if(th instanceof OuchException) {
			((OuchException)th).handleException();
		}
	}
	
//	@After("mypt()")
	public void after() {
		System.out.println("보람찬 하루를 마무리 한다.");
	}
	//////////////////////////////////
	// around로 한 방에 처리하기
	@Around("mypt()")
	public int around(ProceedingJoinPoint pjt) {
		int num = 0;
		
		// before
		this.before();
		
		try {
			num = (int) pjt.proceed();	// 핵심관심사항
		} catch (Throwable e) {
			this.afterThrowing(e);
		} finally {
			this.after();
		}
		
		return num;		
	}
}
```

```java
// OuchException
public class OuchException extends Exception {
	public void handleException() {
		System.out.println("병원이나 가자~");
	}	
}
```

```java
// Person
public interface Person {
	public int coding() throws OuchException;	// 느슨한 결합
}
```

```java
// Programmer
public class Programmer implements Person {
	// 필드는 필요없다.
	
	// 싸피인의 일상
	@Override
	public int coding() throws OuchException {
		System.out.println("열시미 코드를 와다다다다" );	// 핵심관심사항
		
		if(new Random().nextBoolean())
			throw new OuchException();
		
		return (int) (Math.random() * 100)+1;
	}
}
```

```java
// Ssafy
public class Ssafy implements Person{
	// 필드는 필요없다.
	
	// 프로그래머의 일상
	@Override
	public int coding() throws OuchException {
		System.out.println("이제 슬슬 공부해볼까(10트)" );	// 핵심관심사항
		
		if(new Random().nextBoolean())
			throw new OuchException();
		
		return (int) (Math.random() * 100)+1;
	}
}
```

```java
// Test
public class Test {
	public static void main(String[] args) {
		ApplicationContext context = new GenericXmlApplicationContext("applicationContext.xml");
		
		Person p = context.getBean("ssafy", Person.class);
		try {
			p.coding();
		} catch (OuchException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	}
}
```

```html
// ApplicationContext.xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
	<aop:aspectj-autoproxy proxy-target-class="true"></aop:aspectj-autoproxy>
	
	<context:component-scan base-package="com.ssafy.aop"></context:component-scan>
</beans>
```


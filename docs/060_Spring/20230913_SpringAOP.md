---
layout: default
title: SpringAOP
parent: Spring
nav_order: 6
---

# Spring

## AOP (Aspect Oriented Programming)

- OOP에서 모듈화의 핵심 단위는 클래스인 반면, AOP에서 모듈화의 단위는 Aspect
- Aspect는 여러 타입과 객체에 거쳐서 사용되는 기능 (Cross Cutting, 트랜잭션 관리 등)의 모듈화
- Spring IoC를 보완

### 프록시 패턴 (Proxy Pattern)

- 대리자, 대행자, 대변인
- 어떤 객체를 사용할 때, 객체를 직접 참조하는 것이 아닌 해당 객체를 대항하는 객체를 통해 대상 객체에 접근하는 방식
- 해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정할 수 있다.

### 프록시 패턴 적용 예시

```java
// 동일 패키지

public interface Person {
	public void coding();
}
```

```java
// 동일 패키지

public class Programmer implements Person {
	// 필드 생략

	public void coding() {
		System.out.println("열심히 코드를 작성한다."); // 핵심관심사항
	}
}
```

```java
// 동일 패키지

public class Student implements Person {
	// 필드 생략

	public void coding() {
		System.out.println("열심히 공부를 한다."); // 핵심관리사항
	}
}
```

```java
// 동일 패키지

public class PersonProxy implements Person {

	private Person person;

	//설정자 주입
	public void setPerson(Person person) {
		this.person = person;
	}

	@Override
	public void coding() {
		System.out.println("컴퓨터를 부팅한다."); // 이전에 해야될 것
		try {
			person.coding(); // 핵심관심사항
			if (new Random().nextBoolean())
				throw new Exception();
			System.out.println("Git에 Push한다."); // 이상없이 마무리 됐을 때
		} catch (Exception e) {
			System.out.println("반차를 낸다."); // 예외발생
		} finally {
			System.out.println("하루를 마무리한다."); // 모든게 마무리가 됐을 때
		}
	}
}
```

### AOP 용어

| 용어        | 내용                                                                                                                               |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Aspect      | 여러 클래스에 공통적으로 구현되는 관심사(Concern)의 모듈화                                                                         |
| Join Point  | 메서드 실행이나 예외 처리와 같은 프로그램 실행 중의 특정 지점<br>Spring에서는 메서드 실행을 의미한다.                              |
| PointCut    | Join Point에 Aspect를 적용하기 위한 조건 서술<br>Aspect는 지정한 pointcut에 일치하는 모든 join point에서 실행된다.                 |
| Advice      | 특정 조인 포인트(Join Point)에서 Aspect에 의해 취해진 행동<br>Around, Before, After 등의 Advice 타입이 존재                        |
| Target 객체 | 하나 이상의 advice가 적용될 객체<br>Spring AOP는 Runtime Proxy를 사용하여 구현되므로 객체는 항상 Proxy 객체가 된다.                |
| AOP Proxy   | AOP를 구현하기 위해 AOP 프레임워크에 의해 생성된 객체<br>Spring Framework에서 AOP 프록시는 JDK dynamic proxy 또는 CGLIB proxy이다. |
| Weaving     | Aspect를 다른 객체와 연결하여 Advice 객체를 생성<br>런타임 또는 로딩 시 수행할 수 있지만 Spring AOP는 런타임에 위빙을 수행         |

### Spring AOP Proxy

- 실제 기능이 구현된 target 객체를 호출하면, target이 호출되는 것이 아니라 advice가 적용된 Proxy 객체가 호출된다.
- Spring AOP는 기본값으로 표준 JDK dynamic proxy를 사용한다.
- 인터페이스를 구현한 클래스가 아닌 경우 CGLIB 프록시를 사용한다.

### Spring AOP

- @AspectJ : 일반 Java 클래스를 Aspect로 선언하는 스타일, AspectJ 프로젝트에 의해 소개됐다.
- Spring AOP 에서는 pointcut 구문 분석, 매핑을 위해서 AspectJ 라이브러리를 사용한다.
- 하지만 AOP runtime은 순수 Spring AOP 이며, AspectJ에 대한 종속성은 없다.

### Spring AOP 시작하기 - xml

- Aspect 선언하기 - xml 방식
- Aspect는 핵심 관심사항 (Core concern)
- @Aspect annotation 또는 xml bean 등록하기

```java
public class Programmer {
	// 필드 생략

	public void coding() {
		System.out.println("열심히 코드를 작성한다."); // 핵심관심사항
	}
}
```

```java
<bean class="com.backend.aop.Programmer" id="programmer"></bean>
```

- Pointcut 선언
- 포인트 컷은 조인포인트를 사용할지 결정한다. Spring AOP는 메서드 실행만 지원한다.
- 포인트 컷 선언은 두 내용을 포함한다.
  - 조인포인트에 대한 표현식
  - 포인트 컷의 이름

```java
<aop:config>
	<aop:aspect ref="myAspect">
		<aop:pointcut expression="excution(public void com.backend.aop.*.coding())" id="mypt"/> // com.backend.aop 패키지의 모든 클래스의 coding 메서드를 선택, execution : 해당 메서드가 실행됐을 경우
		// advice type에 따른 메서드 지정
		<aop:before method="before" pointcut-ref="mypt"/> // before : target 메서드 실행 전
		<aop:after-returning method="afterReturning" pointcut-ref="mypt"/>
		<aop:after-throwing method="afterThrowing" pointcut-ref="mypt" throwing="th"/> // 예외 상황 처리
		<aop:after method="after" pointcut-ref="mypt"/> // target 메서드 실행 후
	</aop:aspect>
</aop:config>
```

### Spring AOP 시작하기 - Annotation

1. @AspectJ 활성화 - \<aop:aspectj-autoproxy/>
2. Bean 등록 및 @Aspect
3. Pointcut 설정 (@Pointcut)
4. advice type 지정

### Advice Type

- before - target 메서드 호출 이전
- after - target 메서드 호출 이후, java exception 문장의 finally와 같이 동작
- after returning - target 메서드 정상 동작 후
- after throwing - target 메서드 에러 발생 후
- around - target 메서드의 실행 시기, 방법, 실행 여부를 결정

### Advice Type - after returning, after throwing

- after returning, after throwing은 각각 실행 결과와 에러를 인자로 받는다.

```java
public void method1(String message) {
	// 매개변수 타입은 target의 반환 타입과 같아야 한다.
}
public void method2(Throwable err) {
	// 에러가 발생하면 수행되는 메서드이므로 타입은 Throwable
}
```

### Advice Type - around

- around 메서드는 target 메서드의 실행을 직접 수행한다.
- advice 내부에서 target에 접근하기 위해서 메서드의 첫번째 인자로 ProceedingJoinPoint를 받아온다.
- target 메서드의 실행은 proceed() 메서드를 호출한다.
- 그 외 나머지 target 메서드 이전, 이후에 실행할 코드들을 작성한다.

```java
@Around("mypt()")
public void method(ProceedingJoinPoint jp) {
	method1();
	try {
		Object message = jp.proceed();
		method3((String) message);
	} catch (Throwable e) {
		method4(e);
	} finally {
		method2();
	}
}
```

### Pointcut Expression 패턴 예시

| 패턴                                              | 설명                                                                                |
| ------------------------------------------------- | ----------------------------------------------------------------------------------- |
| execution(public \* \*(..))                       | public 메서드 실행                                                                  |
| execution(_ set_(..))                             | 메서드 이름이 set으로 시작하는 메서드 실행                                          |
| execution(_ com.xyz.service.AccountService._(..)) | AccountService Interface에 정의된 모든 메서드                                       |
| execution(_ com.xyz.service._.\*(..))             | service package에 선언된 모든 메서드                                                |
| execution(_ com.xyz.service.._.\*(..))            | service package 또는 그 하위 패키지에 선언된 모든 메서드 실행                       |
| within(com.xyz.service.\*)                        | service package 또는 그 하위 패키지의 모든 join point(Spring AOP에서는 메서드 실행) |

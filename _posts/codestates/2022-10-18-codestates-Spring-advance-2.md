---
title : "코드스테이츠 학습일지 - 2022.10.17 ~ 2022.10.18"

categories:
  - CodeStates
excerpt: "Spring Framework - AOP"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-17
last_modified_at: 2022-10-17
---

## **Today I Learned**
📚오늘의 체크리스트
- [x] 스터디 미션
- [x] 금일 학습 분량
- [x] 금일 학습 이해

---

## Spring Framework
### AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)
- 기존과 다른 프로그램 구조 사고 방식을 제공함으로써 객체 지향 프로그래밍의 부족한 부분을 보완하는 것
  - 비즈니스 클래스에 핵심 기능과 부가 기능이 공존하는 문제
- 애스펙트를 사용하여 핵심 기능과 부가 기능을 분리하고 분리된 부가 기능을 어디에 적용할지 선택하는 기능
- 기존에 사용하던 OOP의 부족한 부분인 관심사 분리에 대한 부분을 보완


#### AOP 용어
> 핵심 기능(Core Concerns)
- 객체가 제공하는 고유의 기능(업무 로직 등을 포함)
  
> 부가 기능(Cross-Cutting Concerns)
- 핵심 기능을 보조하기 위해 제공되는 기능
- 로그 추적 로직, 보안, 트랜잭션 기능 등
- 단독으로 사용되지 않고 핵심 기능과 함께 사용
  
> 애스팩트(Aspect)
- 여러 객체에 공동으로 적용되는 기능(공통 기능)
- 어드바이스 + 포인트컷을 모듈화하여 애플리케이션에 포함되는 횡단 기능
- 여러 어드바이스와 포인트컷이 함께 존재
 
> 조인 포인트(Join Point)
- 메서드 실행 전(S) 또는 실행 후(E)의 포인트를 어드바이스가 적용될 조인포인트라고 부름
- 클래스 초기화, 객체 인스턴스화, 메소드 호출, 필드 접근, 예외 발생과 같은 애플리케이션 실행 흐름에서의 특정 포인트를 의미
- 애플리케이션에 새로운 동작을 추가하기 위해 조인포인트에 관심 코드(Aspect Code) 추가 가능
- 횡단 관심은 조인포인트 전/후에 AOP에 의해 자동으로 추가
- 추상적인 개념으로 AOP를 적용할 수 있는 모든 지점
- 스프링 AOP는 프록시 방식을 사용하므로 조인 포인트는 항상 메소드 실행 지점으로 제한
- 어드바이스 적용이 필요한 곳은 애플리케이션 내에 메서드를 가짐

> 어드바이스(Advice)
- 조인포인트에서 수행되는 코드
- Aspect를 언제 핵심 코드에 적용할 지를 정의
- 시스템 전체 에스펙트에 API 호출을 제공
- 메소드를 호출하기 전에 각 상세정보와 모든 메소드를 로그로 남기기 위해 조인포인트 S를 선택
- 부가 기능에 해당

> 포인트컷(Pointcut)
- 조인포인트 중에서 어드바이스가 적용될 위치를 선별하는 기능
- AspectJ 표현식을 사용해서 지정
- 프록시를 사용하는 스프링 AOP는 메서드 실행 지점만 포인트컷으로 선별 가능

> 위빙(Weaving)
- 포인트컷으로 결정한 타켓의 조인 포인트에 어드바이스를 적용하는 것
  - 어드바이스를 핵심 코드에 적용하는 것을 의미
- 핵심 기능 코드에 영향을 주지 않고 부가 기능을 추가할 수 있음
- AOP 적용을 위해 애스펙트 객체에 연결한 상태
  - 컴파일 타임(AspectJ Compoiler)
  - 로드 타임
  - 런타임(스프링 AOP는 런타임, 프록시 방식)

> AOP 프록시(proxy)
- AOP기능을 구현하기 위해 만든 프록시 객체
- 스프링에서 AOP 프록시는 JDK 동적 프록시 또는 CGLIB 프록시

>  타겟(Target)
- 핵심 기능을 담고 있는 모듈로 부가 기능을 부여할 대상
- 어드바이스를 받는 객체이며 포인트컷으로 결정

> 어드바이저(Advisor)
- 하나의 어드바이스와 하나의 포인트컷으로 구성
- 스프링 AOP에서만 사용되는 특별한 용어

#### 어드바이스(Advice)
##### Advice 순서
> - 기본적으로 순서를 보장하지 않음
- 순서를 지정하고 싶으면 @Aspect 적용 단위로 org.springframework.core.annotation.@Order 애너테이션을 적용
  - 어드바이스 단위가 아닌 클래스 단위로 적용 가능
  - 하나의 애스펙트에 여러 어드바이스가 존재하면 순서를 보장받을 수 없음
- 애스펙트를 별도의 클래스로 분리해야 함

##### Advice 종류
> Before
- 조인포인트 실행 이전에 실행
- 타켓 메서드가 실행되지 전에 처리해야할 필요가 있는 부가 기능의 호출 전에 공통 기능을 실행
- Before Advice를 구현한 메서드는 일반적으로 리턴타입이 void
  - 리턴 값을 갖더라도 실제 Advice 적용 과정에 아무 영향이 없음
- 메서드에서 예외를 발생시킬 경우 대상 객체의 메서드가 호출되지 않게 됨

> After returning
- 조인포인트가 정상 완료 후 실행
- 메서드가 예외 없이 실행된 이후에 공통 기능을 실행

> After throwing
- 메서드가 예외를 던지는 경우에 실행
- 메서드를 실행하는 도중 예외가 발생한 경우 공통 기능을 실행

> After (finally)
- 조인포인트의 동작(정상 또는 예외)과 상관없이 실행
  - 예외 동작의 finally와 유사
- 메서드 실행 후 공통 기능을 실행
- 일반적으로 리소스를 해제하는데 사용

> Around
- 메서드 호출 전후에 수행하며 가장 강력한 어드바이스
  - 조인포인트 실행 여부 선택
  - 전달 값 변환
  - 반환 값 변환
  - 예외 변환
  - try ~ catch ~ finally 가 들어가는 구문 처리 가능
- 메서드 실행 전, 후, 예외 발생 시점에 공통 기능을 실행
- proceed()를 통해 대상을 실행하며 여러번 실행 가능
- 타겟 등 고려해야할 사항이 있을 때 정상적으로 작동이 되지 않는 경우 존재

#### Pointcut 표현식
- 포인트컷은 관심 조인 포인트를 결정하므로 어드바이스가 실행되는 시기를 제어 가능
- AspectJ는 포인트컷을 편리하게 표현하기 위한 특별한 표현식을 제공
  - Ex) @Pointcut("execution(* hello.aop.order..*(..))")
- 포인트컷 표현식은 &&, \|\|, ! 등을 사용하여 결합 가능
- 포인트컷 표현식은 포인트컷 지시자(Pointcut Designator, PCD)로 시작

##### 포인트컷 지시자 종류
> execution
- 메서드 실행 조인포인트를 매칭
- 스프릉 AOP에서 가장 많이 사용하며 기능도 복잡함

> within
- 특정 타입 내의 조인포인트를 매칭

> args
- 인자가 주어진 타입의 인스턴스인 조인포인트

> this
- 스프링 빈 객체(스프링 AOP 프록시)를 대상으로 하는 조인포인트

> target
- Target 객체(스프링 AOP 프록시가 가르키는 실제 대상)를 대상으로 하는 조인포인트

> @target
- 실행 객체의 클래스에 주어진 타입의 애너테이션이 있는 조인포인트

> @within
- 주어진 애너테이션이 있는 타입 내 조인포인트

> @Annotation
- 메서드가 주어인 애너테이션을 가지고 있는 조인포인트를 매칭

> @args
- 전달된 실제 인수의 런타임 타입이 주어진 타입의 애너테이션을 갖는 조인포인트

> bean
- 스프링 전용 포인트컷 지시자미여 빈의 이름으로 포인트컷을 지정

##### 일반적인 포인트컷 표현식들
> 모든 공개 메서드 실행
- execution(public * *(..))

> set 다음 이름으로 시작하는 모든 메서드 실행
- execution(* set*(..))

> AccountService 인터페이스에 의해 정의된 모든 메소드의 실행
- execution(* com.xyz.service.AccountService.*(..))

> Service 패키지에 의해 정의된 메서드 실행
- execution(* com.xyz.service.\*.\*(..))

> Service 패키지 또는 하위 패키지 중 하나에 정의된 메서드 실행
- execution(* com.xyz.service..\*.\*(..))

> Service 패키지 내의 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- within(com.xyz.service.*)

> Service 패키지 또는 하위 패키지 중 하나 내의 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- within(com.xyz.service..*)

> AccountService 프록시가 인터페이스를 구현하는 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- this(com.xyz.service.AccountService)

> AccountService 대상 객체가 인터페이스를 구현하는 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- target(com.xyz.service.AccountService)

> 단일 매개변수를 사용하고 런타임에 전달된 인수가 Serializable과 같은 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- args(java.io.Serializable)

> 대상 객체에 @Transactional 애너테이션이 있는 모든 조인포인트(스프링 AOP에서만 메서드 실행)
- @target(org.springframework.transaction.annotation.Transactional)

> 실행 메서드에 @Transactional 애너테이션이 있는 조인 포인트 (Spring AOP에서만 메서드 실행)
- @annotation(org.springframework.transaction.annotation.Transactional)


> 단일 매개 변수를 사용하고 전달된 인수의 런타임 유형이 @Classified 애너테이션을 갖는 조인 포인트(Spring AOP에서만 메서드 실행)
- @args(com.xyz.security.Classified)

> tradeService 라는 이름을 가진 스프링 빈의 모든 조인 포인트 (Spring AOP에서만 메서드 실행)
- bean(tradeService)

> 와일드 표현식 *Service 라는 이름을 가진 스프링 빈의 모든 조인 포인트
- bean(*Service)

#### 조인포인트(JoinPoint)
> 추상적인 개념으로 AOP를 적용할 수 있는 지점을 의미
- 어드바이스가 적용될 수 있는 위치, 메소드 실행, 생성자 호출, 필드 값 접근, static 메서드 접근 같은 프로그램 실행 중 지점을 나타냄
- AspectJ를 사용하여 컴파일 시점과 클래스 로딩 시점에 적용하는 AOP는 바이트코드를 실제 조작하여 해당 기능을 모든 지점에 다 적용 가능
- 프록시 방식을 사용하는 스프링 AOP는 메서드 실행 지점에만 AOP를 적용 가능
- 프록시는 메서드 오버라이딩 개념으로 동작
- 생성자나 static 메서드, 필드 값 접근에는 프록시 개념이 적용될 수 없음
- 프록시를 사용하는 스프링 AOP의 조인 포인트는 메서드 실행으로 제한
- 스프링 AOP는 스프링 컨테이너가 관리할 수 있는 스프링 빈에만 AOP를 적용 가능

> ##### JoinPoint 인터페이스의 주요 기능
- JoinPoint.getArgs() : JointPoint에 전달된 인자를 배열로 반환
- JoinPoint.getThis() : AOP 프록시 객체를 반환
- JoinPoint.getTarget() : AOP가 적용된 대상 객체를 반환
  - 클라이언트가 호출한 비즈니스 메서드를 포함하는 비즈니스 객체를 반환
- JoinPoint.getSignature() : 조언되는 메서드에 대한 설명을 반환
  - 클라이언트가 호출한 메서드의 시그니처(리턴차입, 이름, 매개변수) 정보가 저장된 Signature 객체를 반환
  - Signature : 객체가 선언하는 모든 연산을 연산의 이름, 매개변수로 받아들이는 객체
  - Signature가 제공하는 메서드
    - String getName()
      : 클라이언트가 호출한 메서드의 이름을 반환
    - String toLongString()
      : 클라이언트가 호출한 메서드의 리턴타입, 이름, 매개변수를 패키지 경로까지 포함해서 반환
    - String toShortString()
      : 클라이언트가 호출한 메서드 시그니처를 축약한 문자열로 반환
- JoinPoint.toString() : 조언되는 방법에 대한 유용한 설명을 인쇄

#### 애너테이션을 이용한 AOP
- @AspectJ 애너테이션 스타일
- 스키마 기반 접근

##### @AspectJ 지원
- @AspectJ는 애너테이션이 있는 일반 Java클래스로 관점을 선언하는 스타일을 지칭
- @AspectJ 스타일은 AspectJ 5 릴리스의 일부로 AspectJ프로젝트에 의해 도입
- 스프링은 pointcut 구분 분석 및 일치를 위해 AspectJ가 제공하는 라이브러리를 사용하여 AspectJ 5와 동일한 애너테이션을 해석
- AOP 런타임은 여전히 순수한 스프링 AOP이며 AspectJ 컴파일러나 위버에 의존하지 않음

##### @AspectJ 지원 활성화
- Spring 설정에서 자동 프록시 빈에 대한 Spring 지원을 활성화해야 함
- @AspectJ 지원은 XML 또는 Java 스타일 설정으로 활성화 가능
- Java 설정으로 @AspectJ 지원 활성화 방법
  - @Configuration으로 @AspectJ 지원을 홀성화하려면 @EnableAspectJAutoProxy 애너테이션을 추가
- XML 설정으로 @AspectJ 지원 활성화 방법
  - XML 기반 구성으로 @AspectJ 지원을 활성화하려면 aop:aspectj-autoproxy 요소를 사용

---

오늘보다 더 나은 내일이 될 수 있도록!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
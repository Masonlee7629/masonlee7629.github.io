---
title : "코드스테이츠 학습일지 - 2022-10-20"

categories:
  - CodeStates
excerpt: "Spring MVC 아키텍처"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-25
last_modified_at: 2022-10-25
---

## Spring MVC
> Spring의 웹 계층을 담당하는 모듈 중 서블릿(Servlet) API를 기반으로 클라이언트의 요청을 처리하는 모듈
- 서블릿(Servlet)<br>
클라이언트의 요청을 처리하도록 특정 규약에 맞추어서 Java 코드로 작성하는 클래스 파일
<br>
<br>
- 아파치 톰캣(Apache Tomcat)<br>
서블릿들이 웹 애플리케이션으로 실행되도록 해주는 서블릿 컨테이너(Servlet Container) 중 하나

- Model<br>
클라이언트에게 응답으로 돌려우는 작업의 처리 결과 데이터
- View<br>
Model 데이터를 이용해서 웹 브라우저 같은 클라이언트 애플리케이션의 화면에 보여지는 리소스를 제공
- Controller<br>
클라이언트 측의 요청을 전달 받아 Model과 View의 중간에서 상호 작용을 해주는 역할

### Spring MVC의 동작 방식과 구성 요소

![image](https://user-images.githubusercontent.com/105192204/197780422-81ff1faa-f17f-4a3b-b1af-fb401d85c749.png)
<br>
<br>
(1) 클라이언트가 요청을 전송하면 DispatcherServlet이란 클래스에 요청이 전달
<br>
<br>
(2) DispatcherServlet이 클라이언트의 요청을 처리할 Controller에 대한 검색을 HandleMapping 인터페이스에게 요청
<br>
<br>
(3) HandleMapping은 클라이언트 요청과 매핑되는 핸들러 객체를 다시 DispatcherServlet에게 리턴
> 핸들러 객체는 해당 핸들러의 Handler 메서드 정보를 포함<br>
Handler 메서드는 Controller 클래스 안에 구현된 요청 처리 메서드를 의미

(4) DispatcherServlet은 Handler 메서드를 직접 호출하지 않고 HandlerAdapter에게 실제 클라이언트 요청을 처리할 Handler 메서드 호출을 위임
<br>
<br>
(5) HandlerAdapter는 DispatcherServlet으로부터 전달 받은 Controller 정보를 기반으로 Controller의 Handler 메서드를 호출
<br>
<br>
(6) Controller의 Handler 메서드는 비즈니스 로직 처리 후 리턴 받은 Model 데이터를 HandlerAdapter에게 전달
> - 서비스 계층(Service Layer)<br>
클라이언트의 요청 사항을 구체적으로 처리하는 영역
<br>
<br>
- 비즈니스 로직(Business Logic)<br>
실제 클라이언트의 요청 사항을 처리하기 위해 Java 코드로 구현한 것

(7) HandlerAdapter는 전달받은 Model 데이터와 View 정보를 다시 DispatcherServlet에게 전달
<br>
<br>
(8) DispatcherServlet은 전달 받은 View 정보를 다시 ViewResolver에게 전달해서 View 검색을 요청
<br>
<br>
(9) ViewResolver는 View 정보에 해당하는 View를 찾아 View를 다시 리턴
<br>
<br>
(10) DispatcherServlet은 ViewResolver로부터 전달 받은 View 객체를 통해 Model 데이터를 넘겨주며 클라이언트에게 전달할 응답 데이터 생성을 요청
<br>
<br>
(11) View 는 응답 데이터를 생성해서 DispatcherServlet에게 전달
<br>
<br>
(12) DispatcherServlet은 View로부터 전달 받은 응답 데이터를 최종적으로 클라이언트에게 전달
> **DispatcherServlet의 역할**<br>
애플리케이션의 가장 앞단에 배치되어 다른 구성요소들과 상호작용하며 클라이언트의 요청을 처리<br>
이런 처리 패턴은 Front Controller Pattern이라 함

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
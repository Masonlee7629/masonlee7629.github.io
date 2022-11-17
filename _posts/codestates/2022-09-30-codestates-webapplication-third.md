---
title : "코드스테이츠 학습일지 - 2022.09.30"

categories:
  - CodeStates
excerpt: "웹 에플리케이션 작동원리 - HTTP"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-09-30
last_modified_at: 2022-10-04
---

## **Today I Learned**
📚오늘의 체크리스트
- [x] 스터디 미션
- [x] 금일 학습 분량
- [ ] 금일 학습 이해

---

## HTTP(HyperText Transfer Protocol)
- HTML과 같은 문서를 전송하기 위한 Application Layer 프로토콜
- HTTP는 특정 상태를 유지하지 않는 특징을 지님(Stateless, 무상태성)
  - Stateless
    - HTTP로 클라이언트와 서버가 통신을 주고받는 과정에서 HTTP가 클라이언트나 서버의 상태를 확인하지 않음

### HTTP Messages
- 클라이언트와 서버 사이에서 데이터가 교환되는 방식
- 요청(Requests), 응답(Response)로 두 가지 유형이 있음
- 몇 줄의 텍스트 정보로 구성되며 구성 파일, API, 기타 인터페이스에서 HTTP messages를 자동으로 완성 
- 요청과 응답은 다음과 같은 유사한 구조를 가짐
  1. start line : 요청이나 응답의 상태를 나타내며 항상 첫 번째 줄에 위치. 응답에서는 status line이라 부름
  2. HTTP headers : 요청을 지정하거나 메시지에 포함된 본문을 설명하는 헤더의 집합
  3. empty line : 헤더와 본문을 구분하는 빈 줄
  4. body : 요청과 관련된 데이터나 응답과 관련된 데이터 또는 문서를 포함. 요청과 응답의 유형에 따라 선택적 사용

#### 요청(Requests)
##### Start line
- HTTP 요청은 클라이언트가 서버에 보내는 메시지로 Start line에는 세 가지 요소가 있음
  1. 수행할 작업(GET, PUT, POST 등)이나 방식(HEAD OR OPTIONS)을 설명하는 HTTP method를 나타냄
  2. 요청 대상(일반적으로 URL이나 URI) 또는 프로토콜, 포트, 도메인의 절대 경로는 요청 컨텍스트에 작성되며 요청 형식은 HTTP method마다 다름
    - origin 형식 : ? 와 쿼리 문자열이 붙는 절대 경로로 POST, GET, HEAD, OPTIONS 등의 method와 함께 사용
    - absolute 형식 : 완전한 URL 형식으로 프록시에 연결하는 경우 대부분 GET method와 함께 사용
    - authority 형식 : 도메인 이름과 포트 번호로 이루어진 URL의 authority component이며 HTTP 터널을 구축하는 경우 CONNECT와 함께 사용할 수 있음
    - asterisk 형식 : OPTIONS 와 함께 별표하나로 서버 전체를 표현
  3. HTTP 버전에 따라 HTTP message의 구조가 달라지므로 start line에 HTTP 버전을 함께 입력

##### Headers
- 헤더 이름(대소문자 구분이 없는 문자열), 콜론(:), 값을 입력하며 값은 헤더에 따라 다름
- 여러 종류의 헤더가 있고 다음과 같이 그룹을 나눌 수 있음
  - General headers : 메시지 전체에 적용되는 헤더로 body를 통해 전송되는 데이터와 관련이 없는 헤더
  - Request headers : fetch를 통해 가져올 리소스나 클라이언트 자체에 대한 자세한 정보를 포함하는 헤더를 의미하며 User-Agent, Accept-Type, Accept-Language 같은 헤더는 요청을 보다 구체화함. Referer처럼 컨텍스트를 제공하거나 If-None과 같이 조건에 따라 제약을 추가할 수 있음
  - Representation headers : 이전에는 Entity headers로 불렸으며 body에 담긴 리소스의 정보(콘텐츠 길이, MIME 타입 등)를 포함하는 헤더

##### Body
- 요청의 본문은 HTTP messages 구조의 마지막에 위치
- GET, HEAD, DELETE, OPTIONS처럼 서버에 리소스를 요청하는 경우에는 본문이 필요하지 않음
- POST나 PUT과 같은 일부 요청은 데이터를 업데이트하기 위해 사용
- 요청의 body는 다음과 같이 두 종류로 나눌 수 있음
  - Single-resource bodies(단일-리소스 본문) : 헤더 두 개(Content-Type과 Content-Length)로 정의된 단일 파일로 구성
  - Multiple-resource bodies(다중-리소스 본문) : 여러 파트로 구성된 본문으로 각 파트마다 다른 정보를 지님

#### 응답(Responses)
##### Status line
- 응답의 첫 줄로 다음의 정보를 포함
  1. 현재 프로토콜의 버전 (HTTP/1.1)
  2. 상태 코드 : 요청의 결과를 나타냄 (200, 302, 404 등)
  3. 상태 텍스트 : 상태 코드에 대한 설명
- Status line은 "HTTP/1.1 404 Not Found."와 같은 형태

##### Headers
- 요청 헤더와 동일한 구조를 지님
  - General headers : 메시지 전체에 적용되는 헤더로 body를 통해 전송되는 데이터와 관련이 없는 헤더
  - Response headers : 위치 또는 서버 자체에 대한 정보(이름, 버전 등)와 같이 응답에 대한 부가적인 정보를 갖는 헤더로 Vary, Accept-Ranges와 같이 상태 줄에 넣기에는 공간이 부족했던 추가 정보를 제공
  - Representation headers : 이전에는 Entity headers로 불렸으며 body에 담긴 리소스의 정보(콘텐츠 길이, MIME 타입 등)를 포함하는 헤더

##### Body
- 응답의 본문은 HTTP messages 구조의 마지막에 위치
- 201, 204와 같은 상태 코드를 가지는 응답에는 본문이 필요하지 않음
- 응답의 body는 다음과 같이 두 종류로 나눌 수 있음
  - Single-resource bodies(단일-리소스 본문) : 
    - 길이가 알려진 단일-리소스 본문은 두 개의 헤더(Content-Type, Content-Length)로 정의
    - 길이를 모르는 단일 파일로 구성된 단일-리소스 본문은 Transfer-Encoding이 chunked로 설정되어 있으며 파일은 chunk로 나뉘어 인코딩되어 있음
  - Multiple-resource bodies(다중 리소스 본문) : 서로 다른 정보를 담고 있는 body


---

HTTP의 요청 메서드와 상태 코드는 별도로 정리하기!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
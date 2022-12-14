---
title : "코드스테이츠 학습일지 - 2022.10.04"

categories:
  - CodeStates
excerpt: "HTTP"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-04
last_modified_at: 2022-10-04
---

## **Today I Learned**
📚오늘의 체크리스트
- [x] 스터디 미션
- [x] 금일 학습 분량
- [ ] 금일 학습 이해

---

## HTTP
### REST API
- 웹에서 사용되는 데이터나 자원(Resource)을 HTTP URI로 표현하고, HTTP 프로토콜을 통해 요청과 응답을 정의하는 방식
- REST는 "Representational State Transfer"의 약자로, 로이 필딩의 박사학위 논문에서 웹(http)의 정잠을 최대한 활용할 수 있응 아키텍처로써 처음 소개

#### Richardson 성숙도 모델

![image](https://user-images.githubusercontent.com/105192204/193840830-1674c81a-5807-44e6-bec1-5ad89b01bb28.png)

- 로이 필딩이 제시한 REST 방법론을 더 실용적으로 적용하기 위해 레오나르드 리차드슨이 만든 4단계 모델
- 2단계까지만 적용해도 좋은 API 디자인이라 볼 수 있고, 이런 경우 HTTP API라고 부름

##### REST 성숙도 모델 - 0단계
- 단순히 HTTP 프로토콜을 사용하는 것
- 해당 API를 REST API라고 할 수 없으며 좋은 REST API를 작성하기 위한 기본 단계

##### REST 성숙도 모델 - 1단계
- 개별 리소스와의 통신을 준수
- 모든 자원은 개별 리소스에 맞는 엔드포인트(Endpoint)를 사용해야 하며 요청하고 받은 자원에 대한 정보를 응답으로 전달해야 한다는 것
- 엔드포인트는 명사로 작성
  - 엔드포인트 작성법
    : CamelCase : 단어를 붙여쓰며 각 단어의 첫글자를 대문자로 작성(첫 단어의 첫글자는 대소문자 상관 없음)
    : Snake_case : 언더바(_)로 각 단어를 이어서 작성
    : Spinal-case : 하이픈(-)으로 각 단어를 이어서 작성
- 응답 시 사용한 리소스의 정보와 리소스 사용에 대한 성공/실패 여부를 반환해야 함

##### REST 성숙도 모델 - 2단계
- CRUD에 맞게 적절한 HTTP 메서드를 사용하는 것
  - CRUD : 대부분의 컴퓨터 소프트웨어가 가지는 기본적인 데이터 처리 기능인 Create(생성), Read(읽기), Update(갱신), Delete(삭제)를 묶어서 일컫는 말
- REST 성숙도 모델 2단계까지 적용하면 대체적으로 잘 작성된 API라고 여김

##### REST 성숙도 모델 - 3단계
- HATEOAS(Hypertext As The Engine Of Application State)라는 약어로 표현되는 하이퍼미디어 컨트롤을 적용
- 요청은 2단계와 동일하지만 응답에는 리소스의 URI를 포함한 링크 요소를 삽입하여야 함

### Open API와 API Key
#### Open API
- 누구에게나 열려있는 API
- 기관이나 API마다 정해진 이용 수칙이 있고 그에 따라 제한 사항(가격, 정보의 제한 등)이 있을 수 있음

#### API Key
- 서버의 문을 여는 열쇠라는 개념
  - 클라이언트의 요청에 따라 서버에서 응답한다는 것은 서버를 운용하는 데 비용이 발생한다는 것으로 서버 입장에서 조건 없이 익명의 클라이언트에게 데이터를 제공할 의무가 없음. 따라서 로그인된 이용자에게만 자원에 접근할 수 있는 권한을 API Key의 형태로 제공하고 데이터를 요청할 때 API Key를 같이 전달해야만 원하는 응답을 받을 수 있음

---

오늘보다 더 나은 내일이 될 수 있도록!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
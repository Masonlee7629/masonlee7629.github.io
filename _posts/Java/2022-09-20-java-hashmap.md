---
title : "Java TIL - HashMap"

categories:
  - Java
excerpt: "Java HashMap 개별정리"
tags:
  - [Java, TIL]

toc: true
toc_sticky: true

date: 2022-09-20
last_modified_at: 2022-09-22
---

## HashMap
### HashMap이란?
HashMap은 Map 인터페이스를 구현한 대표적인 클래스로 Map의 특징인 키(key)와 값(value)을 묶어서 하나의 데이터로 저장한다는 특징을 지닌다. 
그리고 해싱(Hashing)을 사용하기 때문에 많은 양의 데이터를 검색하는 데 뛰어난 성능을 보인다.
<br>
<br>
HashMap은 Entry라는 내부 클래스를 정의하고 Entry타입의 배열을 선언하고 있으며 Entry객체는 키와 값으로 구성된다.<br>
키와 값은 서로 연관된 값이기 때문에 하나의 클래스로 정의하여 하나의 배열로 다루는 것이 더 바람직하다.

```java
// 키와 값은 각각 Object타입으로 저장
class Entry {
  Object key;       // 컬렉션 내에서 유일해야 하며, 중복 저장 시 기존의 값이 새로운 값으로 대치
  Object value;     // 데이터의 중복을 허용
}
```


---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
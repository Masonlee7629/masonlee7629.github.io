---
title : "[Java] 객체지향 프로그래밍 심화 -1"

categories:
  - CodeStates
excerpt: "Java의 상속과 캡슐화"
tags:
  - [CodeStates, Java, TIL]

toc: true
toc_sticky: true

date: 2022-09-06
last_modified_at: 2022-09-06
---

## <span style="color:#F05A28">**Java의 상속**<span>
### 상속이란?
- 기존의 클래스를 재활용하여 새로운 클래스를 작성하는 자바의 문법 요소
- 상위 클래스의 멤버를 하위 클래스와 공유하는 것을 의미
- extends 키워드를 사용하여 정의
- 단일 상속만을 허용

```java
class Parent {
  // 상위 클래스
}

class Child extends Parent {
  // 하위 클래스, Parent 클래스를 상속받는 Child 클래스
}
```

### 포함 관계
- 포함(composite)은 클래스의 멤버로 다른 클래스 타입의 참조변수를 선언하는 것을 의미
- 클래스 간의 관계가 '~은 ~이다(IS-A)' 관계는 상속, '~은 ~을 가지고 있다(HAS-A)' 관계는 포함
- 단위별 여러 개의 클래스는 코드가 작게 나눠 작성되어 있으므로 코드를 관리하는데 용이함

```java
class Dot {
  int a;
  int b;
}

class Circle {      // Circle 클래스에 int a, b가 있을 경우 Dot을 재사용함
  Dot d = new Dot();
  int c;
}
```

### 메서드 오버라이딩
- 상위 클래스에서 상속받은 메서드의 내용을 변경하는 것
- 메서드 오버라이딩의 사용 조건
  - 메서드의 선언부(메서드명, 매개변수, 반환타입)가 상위 클래스와 일치해야함
  - 접근 제어자의 범위가 상위 클래스의 메서드보다 크거나 동일해야함
  - 예외는 상위 클래스의 메서드보다 많이 선언할 수 없음
- 모든 객체를 상위 클래스 타입으로 선언하면 상위 클래스에서 배열로 선언하여 관리 가능

```java
class Transportation {
  void go(){
    System.out.println("I'm going");
  }
}

public class Bike extends Transportation {
  void go(){
    System.out.println("I ride a bike");
  }

  public static void main(String[] args){
    Bike bike = new Bike();
    bike.run();
  }
}

// 출력 결과
// "I ride a bike"
```

### super 키워드
- super 키워드는 상위 클래스의 객체를 호출하는 것
- 상위 클래스와 변수의 이름이 같을 때 두 변수를 구분하기 위해 사용
- this 키워드와 비슷하지만 상위 클래스의 멤버와 자신의 멤버를 구별하는 것이 차이점

```java
class SuperExam {
  public static void main(String[] args){
    Child child = new Child();
    child.test();
  }
}

class Parent {
  int a = 5;
}

class Child extends Parent {
  int a = 10;

  void test(){
    System.out.println("a = " + a);
    System.out.println("this.a = " + this.a);
    System.out.println("super.a = " + super.a);
  }
}

// 출력 결과
// a = 10
// this.a = 10
// super.a = 5
```

### super() 메서드
- super() 메서드는 상위 클래스의 생성자를 호출하는 것
- 생성자 안에서만 사용 가능하며 모든 생성자의 첫 줄에 반드시 this() 또는 super()를 선언
- 상위 클래스에 기본 생성자가 없으면 에러를 발생

```java
public class Exam {
  public static void main(String[] args){
    Teacher t = new Teacher();
  }
}

class Human {
  Human(){
    System.out.println("Human 클래스 생성자");
  }
}

class Teacher extends Human{    // Human 클래스로 확장
  Teacher(){
    super();    // Human 클래스의 생성자 호출, 없을 경우 컴파일러가 생성자의 첫 줄에 자동으로 super()를 삽입
    System.out.println("Teacher 클래스 생성자");
  }
}

// 출력 결과
// Human 클래스 생성자
// Teacher 클래스 생성자
```

### Object 클래스
- 자바의 클래스 상속계층도에서 최상위에 위치한 클래스
- 다른 클래스로부터 아무 상속을 받지 않는 클래스는 자동적으로 Object 클래스를 상속

```java
class Parent {    // 컴파일러가 "extends Object'를 자동적으로 추가
}

class Child extends Parent{
}
```

### Object 클래스의 대표적인 메서드

|메서드명|반환타입|메서드 특징|
|:---:|:---:|:---:|
|toString()|String|객체 정보를 문자열로 출력|
|equal(Object obj)|boolean|등가 비교 연산과 동일하게 스택 메모리값을 비교|
|hashCode()|int|객체의 위치정보 관련. Hashtable 또는 HashMap에서 동일 객체 여부를 판단|
|wait()|void|현재 쓰레드 일시정지|
|notify()|void|일시정지 중인 쓰레드를 재동작|

## <span style="color:#F05A28">**Java의 캡슐화**<span>
### 캡슐화란?
- 특정 객체 안에 관련된 속성과 기능을 하나의 캡슐로 만들어 데이터를 외부로부터 보호하는 것
- 데이터 보호와 내부적으로 사용되는 데이터의 불필요한 외부 노출을 방지
- 유지보수와 코드 확장 시 오류의 범위를 최소화
- 캡슐화의 핵심 수단으로는 접근제어자(Access Modifier), getter와 setter 메서드가 존재

### 패키지(Package)
- 특정한 목적을 공유하는 클래스와 인터페이스의 묶음
- 패키지는 물리적인 디렉토리이고, 클래스나 인터페이스 파일은 해당 패키지에 속함
- 패키지가 있을 경우 소스 코드의 첫 번째 줄에 반드시 패키지명이 표시되어야 함
- 클래스의 충돌을 방지해주는 기능을 보유
- Ex) java.lang, java.util, java.io 등

### import문
- 다른 패키지 내의 클래스를 사용하기 위해 사용

```java
// test1 패키지
package packageexam.test;

public class ImportExam {
  public int a = 0;
  public void print(){
    System.out.println("Import문 출력");
  }
}

// test2 패키지
package packageexam.test2;

import packgeexam.test.ImportExam;    // Import문 작성

public class PackegeExam{
  public static void main(String[] args){
    ImportExam imp = new ImportExam();  //  test패키지의 class를 활용
  }
}
```

### 제어자(Modifier)
- 자바에서 제어자는 클래스, 필드, 메서드, 생성자 등에 부가적인 의미를 부여하는 키워드
- 접근 제어자와 기타 제어자로 구분
- 하나의 대상에 여러 제어자를 사용할 수 있지만 접근 제어자는 단 한번만 사용 가능

### 접근 제어자(Access Modifier)
- 클래스 외부로의 불필요한 데이터 노출과 데이터가 임의로 변경되지 않도록 방지
- 접근 제어자는 private, default, protected, public으로 분류
- 변수명 앞에 접근 제어자가 없는 경우 접근 제어자는 자동으로 default

|접근 제어자|접근 제한 범위|클래스 내|패키지 내|다른 패키지의 하위 클래스|패키지 외|
|:---:|:---:|:---:|:---:|:---:|:---:|
|private|동일 클래스에서만 접근 가능|O|X|X|X|
|default|동일 패키지 내에서만 접근 가능|O|O|X|X|
|protected|동일 패키지와 다른 패키지의 하위 클래스에서 접근 가능|O|O|O|X|
|public|접근 제한 없음|O|O|O|O|

```java
packge packagetest;   // 패키지명 packagetest

// 파일명 : Parent.java
class Exam {    // 접근 제어자 : default
  public static void main(String[] args){
    Parent p = new Parent();

    // System.out.println(p.a);    // 동일 클래스가 아니므로 에러 발생
    System.out.println(p.b);
    System.out.println(p.c);
    System.out.println(p.d);
  }
}

public class Parent {   // 접근 제어자 : public
  private String a = "Private";
  String b = "Default";
  protected String c = "Protected";
  public String d = "Public";

  public void print2(){     // 동일 클래스로 모두 정상 출력
    System.out.println(p.a);
    System.out.println(p.b);
    System.out.println(p.c);
    System.out.println(p.d);
  }
}

// test 클래스 출력 결과
// "Default"
// "Protected"
// "Public"
```

### getter와 setter 메서드
- 대표적으로 private 접근 제어자가 포함된 객체의 변수의 데이터 값을 수정해야할 때 사용
- setter 메서드는 외부에서 메서드에 접근하여 조건에 맞을 경우 데이터 값을 변경
- getter 메서드는 설정한 변수 값을 읽어오는 데 사용
- 메서드명에 get-, set- 을 붙여서 정의

```java
public class GetterSetter {
  public static void main(){
    Tester t = new Tester();
    t.setName("이대겸");
    t.setAge(32);
    t.setId("masonlee7629");

    String name = t.getName();
    System.out.println("테스터의 이름은 " + name);
    int age = t.getAge();
    System.out.println("테스터의 나이는 " + age);
    String id = t.getId();
    System.out.println("테스터의 ID는 " + id);
  }
}

class Tester {
  private String name;
  private int age;
  private String id;

  public String getName(){    // 멤버변수의 값
    return name;
  }

  public void setName(String name){   // 멤버변수의 값 변경
    this.name = name;
  }

  public int getAge(){
    return age;
  }

  public void setAge(int age){
    this.age = age;
  }

  public String getId(){
    return id;
  }

  public void setId(String id){
    this.id = id;
  }
}

// 출력 결과
// 테스터의 이름은 이대겸
// 테스터의 나이는 32
// 테스터의 ID는 masonlee7629
```

---

전체적인 개념 정리를 다시 할 필요성이 느껴진다.<br>
머릿속에 정보가 들어가며 기존의 지식과 뒤죽박죽 섞이고 있다.<br>
내가 이상한건지 나만 빼곤 다들 똑똑한건지 참...
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
---
title : "[Java] 객체지향 프로그래밍 심화 -2"

categories:
  - CodeStates
excerpt: "Java의 다형성과 추상화, 인터페이스"
tags:
  - [CodeStates, Java, TIL]

toc: true
toc_sticky: true

date: 2022-09-13
last_modified_at: 2022-09-13
---

## <span style="color:#F05A28">**Java의 다형성**<span>
### 다형성(Polymorphism)이란?
- 자바에서 다형성은 한 타입의 참조변수를 통해 여러 타입의 객체를 참조할 수 있도록 만든 것
- 상위 클래스 타입의 참조변수를 통해 하위 클래스의 객체를 참조할 수 있도록 허용
- 반대로 하위 클래스의 타입에서 상위 클래스의 객체를 참조하는 것은 불가능


### 참조변수의 타입 변환
- 참조 변수의 타입 변환은 사용할 수 있는 멤버의 개수를 조절하는 것을 의미
- 상속관계에 있는 상위 클래스와 하위 클래스 사이에만 타입 변환이 가능
- 하위 클래스 타입에서 상위 클래스 타입으로의 타입 변환(업캐스팅)은 형변환 연산자 생략 가능
- 상위 클래스에서 하위 클래스 타입으로 변환(다운캐스팅)은 형변환 연산자를 반드시 명시

### instanceof 연산자
- 캐스팅이 가능한 지 여부를 boolean 타입으로 확인할 수 있는 자바의 문법요소
- '참조_변수 instanceof 타입'으로 확인
- 리턴 값이 true면 참조 변수가 검사한 타입으로 타입 변환이 가능하고 false면 타입 변환 불가능
- 참조변수가 null인 경우 false를 반환

```java
public class InstanceOfExam {
    public static void main(String[] args) {
        Transport transport = new Transport();
        System.out.println(transport instanceof Object);      // true
        System.out.println(transport instanceof Transport);   // true
        System.out.println(transport instanceof Bus);         // false

        Transport taxi = new Taxi();
        System.out.println(taxi instanceof Object);           // true
        System.out.println(taxi instanceof Transport);        // true
        System.out.println(taxi instanceof Bus);              // false
        System.out.println(taxi instanceof Taxi);             // true
    }
}

class Transport {};
class Bus extends Transport {};
class Taxi extends Transport {};
```

### 다향성의 활용 예제
```java
public class PolymorphismExam {
    public static void main(String[] args) {
        Customoer customoer = new Customoer();
        customoer.buySnack(new Icecream());
        customoer.buySnack(new Candy());

        System.out.println("현재 잔액은 " + customoer.money + "원 입니다.");
    }
}

class Snack {
    int price;

    public Snack(int price){
        this.price = price;
    }
}

class Icecream extends Snack {
    public Icecream(){
        super(4000);    //  상위 클래스인 Snack의 생성자를 호출
    }

    public String toString(){
        return "아이스크림";
    }
};
class Candy extends Snack {
    public Candy(){
        super(5000);
    }

    public String toString(){   // Object클래스의 toString()메서드 오버라이딩
        return "사탕";
    }
};

class Customoer {
    int money = 50000;

    void buySnack(Snack snack){
        if(money < snack.price){
            System.out.println("잔액이 부족합니다.");
            return;
        }

        money = money - snack.price;
        System.out.println(snack + "을 구매했습니다.");
    }
}

// 출력 결과
// 아이스크림을 구매했습니다.
// 사탕을 구매했습니다.
// 현재 잔액은 41000원 입니다.
```

## <span style="color:#F05A28">**Java의 추상화**<span>
### 추상화(Abstraction)란?
- 객체의 공통적인 속성과 기능을 추출하여 정의하는 것
- 기존 클래스들의 공통적인 요소를 추려서 상위 클래스를 만들어 내는 것

### abstract 제어자
- 주로 클래스와 메서드를 형용하는 사용하는 키워드
- 메서드 앞에 붙은 경우를 '추상 메서드(abstract method)', 클래스 앞에 붙은 경우를 '추상 클래스(abstract class)'라 지칭

```java
abstract class AbstractClass {      // 추상 메서드를 최소 하나 이상 포함하는 추상 클래스
  abstract void abstractMethod();   // 메서드 바디가 없는 추상 메서드
}
```

### 추상 클래스
- 메서드 시그니처만 존재하고 바디가 선언되지 않은 추상 메서드를 포함하는 클래스
- 객체의 생성 불가능
- 상속 관계에 있어 새로운 클래스를 작성하는데 매우 유용함
- 오버라이딩으로 추상 클래스로부터 상속받은 추상 메서드의 내용을 구현하고 객체를 생성

```java
abstract class Transportation {
  public String name;
  public abstract void horn();
}

class Car extends Transportation {    // Transportation 클래스로부터 상속
  public Car(){
    this.name = "자동차";
  }

  public void horn(){                 // 메서드 오버라이딩을 통한 구현부 완성
    System.out.println("빵빵");
  }
}

class Bicycle extends Transprotation {    // Transportation 클래스로부터 상속
  public Bicycle(){
    this.name = "자전거";
  }

  public void horn(){
    System.out.println("따르릉");         // 메서드 오버라이딩을 통한 구현부 완성
  }
}

class CarExam {
  public static void main(String[] args){
    Transportation car = new Car();
    car.horn();

    Bicycle bicycle = new Bicycle();
    bicycle.horn();
  }
}

// 출력 결과
// 빵빵
// 따르릉
```

### final 키워드
- final 키워드는 필드, 지역 변수, 클래스 앞에 위치할 수 있으며 그 위치에 따라 의미가 다름
- 더 이상 변경이 불가하거나 확장되는 않는 성질을 지님

```java
final class FinalExam {   // 확장 및 상속 불가능한 클래스
  final int a = 10;       // 변경 불가능한 상수

  final int sum(){        // 오버라이딩 불가능한 메서드
    final int lv = a;     // 상수
    return a;
  }
}
```

|위치|의미|
|:---:|:---:|
|클래스|변경 또는 확장 불가능한 클래스, 상속 불가능|
|메서드|오버라이딩 불가능|
|변수|값 변경이 불가능한 상수|


## <span style="color:#F05A28">**Java의 인터페이스**<span>
### 인터페이스란?
- 추상 클래스와 유사하지만 추상 클래스보다 추상화 정도가 높음
- 기본적으로 추상 메서드와 상수만을 멤버로 가질 수 있음
- 추상 클래스처럼 완성되지 않은 불완전한 것으로 다른 클래스를 작성하는데 도움을 줄 목적으로 작성

### 인터페이스의 기본 구조
- 클래스를 작성하는 것과 유사하지만 class 키워드 대신 interface 키워드를 사용
- 내부의 모든 필드가 public static final 로 정의되며 생략 가능
- static과 default 메서드 이외의 모든 메서드는 public abstract로 정의되며 생략 가능

```java
public interface InterfaceName {
  public static final int first = 5;    // 인터페이스 인스턴스 변수 정의
  final int second = 10;                // public static 생략
  static int third = 15;                // public, final 생략

  public abstract String getNumber();
    void call();                        // public abstract 생략
}
```

### 인터페이스의 구현
- 자체적으로 인스턴스를 생성할 수 없고 메서드 바디를 정의하는 클래스를 따로 작성해야 함
- '구현하다'라는 의미를 가진 implements 키워드를 사용
- 인터페이스를 구현한 클래스는 해당 인터페이스가 가진 모든 추상 메서드를 오버라이딩하여 바디를 완성하는 것

```java
class 클래스명 implements 인터페이스명 {
  // 인터페이스에 정의된 추상메서드를 구현
}
```

### 인터페이스의 다중 구현
- 클래스 간의 다중 상속은 허용되지 않으나 인터페이스는 다중적 구현이 가능
- 인터페이스는 인터페이스로부터만 상속이 가능하며 클래스와 달리 Object클래스와 같은 최고 조상이 없음

---

추석 연휴가 이제 끝났다.<br>
연휴에 컴퓨터를 할 수 없는 상황이 되어 생각했던 것보다 공부를 하지 못해 아쉬움이 남는다.<br>
그래도 쉴만큼 쉬었으니 이제 다시 달려보자!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---
# 백기선님의 JAVA live-study

live-study(https://github.com/whiteship/live-study)

`11주차 목표`

자바의 열거형에 대해 학습하세요.


## 목차
<!-- TOC -->
- [목차](#목차)
- [Enum이란?](#Enum이란?)
- [java.lang.Enum](#java.lang.Enum)
- [EnumSet](#EnumSet)

## Enum이란?
Enum은 열거형(enumeration)이라는 뜻이며, 서로 관련된 상수를 편리하게 선언하기 위한 것이다. `JDK 1.5`부터 도입된 개념이다. 열거형을 정의하는 방법은 다음과 같다. 

~~~java
// enum 열거형 이름 {상수명1, 상수명2, 상수명3, ...}

// 열거형의 첫 요소부터 0, 1, 2, 3 의 넘버를 부여한다.
enum Direction {NORTH, SOUTH, WEST, EAST;}
~~~

기본적으로 JAVA의 열거형은 `typesafe` 하다는 특징이 있다. 
이는 기존 C언어의 열거형과 대비되는데, 아래 코드를 살펴보자.

~~~java

enum Direction {NORTH, SOUTH, WEST, EAST;}
enum Food {BURGER, CHICKEN, PIZZA};

System.out.println(Direction.NORTH == Food.BURGER)

// false
~~~

Direction 열거형의 NORTH와 Food 열거형의 BURGER은 모두 넘버 0이 부여된다.
따라서 C언어의 경우에는 출력되는 결과가 true이다.
하지만 JAVA의 열거형은 typesafe 즉, type까지도 체크하므로 false를 출력하게 되는 것이다.

### 열거형 상수의 생성 원리
enum의 원리는 클래스를 통해 정의되는 것이다.

~~~java
enum Direction {NORTH, SOUTH, WEST, EAST}
// enum의 내부 구조를 살펴보면 아래와 같음을 확인할 수 있다.

class Direction{
    public static final Direction NORTH = new Direction();
    public static final Direction SOUTH = new Direction();
    public static final Direction WEST = new Direction();
    public static final Direction EAST = new Direction();
}
~~~

### enum의 생성자
enum에서 상수를 선언할 때, 상수명 옆에 `()`를 사용할 수 있다.
그 이유는, enum type은 역시 클래스이기 때문에, 상수 객체를 만들 때
객체를 초기화하는 생성자를 사용할 수 있기 때문이다.

~~~java
enum DisplayType {
    CRT("Cathode Ray Tube"),
    FPD("Flat Panel Display"),
    LCD("Liquid Crystal Display"),

    private final String currentType;

    private DisplayType(String currentType) {
        this.currentType = currentType;
    }

    public String getCurrentType() {
        return this.currentType;
    }
}

public class EnumDemo {
    public static void main(String[] args) {
        System.out.println(DisplayType.CRT.getCurrentType());
        
    }
}
~~~

예제를 보면 알 수 있듯이, 생성자는 `private`으로 생성하여 사용한다.
그 외의 접근 지정자를 이용하면 컴파일 에러가 발생한다.
enum은 기본적으로 상수들의 집합이므로, 컴파일 타임에 모든 정보들이 고정되어야 한다. 따라서 `private` 생성자를 사용하여 enum타입에 접근을 막는다. `private` 키워드를 생략할 수도 있다. 자동으로 컴파일러가 private으로 컴파일하기 때문이다.


### enum의 비교
열거형 상수를 비교하기 위해서는 기본적으로 '=='을 사용한다. 
하지만, ">", "<" 과 같은 비교연산자는 사용할 수 없다. 그 이유는? 클래스이기 때문이다.

또, switch문의 조건식에도 사용할 수 있다.

~~~java
switch(dir){
    case EAST: //executable
    case WEST: //executable
}
~~~

이때, case문에 열거형 이름 없이 상수만을 적어야 한다는 점을 유의해야 한다.
참고로 switch문에는 primitive(기본) 자료형과 enum(열거형)만 사용할 수 있다.

<br>

## java.lang.Enum
모든 열거형은 java.lang.Enum 클래스를 조상 클래스로 가진다.
해당 클래스는 **추상 클래스** 이며,  enum 조작을 위한 다양한 메소드를 가지고 있다.
모든 열거형은 Enum 클래스를 상속받으므로, enum type은 별도의 상속을 받을 수 없다.

주요 메소드를 표로 정리하면 다음과 같다.

|메서드|설명|
|:---:|---|
|`T[] values()`|해당 열거형 타입에 정의된 상수 배열을 반환한다.|
|`Class<E> getDeclaringClass()`|열거형의 객체를 반환한다.|
|`String name()`|열거형 상수가 정의된 이름을 반환한다.|
|`int ordinal()`|열거형 상수가 정의된 순서(index)를 반환한다.|
|`T valueOf(class<T> enumType, String name)`|지정된 열거형에서 name과 일치하는 열거형 상수를 반환한다.|

java.lang.Enum 클래스가 제공하는 메서드들을 자유롭게 사용해보았다.

~~~java
enum Foods {BURGER, CHICKEN, PIZZA, COFFEE}

public class EnumDemo{
    public static void main(String[] args){

        //values()
        for(Foods food : Foods.values()){
            System.out.println(food);
        }

        // valueOf()
        Foods pizza = Foods.valueOf("PIZZA"); 
    }
}
~~~

## EnumSet
`Enumset`이란, enum 클래스를 위한 set컬렉션이다. java.util 패키지 클래스에 저장되어 있다. `EnumSet`은 비트 연산을 이용하므로 빠른 연산 속도를 지원하며, 적은 메모리를 사용한다는 이점이 존재한다. 

* Enumset은 `Set 인터페이스`를 구현하며, `AbstractSet` 클래스를 상속한다.
* 열거형 값만 가질 수 있으며, null을 가질 수 없다. 이를 추가하려고 한다면 `NullPointerException`이 발생하게 된다.
* thread-safe하지 않다. 이를 위해 외부에서 직접 동기화를 구현하거나, `Collections.synchronizedMap`을 사용해야 한다.
* 열거형의 element들은 순서에 따라 저장된다.

* EnumSet 자료형이 제공하는 메서드

|메서드|설명|
|:---:|---|
|`copyOf(EnumSet s)`|매개변수로 들어온 EnumSet을 복사한다.|
|`noneOf(Class elementType)`|빈 EnumSet을 반환한다.|
|`of(E e1, E e2)`|열거형 상수 2개를 입력받아 새로운 EnumSet에 추가 후에 반환한다.|
|`complementOf(EnumSet s)`|매개변수에 들어온 EnumSet의 열거형 상수들을 제외한 열거형 상수들을 새로운 EnumSet에 넣어 반환한다.|
|`range(E from, E to)`|인자로 받은 열거형 상수 사이의 범위를
인덱스의 순서대로 새로운 EnumSet에 넣어 반환한다.|

Enum을 활용한 자료구조로 `HashMap`과 유사한 `EnumMap`도 존재한다.


## 출처
* http://www.tcpschool.com/java/java_api_enum
* https://www.w3schools.com/java/java_enums.asp
* https://docs.oracle.com/javase/7/docs/api/java/lang/Enum.html
* https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/lang/Enum.html
* https://velog.io/@kwj1270/Enum
* 자바의 정석(남궁성 저) 691p ~ 701p

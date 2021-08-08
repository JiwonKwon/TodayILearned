# Chapter6. 객체와 자료구조

변수를 비공개(private)으로 정의하는 이유 : 

남들이 변수에 의존하지 않게 만들어서, 충동이든 변덕이든 맘대로 바꾸게 하려고! 

근데 왜 많은 프로그래머들은 get함수 set함수를 public 하여 외부에 노출시킬까  ? ? ? ? 

---

## 자료추상화

example1) 보다는 exmaple2) 가 나음!

example1) 구체적인 Point 클래스

```java
public class Point{

	public double x;

	public double y;

}
```

직교좌표계인지 극좌표계인지 알길이 없으며 구현을 외부로 노출한다. 변수를 private으로 선언하더라도 get set 함수를 제공한다면 구현을 외부로 노출하는 셈.

example2) 추상적인 Point 클래스

```java
public interface Point{

double getX();

double getY();

void setCartesian(double x, double y);

double getR();

}
```

자료구조를 명백히 표현하고 있다. 

사실 자료 구조 이상을 표현하고 있음 → 좌표를 읽을때는 각 값을 개별적으로 읽어야하며, 좌표를 설정할때는 두 값을 한꺼번에 설정해야한다.

get set함수로 변수를 다룬다고해서 클래스가 되는것은 아니다.! 

추상 인터페이스를 제공해 사용자가 *구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 클래스*다.

example3)보다는 example4) 가 나음!!

example3) 구체적인 Vehicle 클래스 

```java
public interface Vehicle {
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}
```

example4) 추상적인 Vehicle 클래스

```java
public interface Vehicle {
  double getPercentFuelRemaining();
}
```

자료를 세세하게 공개하기보다는 추상적인 개념으로 표현해야 한다.

인터페이스나 get/set 함수만으로는 추상화가 이뤄지지 않는다.

개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 심각하게! 고민해야한다. 아무 생각 없이 get set 함수를 추가하는것이 제일 나쁘다 .. 

---

## 자료/객체 비대칭

개념1) 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개한다.

개념2) 자료 구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.

→ 두 개념은 본질적으로 상반된다.

example5) 절차적인 도형

```java
public class Square {
	public Point topLeft;
	public double side;
}

public class Rectangle {
	public Point topLeft;
	public double height;
	public double width;
}

public class Circle {
	public Point center;
	public double radius;
	public double width;
}

public class Geometry {
	public final double PI = 3.141592653585793;

	public double area(Object shape) throws NoSuchShapeException {
		if(shape instanceOf Square) {
			Square s = (Square)shape;
			return s.side * s.side;
		}
	else if(shape instanceOf Rectangle) {
			Rectangle r = (Rectangle)shape;
			return r.height * r.width;
		}
	else if(shape instanceOf Circle) {
			Circle c = (Circle)shape;
			return PI * c.radius * c.radius
		}
	}
}
```

만약 Geometry 클래스에 함수를 추가하고 싶다면 도형 클래스는 아무 영향도 받지 않는다.

새 도형을 추가하고 싶다면 Geometry 클래스에 속한 함수를 모두 고쳐야 한다.

example6) 객체지향적인 도형

```java
public class Square implements Shape {
	public Point topLeft;
	public double side;

	public double area() {
		return side*side;
	}
}

public class Rectangle implements Shape {
	public Point topLeft;
	public double height;
	public double width;

	public double area() {
		return height*width;
	}
}

public class Circle implements Shape {
	public Point center;
	public double radius;
	public double width;

	public double area() {
		return PI*radius*radius;
	}
}
```

새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다!

반면 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 한다!

정리)

> 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면, 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.

객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 

객체 지향 코드에서 쉬운 변경은 절차적인 코드에서 어렵다..

상호보완적인 관계..

---

## 디미터의 법칙

디미터 법칙은 잘 알려진 [휴리스틱(heuristic)](https://ko.wikipedia.org/wiki/%ED%9C%B4%EB%A6%AC%EC%8A%A4%ED%8B%B1_%EC%9D%B4%EB%A1%A0) 으로, 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙

→ 객체는 조회 함수로 내부 구조를 공개하면 안 된다는 의미다

**기차 충돌 (train wreck)**

```java
String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
```

→ 메소드가 반환하는 객체의 메소드를 사용해서 디미터 법칙 위반

→ 아래 코드로 변환하는 것이 바람직함.

example7)

```java
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
String outputDir = scratchDir.getAbsolutePath();
```

위 예제가 디미터 법칙을 위반하는지 여부는 위의 변수들이 객체인지 자료 구조인지에 달렸다. 

객체라면 내부 구조를 숨겨야 하므로 확실히 디미터 법칙을 위반한다. 

반면, 자료 구조라면 당연히 내부 구조를 노출하므로 문제되지 않는다.

**잡종 구조**

이런 혼란때문에 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다. 단점만 모아놓은 구조로 되도록 피하자

**구조체 감추기**

위의 example7) 예제의 경우 좋은 방식이 아니다. 이 outputDir 경로가 왜 필요할지 같은 모듈에서 찾아 보았더니 (한참 아래로 내려가서) 이런 코드가 있다.

```java
String outFile = outputDir + "/" + className.replace('.', '/') + ".class"; 
FileOutputStream fout = new FileOutputStream(outFile); 
BufferedOutputStream bos = new BufferedOutputStream(fout);
```

위 코드에 따르면 경로를 얻으려는 이유가 임시 파일을 생성하기 위함을 알 수 있다.

그렇다면 ctxt 객체에 임시 파일을 생성하라고 시키면 어떨까?

ctxt 객체에게 임시 파일을 생성하라고 시키면 ctxt는 내부 구조를 드러내지 않으며, 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없다.

```java
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```

---

## 자료 전달 객체

자료 구조체의 전형적인 형태는 공개 변수만 있고 함수가 없는 클래스다. 이런 자료 구조를 DTO라 한다.

***DTO***

공개 변수만 있고 함수가 없는 클래스

***Bean***

비공개 변수와 getter, setter가 있는 클래스

***활성 레코드***

DTO의 특수한 형태.

공개, 비공개 변수와 getter, setter, 그리고 save, find 같은 탐색 함수가 있는 클래스

---

## 결론

> 객체는 동작을 공개하고 자료를 숨긴다.→ 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉽다.→ 기존 객체에 새 동작을 추가하기는 어렵다.

> 자료 구조는 별다른 동작 없이 자료를 노출한다.→ 기존 자료구조에 새 동작을 추가하기는 쉽다.→ 기존 함수에 새 자료 구조를 추가하기는 어렵다.

**바람직한 구조**

- 객체: 비공개 변수와 공개 함수만 포함
- 자료 구조: 함수 없이 공개 변수만 포함

**적합한 쓰임**

- 객체: 새로운 자료 타입 추가
- 자료 구조: 새로운 메소드 추가

느낀점

뭔가 한번에 이해가 가지 않았다.

지금도 이해안가는 부분이 있다... 자료구조라는 단어가 명확하게 다가오지 않아서 더 그런것같기도 하구..

영어로 Data Structure인데 뭔가 Data 라고 이해하는게 더 와닿는것같기도 하고........ 

몇번 더 읽어보고 예제도 많이 접해봐야 완전 이해할 수 있을 것 같다.!! 

[https://github.com/andorx/clean-code/blob/master/chapter-6.md](https://github.com/andorx/clean-code/blob/master/chapter-6.md)
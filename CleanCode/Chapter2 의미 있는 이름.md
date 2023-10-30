# Chapter2. 의미 있는 이름

인상깊었던 부분과 읽으면서 실무에서 떠오르는 것들을 (찔리는 것들을) 정리해 보았다.

## 의도를 분명히 밝혀라

> 코드의 맥락이 코드에 명시적으로 드러나야한다.


```java
public List<int[]> getThem(){

    List<int[]> list1 = new ArrayList<int[]>();
    for(int[] x : theList)
    	if(x[0]==4)
    		list.add(x);
    return list1;
}
```

이 코드를 보면 의미없는 이름으로 의도가 드러나지 않는다.

```java
final int STATUS_VALUE = 0;
final int FLAGGED = 4; 

public List<int[]> getFlaggedCells(){

    List<int[]> flaggedCells = new ArrayList<int[]>();
    for(int[] cell : gameBoard)
    	if(cell[STATUS_VALUE]==FLAGGED)
    		flaggedCells.add(cell);
    return flaggedCells;
}
```

같은 코드이지만 의미하는 cell의 상태는 STATUS_VALUE 이고, 깃발의 상태는 FLAGGED 인 것을 알 수 있다.

더 나아가서는 Int 배열을 멤버변수를 가진 클래스로 만들면, 더 명확해진다. 

```java
public List<Cell> getFlaggedCells(){
    List<Cell> flaggedCells = new ArrayList<int[]>();
    for(Cell cell : gameBoard)
    	if(cell.isFlagged)
    		flaggedCells.add(cell);
    return flaggedCells;
}
```

## 의미있게 구분하라

> 연속된 숫자를 덧붙이거나 불용어를 사용하는 방식은 적절하지 못하다.

예시)

세글자 연속된 문자를 찾으면 false 를 리턴해주는 메소드이다. 

*a1, a2, a3 와 같은 연속된 숫자의 의미없는 변수명들로 이루어져있어서 의도가 드러나지 않는다.*

```java
// 연속 문자 금지

char[] arrPswd = pswd.toLowerCase().toCharArray();

for (int i = 0; i < arrPswd.length; i++) {
				if (i < (arrPswd.length - 2)) // 동일한 3문자
				{
					int a1 = Character.getNumericValue(arrPswd[i]);
					int a2 = Character.getNumericValue(arrPswd[i + 1]);
					int a3 = Character.getNumericValue(arrPswd[i + 2]);

					if ((arrPswd[i] >= 'a' && arrPswd[i] <= 'z') || (arrPswd[i] >= 'A' && arrPswd[i] <= 'Z')) {
						a1 = a1 - 50;
					}

					if ((arrPswd[i + 1] >= 'a' && arrPswd[i + 1] <= 'z') || (arrPswd[i + 1] >= 'A' && arrPswd[i + 1] <= 'Z')) {
						a2 = a2 - 50;
					}

					if ((arrPswd[i + 2] >= 'a' && arrPswd[i + 2] <= 'z') || (arrPswd[i + 2] >= 'A' && arrPswd[i + 2] <= 'Z')) {
						a3 = a3 - 50;
					}

					// 123, abc, cba 연속 3문자
					if (((a1 + 1) == a2) && ((a2 + 1) == a3) && a1 != -1){
                return false;
					}

					if (((a1 - 1) == a2) && ((a2 - 1) == a3) && a3 != -1){
                return false;
					}
			}
```

조금 시간을 들여서 고민해서 고쳐보았다.

```java
// 연속 문자 금지
  final char[] passwordCharArray = newPassword.toLowerCase().toCharArray();

  for (int i = 0; i < passwordCharArray.length - 2; i++) {

      final int firstChar = Character.getNumericValue(passwordCharArray[i]);
      final int secondChar = Character.getNumericValue(passwordCharArray[i + 1]);
      final int thirdChar = Character.getNumericValue(passwordCharArray[i + 2]);

      final int firstCharSequence;
      if ((passwordCharArray[i] >= 'a' && passwordCharArray[i] <= 'z') || (passwordCharArray[i] >= 'A' && passwordCharArray[i] <= 'Z')) {
          firstCharSequence = firstChar - 50;
      }else{
          firstCharSequence = firstChar;
      }

      final int secondCharSequence;
      if ((passwordCharArray[i + 1] >= 'a' && passwordCharArray[i + 1] <= 'z') || (passwordCharArray[i + 1] >= 'A' && passwordCharArray[i + 1] <= 'Z')) {
          secondCharSequence = secondChar - 50;
      }else{
          secondCharSequence = secondChar;
      }

      final int thirdCharSequence;
      if ((passwordCharArray[i + 2] >= 'a' && passwordCharArray[i + 2] <= 'z') || (passwordCharArray[i + 2] >= 'A' && passwordCharArray[i + 2] <= 'Z')) {
          thirdCharSequence = thirdChar - 50;
      }else{
          thirdCharSequence = thirdChar;
      }

      // 123, abc, cba 연속 3문자
      if (((firstCharSequence + 1) == secondCharSequence) && ((secondCharSequence + 1) == thirdCharSequence) && firstCharSequence != -1) {
          return false;
      }
      if (((firstCharSequence - 1) == secondCharSequence) && ((secondCharSequence - 1) == thirdCharSequence) && thirdCharSequence != -1) {
          return false;
      }

  }
```

> 불용어를 사용하지말자

불용어를 추가한이름은 아무런 정보를 주지 못한다. Product 나 ProductInfo나 moneyAmount 나 money나 차이점이 없다. 

1개월차 신입시절에 *MemeberParticularInfo* 라고 클래스명을 지었다가 코드리뷰로 클래스 이름에 대한 댓글이 달렸던 기억이 낫다.. 그래서 그랬구나.. ^.^ 

## 검색하기 쉬운 이름을 사용하라

```jsx
var s = 0
var t = arrayOf(0)
for(j in 0..34){
    s += (t[j] * 4) /5
}
```

```jsx
val realDaysPerIdealDay = 4
val WORK_DAYS_PER_WEEKS = 5
val NUMBER_OF_TASKS = 34
for(j in 0..NUMBER_OF_TASKS){
    val realTaskDays = taskEstimate[j] * realDaysPerIdealDay
    val realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEKS)
    sum += realTaskWeeks
}
```

이름을 의미있게 지으면 함수가 길어진다. 

하지만 검색할 때를 생각해보면 5를  찾기보다 WORK_DAYS_PER_WEEKS 를 검색하는 것이 훨씬 ! 간편하다.

## **한 개념에 한 단어를 사용하라**

어떤건 DeviceManager를 쓴다던지 어떤거는 ProtocolController 라던지 Controller와 Manager라는 단어를 혼용해서 쓰는건 좋지않다.

## 해법영역에서 가져온 이름을 사용해라

코드를 읽는 사람도 프로그래머이므로, 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 사용해도 괜찮다. 

### 느낀점

일을 하면서도 느끼지만 적절한 이름 짓는건 생각보다 고민되는 일이다. 

하지만 남의 코드 읽을때 의미있는 이름의 중요성을 확실히 느낀다. 

이름이 ~~거지같이~~ 되어있으면 왜이렇게했을까 하고 헛웃음 날때도 있다..^.^

누군가 나중에 내코드를 봤을 때 헛움음 나지 않도록 

앞으로도 이름에 좀더 노력을 들여야겠다 ~~~~~~~~~
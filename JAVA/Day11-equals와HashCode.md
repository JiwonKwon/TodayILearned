# day11

## Java의 equals와 HashCode

*Map과 HashCode*

`HashMap` `HashSet` `HashTable`과 같은 객체들을 사용하는 경우, 객체의 의미상 동등성 비교를 위해 **hashCode()**를 호출한다.

## **이런 상황은 어떨까요?**

- equals()가 true인 두 Object를 HashMap에 put을 할 때 동일한 Key로 인식하고 싶은 경우

```java
Map<Developer, Integer> map = new HashMap<Developer, Integer>();
Developer p1 = new Developer();
p1.setAge(28);
p1.setName("Jibab");

Developer p2 = new Developer();
p2.setAge(28);
p2.setName("Jibab");

// p1 p2를 equals 비교하려면 equals 를 overriding하면됨

map.put(p1, 1);
map.put(p2, 1);
System.out.println(map.size()); // 2

// map의 key값을 동일하게 인식하려면 hashcode를 overriding
```

### **왜(Why) 크기가 2일까요?**

- Hash를 사용한 Collection(HashMap, HashTable, HashSet, LinkedHashSet등등)은 key를 결정할때 `hashCode()`를 사용하기 때문에 그렇습니다.

근데 나는 당연히 2라고 생각했다,, 깊게는 생각못했지만 아무튼 ,,^^ 가튼거라도 두번넣었으니까 두개지!!!! 라고 당당하게 대답함

### **어떻게(How) Key 값이 동일하게 인식할 수 있을까요?**

- hashCode()를 @Override하면 문제를 해결할 수 있습니다.

hashCode를 오버라이딩해주고 위의 코드를 다시 실행하면 사이즈는 1이 됨.

+) Map은 put하는 순간에 들어오는 hashCode 값을 기억하므로 후에 값이 바뀌더라도 결과가 바뀌진 않는다.
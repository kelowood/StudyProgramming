# 리팩토링 Inline Temp : 리팩토링에 방해되는 임시변수 제거



## 개요

→ 아주 간단한 연산을 하는 임시변수가 존재하는데 <font color="red">이 임시변수가 다른 리팩토링을 방해한다면 원래 연산으로 바꾼다.</font>



## Inline Temp의 사용 필요성

* 보통 <font color="steelblue">메소드 호출의 결과값이 임시변수에 대입되는 경우</font>에 사용된다.
* 임시변수가 <font color="red">Extract Methods 같은 리팩토링에 방해가 될시</font>에도 사용된다.



## 주의사항

→ 지나치게 <font color="red">복잡한 연산의 경우</font>는 좀 더 생각해보고 변경하길 바란다. 특히 연산량이 많고, 많이 사용되는 임시변수는 더더욱 그렇다.



## 예시

```java
double basePrice = anOrder.BasePrice();
return (basePrice > 1000);
```

<p align="center">▼</p>

```java
return (anOrder.BasePrice() > 1000);
```

→ 불필요한 임시변수를 제거하고 연산을 직접 넣었다.

<font color="red">ㅋㅋ</font>
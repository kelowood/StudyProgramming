# 리팩토링 Inline Method : 지나치게 간단-명확한 메소드의 제거



## 개요

→ 지난 Extract Methods 와는 반대개념. 이번에는 보기에도 너무나도 명확한 메소드를 삭제하고 코드조각으로 만든다.



## Inline Method의 사용 필요성

1. <font color="#4f81bd">메소드의 몸체가 메소드의 이름만큼 명확</font>할때
2. 불필요한 <font color="#4f81bd">인디렉션 (간접접근)을 줄이고자 할때</font>
3. <font color="red">메소드가 잘못 나뉘어져 있을때</font> 그것을 합칠시 사용



## 주의사항

* 다형성을 가지고 있는 메소드 (<font color="red">오버라이딩 메소드</font>)에는 사용해서는 안된다,.
* 코드조각으로 바꾼 다음 잊지말고 <font color="red">기존 메소드는 제거</font>한다.
* <font color="red">재귀가 사용되는 등의 복잡성을 가진 함수</font>는 적용하지 않는게 좋을 수도 있다.



## 예시

```java
int GetRating()
{
    return (MoteThanFiveLateDeliveries()) ? 2 : 1;
}

bool MoteThanFiveLateDeliveries()
{
    return m_numberOfLateDeliveries > 5;
}
```

<p align="center">▼</p>

```java
int GetRating()
{
    return (m_numberOfLateDeliveries > 5) ? 2 : 1;
}
```

→ 지나치게 간략하고 동작이 명확한 메소드를 제거하였다.
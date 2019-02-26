# 리팩토링 Replace Temp with Query : 임시변수를 질의로 바꾸기
<p align="right">2019-02-23</p>



## 개요

→ 수식의 결과값을 저장하기 위한 용도로 임시변수를 사용하는 거라면 <font color="#4f81bd"> 수식을 질의 메소드로 바꾼다.</font>

→ 클래스 내의 <font color="#4f81bd">모든 메소드에서 해당 질의를 사용할 수 있도록 처리</font>

(임시변수를 사용한다면 해당 메소드 내에서 밖에 사용할 수 없다.)

→ <font color="#4f81bd">Inline Temp 리팩토링과 조합</font>하여 임시변수를 모두 질의메소드로 바꿀수도 있다.



## 사용 필요성

→ 임시변수에 값이 한번만 대입되고 대입문을 만드는 수식이 부작용을 초래하지 않을때 사용한다.

(임시변수에 값이 여러번 대입되는 경우는 Split Temporary Variable을 먼저 사용하길 바란다.)



## 예시

```c#
double GetPrice()
{
    int basePrice = m_quantity * m_itemPrice;
    double discountFactor;

    if (basePrice > 1000)
        discountFactor = 0.95;
    else
        discountFactor = 0.98;

    return basePrice * discountFactor;
}
```

→ 먼저 위의 메소드에서 시작한다. 여기서 basePrice와 discountFactor 임시변수를 제거하고자 한다.

(Inline Temp와 병행 활용)

<p align="center">▼</p>

```c#
double GetPrice()
{
    int basePrice = GetBasePrice();
    double discountFactor;

    if (basePrice > 1000)
        discountFactor = 0.95;
    else
        discountFactor = 0.98;

    return basePrice * discountFactor;
}

private int GetBasePrice()
{
    return m_quantity * m_itemPrice;
}
```

→ m_quantity * m_itemPrice 연산을 뽑아내어 메소드로 만들었다.

→ 이 아래부터는 Inline Temp 리팩토링 작업이다.

<p align="center">▼</p>

```c#
double GetPrice()
{
    int basePrice = GetBasePrice();
    double discountFactor;

    if (GetBasePrice() > 1000)
        discountFactor = 0.95;
    else
        discountFactor = 0.98;

    return GetBasePrice() * discountFactor;
}
```

→ basePrice 임시변수를 사용하는 부분을 모두 메소드로 바꾼다.

<p align="center">▼</p>

```c#
double GetPrice()
{
    double discountFactor;

    if (GetBasePrice() > 1000)
        discountFactor = 0.95;
    else
        discountFactor = 0.98;

    return GetBasePrice() * discountFactor;
}
```

→ 더이상 필요하지 않은 basePrice 임시변수 선언 부분은 제거한다.

→ 다음은 discountFactor 를 리팩토링하도록 한다.

<p align="center">▼</p>

```c#
double GetPrice()
{
    double discountFactor = GetDiscountFactor();

    return GetBasePrice() * discountFactor;
}

private double GetDiscountFactor()
{
    if (GetBasePrice() > 1000)
        return 0.95;
    else
        return 0.98;
}
```

→ discountFactor도 Replace Temp With Query를 적용하여 메소드로 추출하였다.

<p align="center">▼</p>

```c#
double GetPrice()
{
    return GetBasePrice() * GetDiscountFactor();
}
```

→ 마지막으로 discountFactor 지역변수에 Inline Temp를 적용하여 위와같이 메소드를 단순화 시켰다.
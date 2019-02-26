# Introduce Explaining Variable : 복잡한 수식을 임시변수로

<p align="right">2019-02-23</p>



## 개요 및 사용 필요성

* 복잡한 수식은 눈으로 알아보기가 어렵다. 이런경우에 임시변수를 사용한다면 수식을 좀 더 다루기 쉽게 나누는데 도움이 될 것이다.
* Introduce Explaining Variable 은 특히 조건문에서 많이 쓰인다.
* 각각의 조건을 잘 설명하는 이름의 변수로 만들어서 사용한다.
* 긴 알고리즘이 있을시 각 단계의 계산결과를 잘 지어진 이름의 임시변수로 설명하도록 한다.



## 주의사항

* Introduce Explaining Variable 대신 Extract Method를 사용해야 할 경우 있다. 임시변수는 메소드 내의 컨텍스트 내에서만 쓸수 있지만, 메소드의 경우는 객체내 뿐만 아니라 다른 객체에서도 쓸 수 있기 때문이다.
* 하지만 지역변수가 엮여 있어서 Extract Method를 사용하기 어렵다면 Introduce Explaining Variable을 사용하도록 한다.



## 예시 (일반)

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    return m_quantity * m_itemPrice -
        Math.max(0, m_quantity - 500) * m_itemPrice * 0.05 +
        Math.min(m_quantity * m_itemPrice * 0.1, 100.0);
}
```

→ 먼저 quantity와 itemPrice의 곱이 basePrice라는 값을 임시변수화 시킨다.

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    final double basPrice = m_quantity * m_itemPrice;
    return basePrice -
        Math.max(0, m_quantity - 500) * m_itemPrice * 0.05 +
        Math.min(m_quantity * m_itemPrice * 0.1, 100.0);
}
```

→ basePrice 값을 return 값의 수식에도 반영한다.

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    final double basPrice = m_quantity * m_itemPrice;
    return basePrice -
        Math.max(0, m_quantity - 500) * m_itemPrice * 0.05 +
        Math.min(basPrice * 0.1, 100.0);
}
```

→ 그 다음은 return 값의 수식의 일부를 quantityDiscount라는 임시변수로 만든다.

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    final double basPrice = m_quantity * m_itemPrice;
    final double quantityDiscount = Math.max(0, m_quantity - 500) * m_itemPrice * 0.05;
    return basePrice - quantityDiscount +
        Math.min(basPrice * 0.1, 100.0);
}
```

→ 마지막으로 return 값의 min 수식을 shipping이라는 임시변수로 만든다.

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    final double basPrice = m_quantity * m_itemPrice;
    final double quantityDiscount = Math.max(0, m_quantity - 500) * m_itemPrice * 0.05;
    final double shipping = Math.min(basPrice * 0.1, 100.0);
    return basePrice - quantityDiscount + shipping;
}
```



## 예시 (Extract Method를 사용한 경우)

→ 위 예시를 Introduce Explaining Variable 대신 Extract Method로 사용해보도록 하겠다.

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    return m_quantity * m_itemPrice -
        Math.max(0, m_quantity - 500) * m_itemPrice * 0.05 +
        Math.min(m_quantity * m_itemPrice * 0.1, 100.0);
}
```

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    return basePrice() -
        Math.max(0, m_quantity - 500) * m_itemPrice * 0.05 +
        Math.min(basePrice() * 0.1, 100.0);
}

private double basePrice()
{
    return m_quantity * m_itemPrice;
}
```

<p align="center">▼</p>

```java
double price()
{
    // price = (base price) - (quantity discount) + (shipping)
    return basePrice() - quantityDiscount() + shipping();
}

private double basePrice() { return m_quantity * m_itemPrice; }
private double quantityDiscount() { 
    return Math.max(0, m_quantity - 500) * m_itemPrice * 0.05;
}
private double shipping() {
    Math.min(basePrice() * 0.1, 100.0);
}
```


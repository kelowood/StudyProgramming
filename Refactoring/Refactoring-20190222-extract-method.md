# 리팩토링 Extract Method : 코드조각을 그룹화하여 별도의 메소드로 만들기

<p align="right">2019-02-22</p>

## Extract Methods 사용 필요성

* 지나치게 <font color="#4f81bd">긴 메소드에 대한 쪼개기</font> 용도

* 메소드를 잘개 쪼갬으로써 다른 메소드에서 사용 가능성 용이

* 메소드를 나눔으로써 일련의 주석을 읽는 것 같은 효과 : 가독성 쉬워짐

* 메소드 오버라이딩 용이

  → Extract Methods 수행시 <font color="#ff0000">메소드의 이름을 잘 지어야 한다.</font> 만약 이해하기 쉬운 이름을 지을 수 없다면 코드를 뽑아내지 않는 것이 나을지도 모른다.



## 주의사항

* 메소드 이름을 정할시 <font color="red">어떻게 하는지를 나타내는 이름을 정하지 말고</font>, <font color="#4f81bd">무엇을 하는지를 나타내는 이름을 정하라</font>.

* 뽑아내고자 하는 코드 조각 내에 원래 사용하던 지역변수가 있을시에는 <font color="#4f81bd">새로운 메소드의 파라미터 혹은 지역변수로 바꾼다</font>.

* 뽑아낸 코드 조각에서 지역변수가 있고, 만약 그 <font color="red">변수가 코드 조각에서 수정되고, 코드조각 이후에 쓰이게 될시</font>에는 이 뽑아낸 코드를 질의(Query)로 봐야 한다.

	 : 질의(Query)는 쉽게 말해 하나의 리턴값(결과값)을 주는 메소드를 의미한다.

* 만약 위와 같이 값이 수정되는 지역변수가 둘 이상 존재시에는 다른 방법을 써야 한다.



## 예시

* 코드조각 내 지역변수가 없을시

``` java
void printOwig()
{
	Enumeration e = m_orders.elements();
    double outstanding = 0.0;
    
    // banner
    System.out.println("*************************");
    System.out.println("****** Custom Owes ******");
    System.out.println("*************************");
    
    // outstanding 계산
    while (e.hasMoreElements())
    {
        Order each = (Order)e.nextElement();
        outstanding += each.getAmount();
    }
    
    // 상세 정보 표시
    System.out.println("name:" + m_name);
    System.out.println("amount:" + outstanding);    
}
```

<p align="center">▼</p>

``` java
void printOwing()
{
	Enumeration e = m_orders.elements();
	double outstanding = 0.0;

	printBanner();

	// calculate outstanding
	while (e.hasMoreElements()) 
	{
		Order each = (Order)e.nextElement();
		outstanding += each.getAmount();
	}

	// print details
	System.out.println("name:" + m_name);
	System.out.println("amount:" + outstanding);
}

void printBanners()
{
	// banner
	System.out.println("*************************");
	System.out.println("***** Customer Owes *****");
	System.out.pringln("*************************");
}
```

→ 단순하게 필요한 <font color="#4f81bd">코드조각만 뽑아서 새로운 메소드로</font> 만들면 된다.

* 코드조각 내 지역변수가 있을시 (읽기전용)

```java
void printOwing()
{
	Enumeration e = m_orders.elements();
	double outstanding = 0.0;

	printBanner();

	// calculate outstanding
	while (e.hasMoreElements()) 
	{
		Order each = (Order)e.nextElement();
		outstanding += each.getAmount();
	}

	// print details
	System.out.println("name:" + m_name);
	System.out.println("amount:" + outstanding);
}
```

<p align="center">▼</p>

```java
void printOwing()
{
	Enumeration e = m_orders.elements();
	double outstanding = 0.0;

	printBanner();

	// calculate outstanding
	while (e.hasMoreElements()) 
	{
		Order each = (Order)e.nextElement();
		outstanding += each.getAmount();
	}

	printDetails(outstanding);
}

void printDetails(double _outstanding)
{
	// print details
	System.out.println("name:" + m_name);
	System.out.println("amount:" + _outstanding);
}
```

→ 단순히 코드 조각 내에서 수정하지 않고 읽기만 하는 지역변수일 경우 (수정 전의 outstanding 변수)에는 <font color="#4f81bd">새로운 메소드의 매개변수로 줌</font>으로써 해결할 수 있다.

* 코드조각 내 지역변수가 있고, 이후 바깥에서 사용할 시

```java
void printOwing()
{
	Enumeration e = m_orders.elements();
	double outstanding = 0.0;

	printBanner();

	// calculate outstanding
	while (e.hasMoreElements()) 
	{
		Order each = (Order)e.nextElement();
		outstanding += each.getAmount();
	}

	printDetails(outstanding);
}
```

<p align="center">▼</p>

```java
void printOwing()
{
	printBanner();
	double outstanding = getOutstanding();
	printDetails(outstanding);
}

double getOutstanding()
{
	Enumeration e = m_orders.elements();
	double outstanding = 0.0;

	// calculate outstanding
	while (e.hasMoreElements()) 
	{
		Order each = (Order)e.nextElement();
		outstanding += each.getAmount();
	}
	
	return outstanding;
}
```

→ 변수 e 값은 뽑아낸 코드 내에서만 사용되므로 매개변수로 사용하여도 문제가 없다.

→ outstanding의 경우에는 코드 조각 내부에서 계산이 되어 그 이후에서도 사용되기 때문에 <font color="#4f81bd">내부에서 선언되어 값을 리턴하는 형태</font>로 수정되었다.
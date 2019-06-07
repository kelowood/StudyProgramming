# Split Temporary Variable : 임시 변수를 따로따로 만들기

<p align="right">2019-06-07</p>



## 개요 및 사용 필요성

* 루프 내에 있지 않은 임시변수를 사용하고자 할때 한번 선언시 여러번 할당하여 사용하지 않고, 각각 임시 변수를 선언하여 사용하자는 것
* 만약 하나의 임시변수를 여러번 할당하여 두가지 이상의 용도에서 사용한다면 코드의 가독성이 매우 떨어지게 된다.



## 주의사항

* 루프 내에 존재하는 임시변수의 경우 루프가 돌때마다 지속적으로 갱신되어야 할 경우 해당 리팩토링을 사용하여서는 안된다.
* (java의 경우) 임시변수를 final로 선언하자.



## 예시

```java
double getDistanceTravelled(int _time)
{
    double result;
    double acc = m_primaryForce / m_mass;
    int primaryTime = Math.min(_time, m_delay);
    result = 0.5 * acc * primaryTime * primaryTime;
    int secondaryTime = _time - m_delay;
    if (secondaryTime > 0)
    {
        double primaryVel = acc * m_delay;
        acc = (m_primaryForce * m_secondaryForce) / m_mass;
        result += primaryVel * secondaryTime + 0.5 * acc * secondaryTime * secondaryTime;
    }
    return result;
}
```

→ 여기에서 문제되는 변수는 acc 변수로써 이 값은 두번 할당되고 있다.  첫번째 acc는 힘에 의한 초기 가속을 저장하고 있고, 두번째 acc는 첫번째 힘과 두번째 힘 모두에 의한 가속을 저장하고 있다. 이것을 분리해야 한다.

<p align="center">▼</p>

```java
double getDistanceTravelled(int _time)
{
    double result;
    double primaryAcc = m_primaryForce / m_mass;
    int primaryTime = Math.min(_time, m_delay);
    result = 0.5 * primaryAcc * primaryTime * primaryTime;
    int secondaryTime = _time - m_delay;
    if (secondaryTime > 0)
    {
        double primaryVel = primaryAcc * m_delay;
        double secondaryAcc = (m_primaryForce * m_secondaryForce) / m_mass;
        result += primaryVel * secondaryTime + 0.5 * secondaryAcc * secondaryTime * secondaryTime;
    }
    return result;
}
```

→ 이와 같이 기존 acc 임시 변수를 primaryAcc와 secondaryAcc로 나누어 사용하도록 수정하였다.
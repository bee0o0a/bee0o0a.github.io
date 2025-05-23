---
title: "실수의 표현에 대하여"
categories: [algorithm]
---

컴퓨터는 데이터르 2진수로 표현한다.
그럼 실수는 2진수로 어떻게 표현할까?

# 컴퓨터와 실수

컴퓨터가 다루는 기본 단위인 "이진수"는 원래 정수만 표현한다.<br/>
실수가 필요하면 이진수 개념을 "두 가지 방향으로 확장" 한다.

#### 1. 소수점(이진 소수점)의 도입

- 10진수에서 3.14처럼 ` . `을 찍듯, 이진수에서 101.101<sub>2</sub>같은 식으로 소수점을 표시한다.
- 0.5는 0.1<sub>2</sub>로 쓸수 있다.
- 하지만 0.1<sub>10</sub> 는 2진수로 표현시 0.000110011<sub>2</sub> 처럼 무한 소수가 되어버린다.

#### 2. 부동소수점(Floating-Point) 방식

- 부동소수점은 소수점의 위치(지수부)와 유효 숫자(가수부)를 따로 저장한다.
- IEEE 754 표준이 대표적임
- 예를 들어 1.25는 1.01<sub>2</sub> -> 가수부 mantissa=01, exponent = 0+bias로 저장된다.

이렇게 하면 매우 크거나 작은 수를 폭넓게 표현할 수 있으며, 소수점 위치를 동적으로 옮겨가며(부동) 근사값을 저장할 수 있다.<br/>
정리하자면, "2진수를 확장"한다는 것은 `이진 소수점`을 허용하고, `부동소수점` 방식으로 실수를 근사 표현하는 것


## 부동소수점에 대해서 이해해보자.

#### 부동 소수점은 왜 필요한가?

앞서 잠시 보았듯이 컴퓨터의 기본 숫자는 정수다.<br/>
하지만 과학 계산, 그래픽, 물리 시뮬레이션 등에서 실수(소수)의 사용은 필연적이다.<br/>
고정 소수점(fixed-point) 방식도 있지만, 이는 소수점 자릿수를 미리 정해야 해서<br/>
아주 크거나 작은 숫를 다루기 까다롭고, 사칙연산 후 오버플러우나 언더플러우 문제가 자주 발생한다.

따라서, 아래의 **"과학적 표기법"** 처럼

```math
1.23 * 10^4
```

컴퓨터용 2진수 표현방법으로 바꿔쓰자! 이게 `부동소수점 방식`이다.


#### 과학적 표기법

잠시 과학적 표기법을 다시 살펴보자.

```math
6.02 * 10^23
```

- 6.02 부분을 가수(mantissa 또는 significand)
- 10<sup>23</sup> 부분을 지수(exponent)

여기서 x10으로 표현하는것은 기수(base)가 10, 즉 10진수란 뜻이다.

컴퓨터는 2진수이기에, 기수는 2

```math
1.1011_2 * 2^3
```

위와 같은 구조로 접근할 수 있다.


#### IEEE 754 표준 (가장 많이 쓰이는 규칙)

실제 컴퓨터는 "32비트(float)"나 "64비트(double)"을 사용하여 실수를 저장한다.

- float(단정도, 32비트)
    - 부호(bit 1개) + 지수(8비트) + 가수(23비트)
- double (배정도, 64비트)
    - 부호(bit 1개) + 지수(11비트) + 가수(52비트)

표현 범위가 double형이 더 넓기에, 좀더 적은 오차를 보장한다.<br/>
여기서 `부호`비트는 0이면 양수, 1이면 음수이며 가장 왼쪽 1비트만 사용한다.

`지수`(Exponent)는 실제 지수값에 bias 라는 숫자를 더해서 저장한다.<br/>
float bias = 127, double bias = 1023

예를 들어, 실제 지수 3을 저장하려면
- float: 3 + 127 = 130(1000010<sub>2</sub>)
- double: 3 + 1023 = 1026(10000000010<sub>2</sub>)

`가수`(Mantissa)는 소수부분을 비트로 저장한다.
예를들어 1.1011<sub>2</sub> -> 1011만 저장(나머지 공간은 0으로 채움)
* 1.xxx에서 `1.`dmf 저장하지 않는 이유는 항상 1로 시작한다는 규칙(정규화)이 있기 떄문

**저장 예시**

1.25를 float로 저장한다고 가정해보자. <br/>
1.25<sub>10</sub>를 이진 소수로 바꾸면 1.01<sub>2</sub>다.

1. 부호(sign) = 0 (양수)
2. 가수: 1.01<sub>2</sub>에서 .01 저장. 나머지는 0으로 채움 -> `01000000000000000000000`(23비트)
3. 지수: 현재 값의 지수부는 1.01<sub>2</sub> x 2<sup>0</sup>이기에 저장값은 0 + 127 = 127 -> 01111111<sub>2</sub>
4. 최종적으로 비트를 합친다면
```css
[ sign | exponent      | mantissa              ]
   0      01111111       01000000000000000000000
```
이 된다.


#### 장점이 있으면 한계도 있는법

우선 컴퓨터로 실수를 표현하는것은 무.조.건 오차가 발생한다고 보면 된다.
이를 오차를 어느정도까지 예상할수 있게 위 방식을 사용하는것 뿐이라 생각한다.
그럼 어떤 장 단점이 있을까?

[장점]
- 매우 큰 수(10<sup>300</sup>)와 매우 작은 수(10<sup>-300</sup>) 모두 표현이 가능하다.
- 자동으로 소수점 위치(부동) 이동하기에 오버플로우 / 언더플로우가 완화 된다.

[장점]
- 그럼에도 정밀도에 제한이 있다
    - float는 약 7자리, double은 약 16자리 십진수 정밀도를 가짐
- 반올림 오차가 발생한다.
    - 0.1<sub>10</sub>처럼 이진으로 딱 떨어지지 않는 수는 근사값만 저장한다.
    - 간단하게 컴퓨터로 0.1 + 0.1 + 0.1 은 0.3과 같은지를 비교만 해봐도 false가 나온다.. ㅎ


## 정리

1. 부동소수점은 이진법을 과학적 표기법으로 표현한 것이다.
2. 부호 / 지수 / 가수 총 세 부분으로 나눠 저장한다.
3. bias를 사용하여 지수를 음수, 양수 모두 표현한다.
4. 큰 수와 작은수 표현이 가능하지만, 정밀도의 제한이 있으며 근사 오차가 존재한다.
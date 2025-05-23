---
title: "포인터 겉핥기.."
categories: [cpp]
---

# 포인터를 살짝 핥아보자..
알고리즘을 공부하기 위해 C++을 공부하고있다.
내가 사용할 수 있는 언어는 Javascript...
공부해야 하는 언어는 Java지만, 알고리즘을 위한 언어로 `C++`을 선택했다.

백엔드를 희망하며 Java를 사용할거면서, 굳이 C++?
이유는 메모리를 좀더 이해하고싶다.
메모리를 고민하며 코드를 작성하는 가장 좋은 공부가 알고리즘이라고 생각한다.

그런데, 기초부터 막힌다 ㅎㅎ.. 모르는 개념을 공부할때 마다 글로 정리해두려고 한다.

이번 글은 `포인터`


## 우선.. 변수란?
```cpp
int x = 10;
```

여기서 `x`는 숫자 10을 담는 상자.
이 상자는 메모리(컴퓨터의 저장공간) 어딘가에 `주소`를 가지고 있음.

## 포인터에서 '&'의 의미
`&`는 "주소"를 뜻한다.
즉, 변수 x를 `&x`로 접근하면 변수 x가 메모리 어디에 있는지
주소를 알려준다.

```cpp
int x = 10;
std::cout << &x << std::endl; // x의 주소(예: 0x7ffee57a99cc)
```

## 포인터 변수의 의미
*포인터*는 "주소(메모리의 위치)를 저장하는 변수"
`*`를 변수 선언 앞에 붙이면, "나는 주소(포인터)를 저장할 거야"라는 뜻

```cpp
int x = 10; // 10이라는 값이 저장된 상자 x
int* p = &x; // x의 주소를 p에 저장 (p는 x를 가리킴)
```

- `int* p`는 "나는 Int형 변수의 주소를 저장할 수 있는 포인터"라는 뜻
- `p = &x`는 "p에 x의 주소를 저장"

위의 코드를 값으로 정리하면
`x`의 값은 `10` (실제 값)
`&x`의 값은 `0x7ffee99c` (x의 메모리 주소)
`p`의 값은 `0x7ffee99c` (x의 주소를 저장)
`*p`의 값은 `10` (p가 가리키는 곳의 실제 값)

## " *p "의 의미 (역참조)
`*p`는 "p가 가르키는 곳에 저장 된 값"을 의미.
즉, p의 주소에 가서 그 상자(x)에 든 값을 읽어와라는 의미.

```cpp
std::cout << *p << std::endl; // 10 (x의 값)
```

## 이런걸 어따써?
이런 생각으로 포인터 예제를 몇개 찾아봄.

### 포인터를 이용하여 변수 값 바꾸기
```cpp
int x = 10;
int* p = &x;

*x = 20; // Error! x에는 *을 못 붙임.
*p = 20; // p가 가르키는 x에 20을 대입
```

`*p = 20;` -> p가 가르키는 x에 20을 할당함.
즉, p로 x를 조종할 수 있음.

위 예제를 보면... x를 직접 바꾸면 되지 뭐하러 조종함?
이런 생각이 들었음.. 아래를 봐보자.

```cpp
void foo(int x) {
    x = 100;
}

int a = 10;
foo(a);
std::cout << a << std::endl;
```
위 코드는 실행시 `10`이 출력
a의 값이 바뀌지 않음

```cpp
void foo (int* px) {
    *px = 100;
}

int a = 10;
foo(&a);
std::cout << a << std::endl;
```
위 코드는 싱행하면? 포인터 변수를 이용하여 실제 a값을 변경하기때문에
10에서 100으로 변경되어있음.

즉, 함수의 인자로 전달된 변수는 함수 내에서 복사되어 원본값에 영향을 주지 않지만 포인터 변수로 전달되면 원본 변수의 주소값을 알기에 해당 주소로 직접 접근하여 값을 변경할 수 있다.

`c++에서는 참조전달을 통하여 더 편하게 전달 가능함`

### 배열의 포인터주소값은?

배열은 이름 자체가 배열의 첫 번째 요소의 주소와 같음

```cpp
int main() {
    int arr[5] = {1,2,3,4,5};
    int* p = arr;  // 배열의 첫번째 요소의 주소 즉 &arr[0]을 가짐

    std::cout << *p << std::endl; // 1

    for(int i=0; i<5; i++) {
        std::cout << *(p + i) << " "; // 1 2 3 4 5
    }
}
```

배열은 연속된 주소에 공간을 할당받기에
첫 요소의 주소값에 1씩 더하여 다음 요소를 탐색할 수 있음.

### 이중 포인터(포인터를 가리키는 포인터)

변수의 주소를 가진 포인터 변수의 주소를 저장하고 싶을 때 사용
-> 간접 참조나 복잡한 함수 전달 등

```cpp
int main() {
    int a = 7;
    int* p = &a;
    int** pp = &p; // pp 는 p의 주소를 저장

    std::cout << a << std::endl; // a의 값인 7
    std::cout << p << std::endl; // a의 주소값
    std::cout << *p << std::endl; // a의 값인 7
    std::cout << &p << std::endl; // p의 주소값
    std::cout << pp << std::endl; // p의 주소값
    std::cout << *pp << std::endl; // p의 값인 a주소값
    std::cout << **pp << std::endl; // a의 값인 7
}
```

이렇게 다양한 포인터 값을 사용할 수 있다.
이번엔 아주 얕은부분만을 핥아보았고,
다음엔 좀더 딥한 곳을 알아봐야겠다.